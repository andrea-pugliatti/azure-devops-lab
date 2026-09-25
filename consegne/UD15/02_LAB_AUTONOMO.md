# UD15 — Consegna LAB autonomo

- branch errore: fix/ud15-target-port
- target port errato: 9999
- differenza nel Terraform plan: 
```
Plan: 0 to add, 1 to change, 0 to destroy.
```
- IaC: PASS
```
Plan: 0 to add, 1 to change, 0 to destroy.
```
- Test: PASS
```
test_health_status (test_backend.CatalogTests.test_health_status) ... ok
test_health_version (test_backend.CatalogTests.test_health_version) ... ok
test_product_count (test_backend.CatalogTests.test_product_count) ... ok

----------------------------------------------------------------------
Ran 3 tests in 0.000s

OK
```
- BuildPush: PASS
```
Result
--------
32
33
31
29
28
```
- Deploy: PASS
```
Apply complete! Resources: 0 added, 1 changed, 0 destroyed.
```
- Smoke: FAIL
- evidenza ingress:
```json
{
  "External": true,
  "FQDN": "catalog-api-ud15.graydesert-78eefa91.italynorth.azurecontainerapps.io",
  "TargetPort": 9999
}
```
- evidenza log:
La nuova revision è in uno stato Activation failed
- causa: Mismatch tra porta target e la porta in cui l'applicazione all'interno del container è in ascolto.
- correzione minima: Ripristinare la targetPort corretta
- branch correzione: fix/ud15-target-port-correct
- run finale: PASS
Build ID: 39
- smoke finale: PASS
```json
{
    "status": "ok",
    "service": "catalog-backend",
    "version": "39"
}
```
