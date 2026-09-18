# UD10 — Consegna laboratorio guidato

## Preflight

- `docker version`:
```
Client: Docker Engine - Community
 Version:           29.8.1
 API version:       1.56
 Go version:        go1.27.1
 Git commit:        4a63305d74
 Built:             Tue Sep 15 11:21:13 2026
 OS/Arch:           darwin/arm64
 Context:           desktop-linux

Server: Docker Desktop 4.91.0 (239619)
 Engine:
  Version:          29.8.0
  API version:      1.56 (minimum version 1.40)
  Go version:       go1.26.8
  Git commit:       3ce5872
  Built:            Thu Sep  3 21:49:37 2026
  OS/Arch:          linux/arm64
  Experimental:     false
 containerd:
  Version:          v2.3.4
  GitCommit:        db8809540e1a7a9da5d518876894933ff55692ab
 runc:
  Version:          1.4.3
  GitCommit:        v1.4.3-0-gbb14dabe
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```
- `docker compose version`:
```
Docker Compose version v5.5.1
```
- daemon: Raggiungibile
- WSL2: Mi trovo su macOS
- RESULT: PASS

## Build backend

- image: catalog-backend
- tag: ud10
- build context: .
- Dockerfile:
```dockerfile
FROM python:3.13-slim

WORKDIR /app

COPY backend/server.py /app/server.py

RUN useradd --create-home --uid 10001 appuser \
    && mkdir -p /runtime \
    && chown -R appuser:appuser /app /runtime

USER appuser

ENV APP_HOST=0.0.0.0 \
    APP_PORT=8000 \
    LOW_STOCK_THRESHOLD=5 \
    RUNTIME_DIR=/runtime

EXPOSE 8000

CMD ["python", "/app/server.py"]
```
- layer/history osservati:
```
IMAGE          CREATED              CREATED BY                                      SIZE      COMMENT
ffc8c521b3d2   About a minute ago   CMD ["python" "/app/server.py"]                 0B        buildkit.dockerfile.v0
<missing>      About a minute ago   EXPOSE [8000/tcp]                               0B        buildkit.dockerfile.v0
<missing>      About a minute ago   ENV APP_HOST=0.0.0.0 APP_PORT=8000 LOW_STOCK…   0B        buildkit.dockerfile.v0
<missing>      About a minute ago   USER appuser                                    0B        buildkit.dockerfile.v0
<missing>      About a minute ago   RUN /bin/sh -c useradd --create-home --uid 1…   81.9kB    buildkit.dockerfile.v0
<missing>      About a minute ago   COPY backend/server.py /app/server.py # buil…   12.3kB    buildkit.dockerfile.v0
<missing>      About a minute ago   WORKDIR /app                                    8.19kB    buildkit.dockerfile.v0
<missing>      2 weeks ago          CMD ["python3"]                                 0B        buildkit.dockerfile.v0
<missing>      2 weeks ago          RUN /bin/sh -c set -eux;  for src in idle3 p…   16.4kB    buildkit.dockerfile.v0
<missing>      2 weeks ago          RUN /bin/sh -c set -eux;   savedAptMark="$(a…   43.7MB    buildkit.dockerfile.v0
<missing>      2 weeks ago          ENV PYTHON_SHA256=1e66a7945a48390ee4c2a4268a…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago          ENV PYTHON_VERSION=3.13.15                      0B        buildkit.dockerfile.v0
<missing>      2 weeks ago          ENV GPG_KEY=7169605F62C751356D054A26A821E680…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago          RUN /bin/sh -c set -eux;  apt-get update;  a…   13.1MB    buildkit.dockerfile.v0
<missing>      2 weeks ago          ENV PATH=/usr/local/bin:/usr/local/sbin:/usr…   0B        buildkit.dockerfile.v0
<missing>      3 weeks ago          # debian.sh --arch 'arm64' out/ 'trixie' '@1…   109MB     debuerreotype 0.17
```

## Container singolo

- nome: catalog-backend-ud10
- porta: 127.0.0.1:8000->8000/tcp
- health: 200 OK
```json
{
  "status": "ok",
  "service": "catalog-backend",
  "version": "2.0"
}
```
- products: 
```json
{
    "count": 4,
    "products": [
        {
            "id": "P001",
            "name": "Notebook Pro 14",
            "category": "Notebook",
            "price": 1299.0,
            "stock": 8,
            "stock_status": "OK"
        },
        {
            "id": "P002",
            "name": "Monitor 27 UHD",
            "category": "Monitor",
            "price": 349.0,
            "stock": 4,
            "stock_status": "LOW"
        },
        {
            "id": "P003",
            "name": "Dock USB-C",
            "category": "Accessori",
            "price": 119.0,
            "stock": 15,
            "stock_status": "OK"
        },
        {
            "id": "P004",
            "name": "Keyboard Business",
            "category": "Accessori",
            "price": 59.0,
            "stock": 2,
            "stock_status": "LOW"
        }
    ]
}
```
- log: 
```
Catalog backend listening on http://0.0.0.0:8000 threshold=5 runtime=/runtime
[backend] 172.17.0.1 - "GET /health HTTP/1.1" 200 -
[backend] 172.17.0.1 - "GET /api/products HTTP/1.1" 200 -
```
- environment verificato:
```sh
LOW_STOCK_THRESHOLD=5
APP_PORT=8000
RUNTIME_DIR=/runtime
```

