# UD13 — Consegna LAB autonomo

- branch: fix/ud13-pipeline-path
- path errato: infra/bicep/delivery-NON-ESISTE.bicep
- stage: Deploy
- job: DeployIaC
- step: Bicep What-If and deployment
- messaggio:
```
2026-09-24T10:43:00.0801230Z WARNING: The configuration value of bicep.use_binary_from_path has been set to 'false'.
2026-09-24T10:43:01.6184150Z ERROR: An error occurred reading file. Could not find file '/Users/<omitted>/azdo-agent/_work/1/s/infra/bicep/delivery-NON-ESISTE.bicep'.
2026-09-24T10:43:03.2055660Z ERROR: An error occurred reading file. Could not find file '/Users/<omitted>/azdo-agent/_work/1/s/infra/bicep/delivery-NON-ESISTE.bicep'.
```
- classe del problema: è un errore di file / path del repository
- correzione: Correggere il path del file
- run finale: PASS
- PR/merge: 
```
gh pr create \
  --base main \
  --head fix/ud13-pipeline-path \
  --title "UD13: introduce and fix pipeline error" \
  --body "Testiamo in modo controllato un errore all'interno pipeline."
```
b41d2d3