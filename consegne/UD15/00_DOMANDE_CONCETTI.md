# UD15 — Domande concetti

## 1. Qual è la differenza tra CI e Continuous Delivery?
**Risposta:**
La Continuous Integration (CI) verifica la qualità del codice. La Continuous Delivery (CD) estende il processo alla fase di rilascio, verificando che l'artefatto possa essere distribuito, che il servizio si avvii e che risponda correttamente tramite smoke test.

## 2. Quale service connection utilizza la pipeline UD15?
**Risposta:**
Utilizza la Azure Resource Manager service connection `sc-azure-ud13-15`, autenticata tramite WIF.

## 3. Perché la pipeline non utilizza `Docker@2`?
**Risposta:**
Il task `Docker@2` richiede una Docker Registry service connection separata. Per mantenere l'architettura uniforme su una singola connessione Azure Resource Manager, la pipeline usa un task AzureCLI@2 all'interno del quale esegue direttamente i comandi nativi az login, docker build e docker push.

## 4. Quale ruolo deve avere l'identità della pipeline sull'ACR?
**Risposta:**
Il ruolo AcrPush, necessario per inviare e pubblicare le container image nel registry.

## 5. Perché Terraform usa uno state remoto?
**Risposta:**
Per garantire che lo stato sia centralizzato e condivisibile tra job e run differenti.

## 6. Dove viene conservato lo state UD15?
**Risposta:**
Lo state di UD15 viene conservato da remoto in Azure Storage nel container `tfstate`.

## 6bis. Perché lo Storage dello state deve esistere prima di `terraform init`?
**Risposta:**
Perché Terraform non può istanziare un backend su una risorsa che ancora non esiste. L'infrastruttura di appoggio dello state deve essere disponibile prima dell'inizializzazione del provider e del caricamento del file `.tfstate`.

## 7. Quale ruolo permette alla pipeline di utilizzare lo state Blob?
**Risposta:**
Storage Blob Data Contributor, assegnato all'identità della service connection sul container dedicato `tfstate`.

## 8. Come si autentica Terraform durante la pipeline?
**Risposta:**
La service connection apre una sessione Azure autenticata tramite Workload Identity Federation. Terraform, venendo eseguito all'interno del blocco AzureCLI@2, eredita e riutilizza automaticamente il contesto della sessione CLI attiva, senza bisogno di credenziali cablate nei file .tf.

## 9. Quali risorse vengono lette come esistenti?
**Risposta:**
Risorse preesistenti:
- Resource Group
- Azure Container Registry
- User-assigned managed identity

## 10. Quali risorse vengono gestite da Terraform?
**Risposta:**
Risorse prodotte:
- Container Apps Environment (azurerm_container_app_environment)
- Container App (azurerm_container_app)

## 11. Perché la Container App utilizza una managed identity?
**Risposta:**
Una Managed Identity serve a permettere a una risorsa cloud di autenticarsi verso altri servizi Azure senza memorizzare credenziali nel codice.
Per rispettare il principio del minimo privilegio, l'identità della Container App deve avere solo i permessi di lettura della immagine `AcrPull`.

## 12. Quale ruolo ha questa identità sull'ACR?
**Risposta:**
AcrPull, che consente all'applicazione di scaricare l'immagine dal registry per eseguirla.

## 13. Perché viene eseguito `terraform plan` nello stage iniziale?
**Risposta:**
Perché consente di validare la sintassi e la coerenza dell'infrastruttura prima di sprecare tempo e risorse nei test di codice e nella compilazione dell'immagine.

## 14. Perché viene ricreato il piano nello stage Deploy?
**Risposta:**
Perché lo stage Deploy deve verificare lo stato dell'infrastruttura in quel momento esatto e confermare il piano definitivo con l'immagine appena pubblicata dallo stage di BuildPush.

## 15. Che relazione esiste tra Build ID, tag e `APP_VERSION`?
**Risposta:**
Il Build ID funge da identificatore univoco e viene applicato come tag della container image su ACR e utilizzato come variabile d'ambiente `APP_VERSION` nella Container App. In questo modo si stabilisce un collegamento diretto tra la run della pipeline, l'immagine e l'output restituito dall'endpoint `/health`.

## 16. Perché uno smoke test può fallire dopo un apply riuscito?
**Risposta:**
Perché terraform apply certifica solo che Azure ha accettato la configurazione infrastrutturale richiesta. Lo smoke test può comunque fallire per diversi motivi, per esempio se l'applicazione va in crash all'avvio, se la porta di ascolto non corrisponde o se l'endpoint `/health` risponde con un errore.

## 17. Perché `workspace: clean: all` rende importante lo state remoto?
**Risposta:**
Il comando serve ad eliminare i file nella directory di lavoro dell'agente prima di ogni job. Se lo state fosse salvato localmente, verrebbe eliminato tra uno stage e l'altro, rendendolo inutile. Sarebbe quindi impossibile per i job successivi conoscere le risorse già create.

## 18. Che cosa dimostra l'errore sulla target port?
**Risposta:**
Dimostra che applicare con successo un cambiamento all'infrastruttura non significa che il servizio stia funzionando. Terraform applica il cambio di porta, ma l'applicazione continua ad ascoltare sulla 8000, causando il fallimento dello smoke test e provando la necessità di ulteriori controlli.

## 19. Che cosa elimina `terraform destroy` a fine UD15?
**Risposta:**
Lo eseguiamo alla fine dell'esercitazione per eliminare le risorse sulle risorse gestite dallo state UD15, cioé Container App e Container Apps Environment.

