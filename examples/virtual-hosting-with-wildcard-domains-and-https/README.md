# Example of virtual hosting with reverse proxy

Given:

- **Service 1**. It's a nginx server that responds with index.html containing `h1 Service 1`)
- **Service 2**. It's a nginx server that responds with index.html containing `h1 Service 2`)
- **Reverse Proxy**. It is a nginx server that handles/resolves different domains: `service1.local`, `service2.local` and wildcard `*.service2.local`.
Reverse proxy routes input requests based on `Host` http-header and forwards it to appropriate `ServiceN`. In case of wildcard domain, e.g. request like `abc.service2.local` will be handled by separate match block and appropriate response will be produced.

## Run
```bash
# go to current directory before running following command from outside
docker compose up
```

## Troubleshooting


For requesting reverse-proxy by domain names, first of all, appropriate DNS server or configured /etc/hosts is required.
DNS server is preferred, since it just requires us to add `A` DNS record to link domains with IP addresses and that's it.
In case of `/etc/hosts`, wildcard domains won't be resolved correctly, so it will require us to unique records per domain.
Another option to trigger reverse-proxy matcher is to make a request by IP address and set `Host` header equal to desired domain name.
Like so:
```bash
curl --header "Host: abc.service2.local" http://127.0.0.1
```
Requesting reverse-proxy by only IP address without `Host` header will cause a message – "IP is not supported.".

### Example of configured /etc/hosts

```shell
127.0.0.1 service1.local
127.0.0.1 service2.local
127.0.0.1 abc.service2.local
```

## Prepare Self-signed certificate

```shell
# create private key
wsl openssl genrsa -des3 -out server.key 1024

# create certificate signing request (csr)
wsl openssl req -new -key server.key -out server.csr

# drop pass requirement
wsl cp server.key server.key.org
wsl openssl rsa -in server.key.org -out server.key

# sign certificate (separately customize v3.ext file)
wsl openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt -extfile v3.ext

# install certificate to Trusted Root Certification Authorities Store on local machine
```
