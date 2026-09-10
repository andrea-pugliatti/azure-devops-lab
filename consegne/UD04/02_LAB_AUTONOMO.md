# Consegna UD04 — Laboratorio autonomo

## Scelta del servizio e della ridondanza

Riporta requisito, servizio scelto, ridondanza e motivazione.

Per lo scenario proposto la soluzione giusta è il Blob Storage perché è progettato specificamente per memorizzare oggetti non strutturati (documenti, PDF, immagini). Supporta access tier (Hot è ideale per l'accesso frequente durante i 30 giorni), lifecycle management (consente di automatizzare l'eliminazione dei file temporanei dopo 1 giorno) e autenticazione senza chiavi con User Delegation SAS. Azure Files è pensato per unità di rete condivise utilizzando protocolli SMB/NFS. Azure Queue e Azure Table non sono idonei per memorizzare file o documenti.
Per quanto riguarda la ridondanza LRS replica i dati solo localmente. Viene adottato nel laboratorio perché è l'opzione a minor costo. In produzione è conveniente usare ZRS (Zone-Redundant Storage) perché replica i dati in modo sincrono attraverso tre distinte Availability Zone all'interno della stessa region.

## Operazioni e verifica

Documenta container, Blob e SAS limitata riportando soltanto configurazione ed esito.

Da CLI creo un nuovo container con il comando:
`az storage container create --account-name "$LAB_STORAGE" --name archive --auth-mode login`
La risposta è: 
```json
{
  "created": true
}
```
Assegno il ruolo Storage Blob Data Contributor per l'utente. Aspetto la propagazione e dopo carico il Blob con il comando:
`az storage blob upload --account-name "$LAB_STORAGE" --container-name archive --name current/documento.txt --file consegne/UD04/01_DOCUMENTO_LAB.txt --auth-mode login --overwrite`
La risposta è:
```json
{
  "client_request_id": "ae22d152-ad01-11f1-b24a-f5bc523d33b0",
  "content_md5": "iHJM98k0MPZjlOJ7uiHd5g==",
  "date": "2026-09-10T10:23:35+00:00",
  "encryption_key_sha256": null,
  "encryption_scope": null,
  "etag": "\"0x8DF0F2592D0DD39\"",
  "lastModified": "2026-09-10T10:23:36+00:00",
  "request_id": "5ef96161-c01e-00d8-200e-41ed04000000",
  "request_server_encrypted": true,
  "structured_body": null,
  "version": "2026-04-06",
  "version_id": null
}
```
Generiamo una SAS che consente solo lettura del singolo Blob e scade dopo 15 minuti. (Nota: il comando è per macOS, usa il parametro -v invece del parametro -d)
```bash
SAS_EXPIRY="$(date -u -v '+15M' +%Y-%m-%dT%H:%MZ)"
SAS_URL="$(az storage blob generate-sas \
  --account-name "$LAB_STORAGE" \
  --container-name archive \
  --name current/documento.txt \
  --permissions r \
  --expiry "$SAS_EXPIRY" \
  --auth-mode login \
  --as-user \
  --full-uri \
  --output tsv)"

curl --fail --silent --show-error "$SAS_URL" --output /tmp/documento-sas.txt
cmp consegne/UD04/01_DOCUMENTO_LAB.txt /tmp/documento-sas.txt
unset SAS_URL
```

## Diagnosi

Per ciascun errore separa sintomo, piano interessato, causa, controllo e correzione.

1. Sintomo: AuthorizationPermissionMismatch
Piano interessato: Data Plane.
Causa probabile: L'identità Microsoft Entra ID autenticata non possiede le autorizzazioni RBAC necessarie per accedere ai blob o per richiedere la chiave di delega utente (User Delegation Key). Questo accade tipicamente se l'utente possiede solo ruoli del management plane (come Contributor o Reader sull'account), se il ruolo dati non è stato ancora assegnato, oppure se l'assegnazione non si è ancora propagata nei sistemi di autorizzazione di Azure.
Controlli: Verificare l'identità connessa con `az account show --query "user.name" --output tsv` e verificare se l'account ha ruoli del piano dati assegnati sullo storage account con `az role assignment list --scope <storage-account> --output table`.
Correzione: Assegnare all'identità un ruolo del piano dati, come Storage Blob Data Contributor limitando lo scope allo Storage Account o al singolo Container, e attendere qualche minuto affinché le assegnazioni si propaghino.

2. Sintomo: ResourceNotFound: The specified container does not exist
Piano interessato: Data Plane.
Causa probabile: Nello script è stato specificato `--container-name <NOME>`, ma tale container non esiste nello storage account.
Controlli: Elencare i container presenti nello storage account con `az storage container list --account-name <storage-account> --auth-mode login --query "[].name" --output table`
Correzione: Indicare il container corretto: `--container-name <NOME_CORRETTO>`

3. Sintomo: curl: (22) The requested URL returned error: 403
Piano interessato: Data Plane.
Causa probabile: Il comando curl è stato inviato in un modo non valido. Per esempio se la variabile $SAS_URL viene passata senza doppi apici, oppure se l'identità che l'ha emessa perde i permessi o la sessione scade, il token cessa immediatamente di essere valido.
Controlli: Verificare che la data sia effettivamente generata con `echo "$SAS_EXPIRY"` ed eseguire una chiamata curl dettagliata (rimuovendo --silent e --fail) per leggere l'errore restituito da Azure `curl -i "$SAS_URL"`.
Correzione: Assicurarsi di racchiudere sempre la variabile dell'URL tra doppi apici ("$SAS_URL") per preservare i parametri di firma e verificare che il blob indicato nel comando SAS (current/documento.txt) corrisponda alla risorsa che si intende effettivamente scaricare.

## Lifecycle, costi e cleanup

Spiega la relazione tra prefisso e lifecycle rule, indica i driver di costo e registra la verifica del cleanup.

Nelle policy di Lifecycle Management di Azure Storage, le regole valutano su quali blob effettuare l'azione tramite un confronto sul prefisso partendo dall'inizio del percorso completo.
Il prefisso configurato impone che il percorso inizi tassativamente con documents/. Il blob in questione si trova invece nel container archive/. 

I principali driver di costo sono:
- Capacità di archiviazione: Volume di dati effettivamente conservato.
- Livello di ridondanza: Scelta dello SKU (LRS è la configurazione più economica rispetto a ZRS o replica geografica GRS).
- Access Tier: Hot comporta un costo di conservazione per GB più alto rispetto a Cool/Archive, ma costi nettamente inferiori per singola operazione/transazione e nessuna penale di permanenza o tempo di recupero.

Cleanup:
Rimossa l'assegnazione RBAC del ruolo Storage Blob Data Contributor dal portale IAM dello storage account. Rimosse le variabili di sessione e i token dalla shell. È stato eliminato il Resource Group contenente lo storage account e tutti i dati con `az group delete --name "$LAB_RG" --yes --no-wait`
Attesa con `az group wait --name "$LAB_RG" --deleted`
Verifica con `az group exists --name "$LAB_RG"`
Che risponde con `false` 

## Risultato finale

- nessun segreto pubblicato:
- hash abbreviato e messaggio del commit:

