# UD14 — Consegna LAB autonomo

- branch: feature/ud14-ci-v2
- modifica app: 
Ho cambiato la versione dell'app da v1 a v2
```py
APP_VERSION = os.getenv("APP_VERSION", "ci-v1")
APP_VERSION = os.getenv("APP_VERSION", "ci-v2")
```
- test fallito: test_health_version fallisce
- causa: Il test si aspetta che la versione sia `ci-v1` non `ci-v2`
- modifica test:
Ho modificato il test in modo che si aspetti la nuova versione
```py
self.assertEqual(payload["version"], "ci-v2")
```
- test finale: PASS
```
Ran 3 tests
OK
```
- PR: `UD14: bump catalog version to ci-v2`
https://github.com/andrea-pugliatti/azure-devops-lab/pull/5
- merge: fc10275
- run CI:
Image ID: 23537d91f1f5385ea3cfe294a038185c19a5ea08ac5df8e29a1a1958067b8598
UD14_FINAL_IMAGE_TAG=15
- nuovo tag ACR: 15
- cleanup eseguito: NO
