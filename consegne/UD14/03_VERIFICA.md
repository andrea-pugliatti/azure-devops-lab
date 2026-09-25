# UD14 — Verifica

## 1. Perché Test deve precedere BuildPush?
Se il codice non supera i test, non ha senso sprecare risorse per costruire e pubblicare un'immagine non valida. Per questo il fallimento dei test arresta in anticipo la pipeline.

## 2. Che cosa significa pipeline verde?
Indica esclusivamente che tutti i controlli automatizzati configurati sono stati superati con successo. Non garantisce l'assenza di bug, la sicurezza del software o la sua idoneità al rilascio senza ulteriori verifiche.

## 3. Perché usiamo `Build.BuildId` come tag?
Fornisce un tag univoco che permette di risalire all'esatta run di Azure Pipelines che ha generato e pubblicato quella specifica immagine, a differenza di un tag generico e mutevole come latest.

## 4. Quale service connection usa Docker@2?
Usa la Docker Registry service connection `sc-acr-ud14`.

## 5. Perché ACR admin user può rimanere disabilitato?
Perché l'autenticazione verso Azure Container Registry avviene tramite Workload Identity Federation, eliminando la necessità gestire credenziali nel file yaml.

## 6. Se Test fallisce, ha senso controllare i permessi ACR? Perché?
No, poiché lo stage di test precede la build e il push, il fallimento interrompe l'esecuzione della pipeline prima dell'avvio del task Docker@2. Il registry ACR non entra in gioco durante la fase di test.

## 7. Quale evento attiva la CI configurata?
Un nuovo commit o un merge completato sul branch main, definito tramite `trigger: - main`.

## 8. Perché non eseguiamo cleanup alla fine di UD14?
Perché l'immagine container prodotta e pubblicata nel registro ACR fungerà direttamente da input per il deployment continuo in UD15.

## 9. Perché Microsoft-hosted è il percorso principale della CI UD14?
Perché adotta il modello cloud-native in cui l'infrastruttura dell'Agent è completamente gestita da Microsoft su macchine virtuali temporanee.

## 10. Perché due Job Microsoft-hosted non devono condividere implicitamente file locali?
Perché ogni job viene eseguito su una macchina virtuale temporanea. Ogni job riceve un ambiente nuovo e il filesystem non persiste tra un job e l'altro, rendendo necessario rieseguire il checkout del codice o trasferire esplicitamente artefatti tramite pipeline.

## 11. Quando si usa il fallback self-hosted?
Si utilizza il fallback self-hosted solo quando i Microsoft-hosted agent non sono disponibili.

## 12. Perché `workspace: clean: all` è importante sul fallback self-hosted?
L'Agent self-hosted lavora su un filesystem persistente. Ripulire il workspace prima di ogni job evita che file residui o cache di run precedenti possano falsare l'esito della nuova compilazione.

## 13. Distingui Agent e Docker Registry service connection.
L'Agent è la risorsa incaricata di eseguire i task della pipeline, mentre la service connection fornisce l'identità con cui quei task accedono ai servizi esterni.

## 14. Perché controlliamo `python3 --version` e `docker --version` nei Job?
Permette di distinguere immediatamente i problemi applicativi da quelli legati all'ambiente di esecuzione. In sostanza ci accertiamo che i tool necessari siano presenti e funzionanti sull'Agent prima di eseguire i comandi della pipeline.

## Gate

- UD14_AGENT_MODE: MICROSOFT_HOSTED
- pipeline CI riuscita: sì
- ACR/tag: 15
