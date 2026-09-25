# UD15 — Consegna LAB guidato

## Controlli iniziali
- ACR: `acrud131546xe5qaf4ai56`
- `LegacyRegistryPermissions`: RoleAssignmentMode = LegacyRegistryPermissions
- `sc-azure-ud13-15`: Presente
- `AcrPush`: Role assegnato all'identità della pipeline
- Storage state: storage account creato `sttf15756eec566e9c46a4`
- container state: storage container creato `tfstate`
- `Storage Blob Data Contributor`: Assegnato all'identità della pipeline
- managed identity: Managed identity creata `id-ud15-acrpull`
- `AcrPull`: Role assegnato alla Managed identity `id-ud15-acrpull`
- Agent: Online

## Terraform
- init: Terraform has been successfully initialized!
- validate: Success! The configuration is valid.
- plan iniziale: Plan: 2 to add, 0 to change, 0 to destroy.
- apply: Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
- state remoto: ud15.tfstate presente in tfstate

Nota: ho dovuto modificare `azure-pipelines-delivery.yml`, Il comando docker build di base crea l'immagine solo per l'architettura locale. Ho dovuto forzare l'architettura linux/amd64.

## Pipeline
- Build ID: 27
- IaC: PASS
```
Terraform has been successfully initialized!
Success! The configuration is valid.
Plan: 2 to add, 0 to change, 0 to destroy.
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
Docker version 29.8.1, build 4a63305d74
Login Succeeded
#0 building with "desktop-linux" instance using docker driver

#1 [internal] load build definition from Dockerfile
#1 transferring dockerfile: 319B done
#1 DONE 0.0s

#2 [internal] load metadata for docker.io/library/python:3.13-slim
#2 ...

#3 [auth] library/python:pull token for registry-1.docker.io
#3 DONE 0.0s

#2 [internal] load metadata for docker.io/library/python:3.13-slim
#2 DONE 1.0s

#4 [internal] load .dockerignore
#4 transferring context: 75B done
#4 DONE 0.0s

#5 [1/4] FROM docker.io/library/python:3.13-slim@sha256:8d9d0b8bcf6506481eae4907c18f5e3e7902e629f5f6d684f9e7c32e85e3ddf0
#5 resolve docker.io/library/python:3.13-slim@sha256:8d9d0b8bcf6506481eae4907c18f5e3e7902e629f5f6d684f9e7c32e85e3ddf0 done
#5 DONE 0.0s

#6 [internal] load build context
#6 transferring context: 1.87kB done
#6 DONE 0.0s

#7 [2/4] WORKDIR /app
#7 CACHED

#8 [3/4] COPY server.py /app/server.py
#8 CACHED

#9 [4/4] RUN useradd --create-home --uid 10001 appuser     && chown -R appuser:appuser /app
#9 CACHED

#10 exporting to image
#10 exporting layers done
#10 exporting manifest sha256:f2976c159684e9f84beff2545b2dbd762667f39d56e1425667897ec3e863682a done
#10 exporting config sha256:914233ab03882c1417409b98fbb683967898650c23e20a13fdee25433c261ae4 done
#10 exporting attestation manifest sha256:94d96e5a247b8b43c6fbf0d47b1965eb09391f4a1e0e541654d056819fa4177e done
#10 exporting manifest list sha256:427ef8de9fcc023d5a241afbca4a9bc1724b13fc0ba76037c2f55b6ddfc801fe done
#10 naming to acrud131546xe5qaf4ai56.azurecr.io/catalog-backend:27 done
#10 DONE 0.0s
The push refers to repository [acrud131546xe5qaf4ai56.azurecr.io/catalog-backend]
7827c8b81cc0: Waiting
44136fa355b3: Waiting
3d9fb7471420: Waiting
6b37362b3da7: Waiting
264ba3d8ae19: Waiting
08b98075eb39: Waiting
4b4a95e0b833: Waiting
cd2670f00dbb: Waiting
7827c8b81cc0: Waiting
3d9fb7471420: Layer already exists
6b37362b3da7: Layer already exists
264ba3d8ae19: Layer already exists
4a43a40b039e: Layer already exists
08b98075eb39: Layer already exists
4b4a95e0b833: Layer already exists
cd2670f00dbb: Layer already exists
7827c8b81cc0: Pushed
27: digest: sha256:427ef8de9fcc023d5a241afbca4a9bc1724b13fc0ba76037c2f55b6ddfc801fe size: 856
Result
--------
27
26
24
23
21

```
- Deploy: PASS
```
Plan: 2 to add, 0 to change, 0 to destroy.
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```
- Smoke: PASS
```
{
    "status": "ok",
    "service": "catalog-backend",
    "version": "27"
}
```

## Deployment
- image tag: 27
- FQDN: `catalog-api-ud15.graydesert-78eefa91.italynorth.azurecontainerapps.io`
- APP_VERSION: 27
- revision: catalog-api-ud15--3ejks4d
- health: Healthy
