# Consegna UD04 — Verifica

## Parte A — Scelta singola

Per le domande 1–8 riporta risposta e motivazione.

1. A. Blob progettato per oggetti binari e file multimediali accessibili via HTTP.
2. B. Files offre unità di rete gestite accessibili con SMB e NFS
3. B. No, serve un ruolo del piano dati
4. B. ZRS Zone-Redundant Storage, replica su più zone all'interno della stessa region
5. B. SAS
6. C. segnala una soglia ma non blocca i consumi.
7. B. soltanto Blob che corrispondono al filtro
8. A. rende esplicito l'uso dell'identità Entra

## Parte B — Risposte brevi

9. La ridondanza garantisce un alto livello di disponibilità e la tolleranza ai guasti dell'infrastruttura (hardware, datacenter o intera region) tramite replica. Non protegge da errori accidentali. Al contrario il backup protegge dall'errore umano e consente il ripristino di uno stato precedente dei dati.
10. Il Management Plane governa il ciclo di vita, le configurazioni di rete e le policy della risorsa Azure. Un esempio di comando è `az storage account create`, per creare uno storage account.
Il Data Plane gestisce l'accesso e la manipolazione dei dati memorizzati (lettura, scrittura, cancellazione dei file). Un esempio di comando è `az storage blob download`, per leggere/scaricare un file.
11. 
    1. Concessione esclusiva dell'azione richiesta (es. solo lettura r, escludendo scrittura o cancellazione).
    2. Durata ristretta al tempo minimo necessario.
    3. Scope a livello di singolo Blob anziché a livello di intero container o Storage Account.
    4. Obbligo di HTTPS esclusivo (--https-only).
12. Il tier Archive è un livello offline a bassissimo costo di memorizzazione progettato per archiviazione a lungo termine. Prima di poter essere letto, un blob in Archive deve subire un processo di ripristino, operazione che richiede molto tempo, oltre a costi addizionali.
13. Le Storage Account Key sono credenziali che bypassano le regole RBAC e garantiscono permessi totali e illimitati. Anche una SAS salvata nel codice rimane riutilizzabile da terzi per tutta la sua durata di validità. Se salvate in un repository, restano memorizzate nella cronologia Git, esponendosi a data breach.

## Parte C — Caso situazionale

14. Problemi identificati:
    1. Il ruolo Contributor non è adatto. Esso è infatti un ruolo del management plane, non garantisce l'accesso al data plane tramite Entra ID ed espone l'infrastruttura al rischio di modifiche accidentali dell'intero account.
    2. La condivisione della Account Key viola il principio del minimo privilegio. Questo, inoltre, garantisce permessi totali e illimitati.
    Anche la SAS a pieni permessi e senza scadenza breve viola il principio del minimo privilegio.
    3. L'omissione di --auth-mode login causa il fallback della CLI sull'autenticazione tramite chiave anziché usare l'accesso sicuro basato su token Entra ID.
15. Autorizzazione e Scope consigliati:
    1. Fare uso di --auth-mode login nella CLI, eliminando l'uso delle chiavi nel codice.
    2. Assegnare il ruolo Storage Blob Data Reader, o Storage Blob Data Contributor in caso sia necessaria la modifica.
    3. Limitare l'assegnazione al singolo container che ospita i documenti privati, evitando lo scope a livello di resource group o di account.
    4. Generare User Delegation SAS, di sola lettura e con scadenza di breve durata.
16. Procedura di verifica dell'accesso:
Dopo aver assegnato il ruolo Storage Blob Data Contributor facciamo diverse verifiche. 
1. Verifichiamo che l'assignment si sia propagato con `az storage container list`.
2. Facciamo un'operazione di test:
```bash
az storage blob upload \
  --account-name "<STORAGE_ACC>" \
  --container-name "<CONTAINER_NAME>" \
  --name <FILE_NAME> \
  --file <FILE_NAME> \
  --auth-mode login \
  --overwrite \
  --output table
```
3. Generiamo e usiamo una SAS:
```bash
SAS_EXPIRY="$(date -u -v '+15M' +%Y-%m-%dT%H:%MZ)"
SAS_URL="$(az storage blob generate-sas \
  --account-name "<STORAGE_ACC>" \
  --container-name "<CONTAINER_NAME>" \
  --name <FILE_NAME> \
  --permissions r \
  --expiry "$SAS_EXPIRY" \
  --auth-mode login \
  --as-user \
  --full-uri \
  --output tsv)"

curl --fail --silent --show-error "$SAS_URL" --output /tmp/documento-sas.txt
cmp <FILE_NAME> /tmp/documento-sas.txt
unset SAS_URL
```

Procedura di cleanup:
1. Rimozione dal portale della role assignment, oppure tramite `az role assignment delete`.
2. 
```bash
unset SAS_URL
az group delete --name "$LAB_RG" --yes --no-wait
az group wait --name "$LAB_RG" --deleted
az group exists --name "$LAB_RG"
```