## Compose

- backend:
```yaml
backend:
    build:
      context: .
      dockerfile: docker/backend.Dockerfile
    image: catalog-backend:ud10
    environment:
      APP_PORT: "8000"
      LOW_STOCK_THRESHOLD: "5"
      RUNTIME_DIR: "/runtime"
    volumes:
      - catalog-runtime:/runtime
    expose:
      - "8000"
    networks:
      - catalog-net
    healthcheck:
      test:
        [
          "CMD",
          "python",
          "-c",
          "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/health', timeout=2)"
        ]
      interval: 5s
      timeout: 3s
      retries: 10
      start_period: 3s
```
- frontend:
```yaml
frontend:
    build:
      context: .
      dockerfile: docker/frontend.Dockerfile
    image: catalog-frontend:ud10
    ports:
      - "127.0.0.1:8080:80"
    depends_on:
      backend:
        condition: service_healthy
    networks:
      - catalog-net
```
- rete:
```yaml
networks:
  catalog-net:
    driver: bridge
```
- volume:
```
volumes:
  catalog-runtime:
```
- frontend URL: Raggiungibile
- backend healthy: `[backend] 127.0.0.1 - "GET /health HTTP/1.1" 200`
- `/health`: 200 OK
```json
{
  "status": "ok",
  "service": "catalog-backend",
  "version": "2.0"
}
```
- `/api/products`:
```json
{
    "count": 4,
    "products": [
        {
            "id": "P001",
            "name": "Notebook Pro 14",
            "category": "Notebook",
            "price": 1299.0,
            "stock": 8,
            "stock_status": "OK"
        },
        {
            "id": "P002",
            "name": "Monitor 27 UHD",
            "category": "Monitor",
            "price": 349.0,
            "stock": 4,
            "stock_status": "LOW"
        },
        {
            "id": "P003",
            "name": "Dock USB-C",
            "category": "Accessori",
            "price": 119.0,
            "stock": 15,
            "stock_status": "OK"
        },
        {
            "id": "P004",
            "name": "Keyboard Business",
            "category": "Accessori",
            "price": 59.0,
            "stock": 2,
            "stock_status": "LOW"
        }
    ]
}
```
- P001: 200 OK
```json
{
  "id": "P001",
  "name": "Notebook Pro 14",
  "category": "Notebook",
  "price": 1299.0,
  "stock": 8,
  "stock_status": "OK"
}
```
- XXX: 404 Not Found
```json
{
  "error": "product_not_found",
  "product_id": "XXX"
}
```

## Networking

- `backend` risolto dal frontend: `172.18.0.2`
- `localhost:8000` dal frontend: `wget: can't connect to remote host: Connection refused`
- spiegazione: `localhost` all'interno del frontend indica la rete privata interna, questo rende il backend irraggiungibile

## Environment

- threshold iniziale: 5
- threshold temporaneo: 10
- effetto osservato: Cambiando il threshold, anche un prodotto con stock 8 risulta `LOW`.
- valore ripristinato: 5

## Volume

- counter before: 3
- counter after recreate: 4
- persistenza PASS/FAIL: PASS
- volume: catalog-runtime

## Lifecycle

- restart test: PASS
- log backend:
```
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 172.18.0.3 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
backend-1  | [backend] 127.0.0.1 - "GET /health HTTP/1.1" 200 -
```
- log frontend:
```
frontend-1  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
frontend-1  | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
frontend-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
frontend-1  | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
frontend-1  | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
frontend-1  | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
frontend-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
frontend-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
frontend-1  | /docker-entrypoint.sh: Configuration complete; ready for start up
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: using the "epoll" event method
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: nginx/1.31.6
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: built by gcc 15.2.0 (Alpine 15.2.0)
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: OS: Linux 7.0.12-linuxkit
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker processes
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 29
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 30
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 31
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 32
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 33
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 34
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 35
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 36
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 37
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 38
frontend-1  | 2026/09/18 12:07:59 [notice] 1#1: start worker process 39
frontend-1  | 172.18.0.1 - - [18/Sep/2026:12:08:09 +0000] "GET /api/counter HTTP/1.1" 200 18 "-" "curl/8.7.1" "-"
frontend-1  | 172.18.0.1 - - [18/Sep/2026:12:17:36 +0000] "GET /health HTTP/1.1" 200 72 "-" "curl/8.7.1" "-"
```

## Git

- commit: Ho effettuato il commit in preconsegna
- push/PR: 
