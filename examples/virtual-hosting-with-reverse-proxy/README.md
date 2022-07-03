# Example of virtual hosting with reverse proxy

Given:

- **Service 1**. It's a nginx server that responds with index.html containing `h1 Service 1`)
- **Service 2**. It's a nginx server that responds with index.html containing `h1 Service 2`)
- **Reverse Proxy**. It is a nginx server that handles/resolves two different domains: `service1.local` and `service2.local`.
  Reverse proxy routes input requests based on `Host` http-header and forwards it to appropriate `ServiceN`.

## Run
```bash
# go to current directory before running following command from outside
docker compose up
```

