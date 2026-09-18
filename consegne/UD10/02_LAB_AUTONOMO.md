# UD10 — Consegna laboratorio autonomo

## Baseline

- compose ps:
```
NAME                           IMAGE                   COMMAND                  SERVICE    CREATED          STATUS                    PORTS
catalogo-prodotti-backend-1    catalog-backend:ud10    "python /app/server.…"   backend    17 minutes ago   Up 14 minutes (healthy)   8000/tcp
catalogo-prodotti-frontend-1   catalog-frontend:ud10   "/docker-entrypoint.…"   frontend   17 minutes ago   Up 17 minutes             127.0.0.1:8080->80/tcp
```
- health: 200 OK

## Errore

- variabile: LOW_STOCK_THRESHOLD
- valore errato: not-a-number
- stato backend: Exited (1)
- log/errore:
```
backend-1  | Traceback (most recent call last):
backend-1  |   File "/app/server.py", line 21, in read_int_env
backend-1  |     return int(raw)
backend-1  | ValueError: invalid literal for int() with base 10: 'not-a-number'
backend-1  |
backend-1  | The above exception was the direct cause of the following exception:
backend-1  |
backend-1  | Traceback (most recent call last):
backend-1  |   File "/app/server.py", line 28, in <module>
backend-1  |     LOW_STOCK_THRESHOLD = read_int_env("LOW_STOCK_THRESHOLD", 5)
backend-1  |   File "/app/server.py", line 23, in read_int_env
backend-1  |     raise ValueError(f"{name} deve essere un intero, ricevuto: {raw!r}") from exc
backend-1  | ValueError: LOW_STOCK_THRESHOLD deve essere un intero, ricevuto: 'not-a-number'
```

## Diagnosi

- Sintomo: Il container backend è sullo stato `Exited`, non sta girando.
- Risultato atteso: Lo stato dovrebbe essere `Up`.
- Evidenza: Confermato utilizzando il comando `docker compose ps -a`.
- Ipotesi: Configurazione errata
- Causa: Errore `ValueError: LOW_STOCK_THRESHOLD deve essere un intero, ricevuto: 'not-a-number'`

## Fix

- modifica minima: Modificare la variabile di ambiente LOW_STOCK_THRESHOLD
- rebuild necessario?: No
- motivazione: Non è necessario il rebuild dell'immagine perché non è stato modificato né il codice sorgente né il Dockerfile. Per un cambio della configurazione di compose.yaml è necessario solo reinstanziare il container.

## Test

- backend healthy: sì
- health: 200 OK
- products: 4 prodotti
- browser: Raggiungibile, 4 prodotti

## Modifica conservata

- APP_ENV: "local-docker"
- compose config: 
```yaml
name: catalogo-prodotti
services:
  backend:
    build:
      context: /Users/dekine/Documents/Code/azure-devops-lab/app/catalogo-prodotti
      dockerfile: docker/backend.Dockerfile
    environment:
      APP_ENV: local-docker
      APP_PORT: "8000"
      LOW_STOCK_THRESHOLD: "5"
      RUNTIME_DIR: /runtime
    expose:
      - "8000"
    healthcheck:
      test:
        - CMD
        - python
        - -c
        - import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/health',
          timeout=2)
      timeout: 3s
      interval: 5s
      retries: 10
      start_period: 3s
    image: catalog-backend:ud10
    networks:
      catalog-net: null
    volumes:
      - type: volume
        source: catalog-runtime
        target: /runtime
        volume: {}
  frontend:
    build:
      context: /Users/dekine/Documents/Code/azure-devops-lab/app/catalogo-prodotti
      dockerfile: docker/frontend.Dockerfile
    depends_on:
      backend:
        condition: service_healthy
        required: true
    image: catalog-frontend:ud10
    networks:
      catalog-net: null
    ports:
      - mode: ingress
        host_ip: 127.0.0.1
        target: 80
        published: "8080"
        protocol: tcp
networks:
  catalog-net:
    name: catalogo-prodotti_catalog-net
    driver: bridge
volumes:
  catalog-runtime:
    name: catalogo-prodotti_catalog-runtime
```
- test: 200 OK

## Git

- branch: fix/ud10-invalid-threshold
- commit: 2aeb08c "fix: validate Docker runtime configuration"
- PR: https://github.com/andrea-pugliatti/azure-devops-lab/pull/3
- diff verificato: sì
- merge: Squashed and merged pull request andrea-pugliatti/azure-devops-lab#3 (UD10: validate Docker runtime configuration)


## Passaggio alla verifica

- stack mantenuto attivo: sì
