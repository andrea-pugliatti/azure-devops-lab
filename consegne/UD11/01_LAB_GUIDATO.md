# UD11 — Consegna laboratorio guidato

## Preflight

- Azure: PASS
- Docker: PASS
- containerapp extension: Installata
- Microsoft.App: Inizialmente NotRegistered, ho usato il comando `az provider register` per registrare
- Microsoft.OperationalInsights: Registered

Ho dovuto registrare anche Microsoft.ContainerRegistry

## ACR

- nome: acr1789978925ud11
- SKU: Basic
- login server: acr1789978925ud11.azurecr.io
- repository: catalog-backend
- tag v1: v1
- tag v2: v2
- admin user abilitato: NO

## v1

- local test:
```json
{
    "status": "ok",
    "service": "catalog-backend",
    "version": "v1"
}
```
- push: 
```
The push refers to repository [acr1789978925ud11.azurecr.io/catalog-backend]
89deb3477321: Pushed
44136fa355b3: Pushed
123abebbace7: Pushed
bd36565c0fde: Pushed
4b83fbae47dd: Pushed
0fb3db4aef9a: Pushed
29d65122b9d0: Pushed
c6b4110ad8ef: Pushed
9c481c0ff72a: Pushed
v1: digest: sha256:db12b8b31e26de0222884c44f973df704f1d484940fe5bc745ad26e297dee9a6 size: 856
```
- ACR tag: v1
- ACA environment: acaenv-ud11
- Container App: catalog-api-ud11
- managed identity: SystemAssigned
- registry identity: system
- ingress: External = true
- target port: 8000
- FQDN: catalog-api-ud11.blackwave-87109f71.italynorth.azurecontainerapps.io
- health: 200 OK
```
HTTP/2 200
server: CatalogBackend/3.0 Python/3.13.15
date: Mon, 21 Sep 2026 10:15:09 GMT
content-type: application/json; charset=utf-8
content-length: 71

{
  "status": "ok",
  "service": "catalog-backend",
  "version": "v1"
}
```
- version: v1
- revision: catalog-api-ud11--v1
- logs:
`{"TimeStamp": "2026-09-21T10:11:03.2065421+00:00", "Log": "F Catalog backend v1 listening on http://0.0.0.0:8000 threshold=5"}`

Nota: essendo su mac, fare la build in locale normalmente crea un'immagine arm64. Ho usato il comando `docker buildx build --platform linux/amd64 -t acr1789978925ud11.azurecr.io/catalog-backend:v1 --push .` per effettuare la build corretta e pushare immediatamente l'immagine su ACR.

## v2

- Dockerfile version: v2
- local test: 
```json
{
    "status": "ok",
    "service": "catalog-backend",
    "version": "v2"
}
```
- push: 
```
The push refers to repository [acr1789978925ud11.azurecr.io/catalog-backend]
206608d6f1c3: Pushed
44136fa355b3: Already exists
123abebbace7: Layer already exists
bd36565c0fde: Layer already exists
4b83fbae47dd: Layer already exists
0fb3db4aef9a: Layer already exists
29d65122b9d0: Layer already exists
c6b4110ad8ef: Layer already exists
9c481c0ff72a: Layer already exists
v2: digest: sha256:0b95ae4fb7834e0676c134bf4e76ad2cf0ef2653ed8f455ddaef88236d63e47a size: 856
```
- ACR tag: v2 (il tag docker è ud11-v2)
- update: Succeeded
```
Name                  Active    Health    Created
--------------------  --------  --------  -------------------------
catalog-api-ud11--v1  True      Healthy   2026-09-21T10:10:46+00:00
catalog-api-ud11--v2  True      None      2026-09-21T10:26:57+00:00
```
- revision: catalog-api-ud11--v2
- health:
```json
{
    "status": "ok",
    "service": "catalog-backend",
    "version": "v2"
}
```
- version: v2

Nota: Anche qui ho utilizzato il comando `docker buildx build --platform linux/amd64 -t "$IMAGE_V2" --push .` per fare il push della build corretta.

## Scaling

- minReplicas: 0
- maxReplicas: 1
- scale-to-zero possibile: sì
