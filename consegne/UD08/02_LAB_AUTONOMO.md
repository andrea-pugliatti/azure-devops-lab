# UD08 — Consegna laboratorio autonomo

## Baseline

- health:
```sh
HTTP/1.0 200 OK
Server: CatalogoProdotti/1.0 Python/3.13.15
Date: Wed, 16 Sep 2026 10:57:28 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 74

{
  "status": "ok",
  "service": "catalogo-prodotti",
  "version": "1.0"
}
```
- products:
```sh
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

## Errore introdotto

- configurazione modificata: "api_prefix"
- endpoint funzionante: `/health`, `api-v2/products`
- endpoint non funzionante: `api/products`
- comportamento frontend: Il frontend segnala errore.

## Diagnosi

1. Sì, il backend è avviato.
2. `/health` funziona e ritorna 200 OK
3. `api-v2/products`
4. `api/products`
5. configurazione incoerente

## Fix

- causa: "api_prefix" configurato male
- fix: sostituire "api_prefix": "/api-v2" con "api_prefix": "/api" 
- environment aggiunto: "environment": "local"

## Test finali

- `/health`: 200 OK
- `/api/products`: 200 OK
- `/api/products/P001`: 200 OK
- `/api/products/XXX`: 404 Not Found
- browser: Funziona, 4 prodotti caricati

## Git

- branch: fix/ud08-api-prefix
- `git diff` verificato: Compare solo la modifica al config.json
- commit: 9e2489a fix: restore API contract and mark local environment
- push: effettuato con `git push -u origin fix/ud08-api-prefix`
- PR: 2 URL: https://github.com/andrea-pugliatti/azure-devops-lab/pull/2
- `gh pr diff` verificato:
```
diff --git a/app/catalogo-prodotti/config.json b/app/catalogo-prodotti/config.json
index ab200c3..cd92a83 100644
--- a/app/catalogo-prodotti/config.json
+++ b/app/catalogo-prodotti/config.json
@@ -2,5 +2,6 @@
   "host": "127.0.0.1",
   "port": 8000,
   "api_prefix": "/api",
-  "low_stock_threshold": 5
+  "low_stock_threshold": 5,
+  "environment": "local"
 }
```
- merge: effettuato tramite il comando:
```sh
gh pr merge \
  --squash \
  --delete-branch
```
- main sincronizzata: Sì
```
Already on 'main'
Your branch is up to date with 'origin/main'.
Already up to date.
```

## Distinzione

- review collaborativa: La prima PR che abbiamo eseguito. Viene richiesta la revisione formale del lavoro. Chi fa la code review valida la soluzione, richiede modifiche, commenta o approva il cambiamento.
- auto-verifica PR individuale: La seconda PR che abbiamo eseguito. Viene effettuata dall'autore stesso della Pull Request e serve a eliminare eventuali refusi.
