# UD15 — Verifica finale

## 1. Qual è la causa più probabile?
L'ingress di Azure Container Apps instrada il traffico sulla porta 9999, mentre l'applicazione all'interno del container è in ascolto sulla porta 8000. Quindi c'è un mismatch della porta e per questo lo smoke test fallisce.

## 2. Quali componenti non modificheresti?
Non modificheresti il codice dell'applicazione, il Dockerfile, né lo script dello smoke test. L'unico elemento da correggere è il file Terraform, riallineando target_port = 8000.

## 3. Quale autorizzazione controlli?
Il ruolo RBAC, verificando che sia assegnato all'identità collegata alla service connection sullo Storage Account oppure sul container dello state.

## 4. Perché non devi spostare lo state nel repository come soluzione?
Perché l'errore indica semplicemente che all'identità della service connection manca il ruolo adeguato (Storage Blob Data Contributor) sul container dello Storage Account. La soluzione corretta è assegnare il ruolo mancante, non cambiare la strategia di backend.

## 5. Quale service connection e quale ruolo controlli?
La Azure Resource Manager service connection della pipeline e il ruolo RBAC assegnato alla sua identità sull'Azure Container Registry.

## 6. Perché non abiliti l'admin user dell'ACR?
L'admin user introduce credenziali condivise e viola il principio del minimo privilegio.

## 7. Quali elementi confronti per ricostruire la release?
Verificare se differiscono o se l'ingress punta ancora a una revision precedente:
- Azure DevOps: il valore univoco della Build ID della run.
- Azure Container Registry: il tag dell'immagine appena pubblicata.
- Container App: la revision attiva, il valore del campo immagine e la variabile d'ambiente APP_VERSION iniettata.
- /health: la versione dichiarata nella risposta dall'endpoint.

## 8. Distingui Continuous Integration e Continuous Delivery.
La Continuous Integration (CI) verifica la qualità del codice. La Continuous Delivery (CD) estende il processo alla fase di rilascio, verificando che l'artefatto possa essere distribuito, che il servizio si avvii e che risponda correttamente tramite smoke test.

## 9. Perché UD15 utilizza uno state remoto?
Per garantire che lo stato dell'infrastruttura sia centralizzato e dotato di locking tra job, run e membri del team differenti, senza dipendere dal filesystem temporaneo dell'agente.

## 10. Perché lo Storage Account dello state viene preparato prima del laboratorio?
Terraform non può configurare il proprio state su una risorsa che ancora non esiste. Lo Storage e il blob container devono essere già pronti prima di invocare terraform init.

## 11. Come si autentica Terraform nella pipeline?
Terraform viene eseguito all'interno di un task AzureCLI@2. Questo task usa la service connection con Workload Identity Federation per avviare una sessione Azure. Terraform eredita e riutilizza direttamente quella sessione autenticata senza credenziali nei file .tf.

## 12. Quali risorse sono data source e quali sono gestite da Terraform?
- Data source esistenti: Resource Group, Azure Container Registry, User-Assigned Managed Identity.
- Gestite da Terraform: Container Apps Environment e Container App.

## 13. Perché il piano viene eseguito anche prima della build?
Per intercettare errori di sintassi o incompatibilità infrastrutturali prima di sprecare tempo e risorse nella build dell'immagine e nell'esecuzione dei test di codice.

## 14. Perché viene ricreato nello stage Deploy?
Perché lo stage Deploy deve verificare lo stato dell'infrastruttura in quel momento esatto e confermare il piano definitivo con l'immagine appena pubblicata dallo stage di BuildPush.

## 15. Perché la pipeline utilizza `AzureCLI@2` invece di `Docker@2`?
Docker@2 richiede una service connection dedicata di tipo Docker Registry. AzureCLI@2 permette di uniformare l'intera pipeline su una sola service connection Azure Resource Manager, eseguendo az acr login e i comandi CLI nativi via federazione.

## 16. Quale ruolo ha la pipeline sull'ACR?
AcrPush, necessario per caricare le nuove container image nel registry privato.

## 17. Quale ruolo ha la managed identity della Container App?
AcrPull, limitato alla sola lettura per consentire di scaricare l'immagine senza concedere permessi di modifica.

## 18. Perché `workspace: clean: all` non deve cancellare lo state della delivery?
Questa impostazione azzera il filesystem locale del self-hosted agent prima di ogni job. Mantenere lo state sul Blob Storage remoto garantisce che i job successivi o le nuove run non perdano la memoria delle risorse create.

## 19. Che cosa viene eliminato da `terraform destroy` alla fine?
Vengono eliminate esclusivamente le risorse tracciate nello state di UD15, il Container Apps Environment e la Container App.

## Cleanup finale

- pipeline Terraform destroy: Eseguita
- Container App rimossa: sì
- Environment rimosso: sì
- Resource Group eliminato: sì
- service connection rimosse: sì
- Agent deregistrato: sì
- repository conservato: sì
