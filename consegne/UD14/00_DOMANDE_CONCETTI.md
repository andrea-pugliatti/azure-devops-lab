# UD14 — Domande concetti

## 1. Qual è lo scopo principale della Continuous Integration?
**Risposta:**
Effettuare una sequenza di controlli automatizzati e ripetibili per ogni modifica al repository in modo, per verificare il codice e pubblicare l'artefatto. Il punto è riuscire ad identificare bug e regressioni non appena il codice viene inviato, garantendo un alto livello di qualità del codice.

## 2. Perché eseguiamo i test prima della build Docker?
**Risposta:**
Se il codice non supera i test, non ha senso sprecare risorse per costruire e pubblicare un'immagine non valida. Per questo il fallimento dei test arresta in anticipo la pipeline.

## 3. Che cosa significa realmente una pipeline verde?
**Risposta:**
Indica esclusivamente che tutti i controlli automatizzati configurati sono stati superati con successo. Non garantisce l'assenza di bug, la sicurezza del software o la sua idoneità al rilascio senza ulteriori verifiche.

## 4. Perché `Build.BuildId` è più utile di `latest` come tag dell'immagine?
**Risposta:**
Fornisce un tag univoco che permette di risalire all'esatta run di Azure Pipelines che ha generato e pubblicato quella specifica immagine, a differenza di un tag generico e mutevole come latest.

## 5. Qual è la differenza tra `sc-azure-ud13-15` e `sc-acr-ud14`?
**Risposta:**
sc-azure-ud13-15 è una Azure Resource Manager service connection usata per gestire le risorse infrastrutturali di Azure mentre sc-acr-ud14 è una Docker Registry service connection usata specificamente dal task Docker@2 per autenticarsi con Azure Container Registry per build e push.

## 6. Perché l'admin user dell'ACR rimane disabilitato?
**Risposta:**
Viene impiegata la Workload Identity Federation, un modello basato su identità federate che elimina la necessità di memorizzare le credenziali del registry nel file yaml.

## 7. Qual è l'effetto del trigger sul branch `main`?
**Risposta:**
Determina l'avvio automatico di una nuova run della pipeline a ogni commit o merge sul branch main.

## 8. Perché due Job Microsoft-hosted distinti effettuano entrambi il checkout del repository?
**Risposta:**
I due job possono essere allocati su VM Linux temporanee differenti. Non condividendo il filesystem locale, ciascun Job deve effettuare checkout: self per ricostruire il contesto a partire dal repository.

## 9. Che cosa significa che un Job Microsoft-hosted riceve un ambiente temporaneo?
**Risposta:**
Significa che la macchina virtuale viene creata per quel singolo job e distrutta alla fine. Ogni job riceve un ambiente nuovo e un job successivo non trova traccia dei file lasciati dal job precedente.

## 10. Qual è la differenza fra responsabilità dell'Agent e responsabilità della service connection?
**Risposta:**
L'Agent esegue i task della pipeline, mentre la service connection fornisce l'identità e le autorizzazioni con cui i task accedono ai servizi esterni.

## 11. Perché verifichiamo `python3 --version` e `docker --version` nei Job?
**Risposta:**
Permette di distinguere immediatamente i problemi applicativi da quelli legati all'ambiente di esecuzione. In sostanza ci accertiamo che i tool necessari siano presenti e funzionanti sull'Agent prima di eseguire i comandi della pipeline.

## 12. Quando utilizziamo il file YAML self-hosted di fallback?
**Risposta:**
Lo utilizziamo solo quando i Microsoft-hosted agent non sono disponibili.

## 13. A cosa serve `workspace.clean: all` nel fallback self-hosted?
**Risposta:**
Ripulisce l'intero workspace prima dell'avvio, impedendo che file residui lasciati da run precedenti sull'Agent persistente possano alterare l'esito della nuova esecuzione.

## 14. Che cosa fa `Docker@2` con `buildAndPush`?
**Risposta:**
Esegue in un singolo step la compilazione dell'immagine dal Dockerfile, l'assegnazione del tag specificato e il push dell'artefatto nel registry ACR tramite la service connection configurata.

## 15. In quale ordine conviene leggere i log quando una pipeline fallisce?
**Risposta:**
Si procede in modo progressivo: Stage → Job → Step → comando → messaggio di errore.

## 16. Perché un errore nei test non deve portarci immediatamente a controllare ACR?
**Risposta:**
Perché lo stage dei test precede quello di build e push. Se il test fallisce, la pipeline si blocca in anticipo e il processo non tenta nemmeno di connettersi o inviare dati verso ACR.

## 17. Che cosa dimostra il test failure intenzionale del laboratorio autonomo?
**Risposta:**
Dimostra che la CI è importante per rilevare velocemente le incoerenze tra codice e test. Ciò conferma che l'efficacia della pipeline è vincolata alla qualità dei test scritti e non ci da una visione completa sulla qualità del software.

## 18. Perché non eliminiamo ACR e immagini al termine della UD14?
**Risposta:**
Perché l'immagine container salvata in ACR sarà l'applicazione di partenza per la fase di deployment in UD15.

## 19. In che modo la UD14 prepara direttamente il lavoro della UD15?
**Risposta:**
La UD14 realizza la Continuous Integration, cioè validazione del codice, creazione dell'immagine, applicazione di tag con Build.BuildId e pubblicazione in ACR. La UD15 avvierà la Continuous Delivery prelevando l'immagine pronta per distribuirla.
