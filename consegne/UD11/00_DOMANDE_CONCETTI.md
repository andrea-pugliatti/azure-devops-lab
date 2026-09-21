# UD11 — Risposte alle domande sui concetti

## 1. Distingui registry, repository, tag e digest.
**Risposta:**
Il registry è il servizio cloud che ospita le immagini Docker. Il repository è il raggruppamento all'interno del registry per le diverse versioni di una stessa immagine. Il tag è un'etichetta testuale leggibile e riutilizzabile associata a una versione dell'immagine. Il digest è un identificatore univoco e immutabile calcolato direttamente sul contenuto dell'immagine.

## 2. Perché non conviene basare una release solo sul tag `latest`?
**Risposta:**
`latest` è un semplice tag e non garantisce che l'immagine sia la più recente o una versione validata. Usare tag espliciti garantisce la tracciabilità, permettendo di sapere esattamente quale versione del codice è in esecuzione su ciascuna revision.

## 3. Perché il login server ACR va letto dalla risorsa invece di costruirlo manualmente?
**Risposta:**
Il nome risorsa di Azure e l'endpoint di login effettivo non coincidono necessariamente. Leggere il login server direttamente dalle proprietà della risorsa con Azure CLI evita errori manuali.

## 4. Qual è la differenza tra Container Apps Environment e Container App?
**Risposta:**
Container Apps Environment è l'ambiente infrastrutturale in cui risiedono una o più Container App. La Container App è la specifica dichiarativa della singola applicazione, cioé descrive l'immagine, risorse CPU/RAM, variabili d'ambiente, ingress, scalabilità e identità.

## 5. Distingui revision e replica.
**Risposta:**
Una revision è uno snapshot immutabile della configurazione dell'applicazione, comprende la versione dell'immagine, le scale rule, le environment variable, le risorse. Una replica è l'istanza runtime effettiva del container generata da una revision per gestire il traffico.

## 6. A che cosa serve ingress?
**Risposta:**
Serve ad instradare il traffico esterno HTTPS tramite FQDN pubblico verso la porta interna corretta del container all'interno dell'ambiente gestito.

## 7. Perché target port deve coincidere con la porta di ascolto dell'applicazione?
**Risposta:**
L'ingress indirizza il traffico verso la targetPort specificata. Se questa non coincide con la porta su cui il processo interno ascolta, le richieste inoltrate dall'ingress non troveranno alcun socket aperto, rendendo l'applicazione irraggiungibile.

## 8. Perché usare managed identity per il pull da ACR?
**Risposta:**
Consente alla Container App di autenticarsi su ACR tramite ruoli Azure RBAC, eliminando la necessità di esporre credenziali e password statiche nel codice o nelle configurazioni.

## 9. A che cosa serve `AcrPull`?
**Risposta:**
Fornire alla Container App il livello minimo di autorizzazione necessario per leggere e scaricare le immagini dal registry privato. Non concede i diritti di scrittura/push, rispettando il principio del privilegio minimo.

## 10. Che cosa succede tipicamente quando aggiorni l'image di una Container App?
**Risposta:**
La modifica dell'immagine attiva la creazione automatica di una nuova revision immutabile. In modalità single revision, la nuova revision diventa la versione attiva che riceve il traffico al posto della precedente.

## 11. Perché in UD11 eseguiamo manualmente operazioni che verranno automatizzate in UD14–UD15?
**Risposta:**
Perché l'automazione consiste nel codificare un flusso già compreso, non nel saltarne l'apprendimento. Eseguire i passaggi manualmente permette di comprendere le responsabilità di rete, identità, porte e packaging prima di delegarle agli agent di pipeline.

## 12. Qual è la differenza fra RBAC Registry Permissions e RBAC+ABAC rispetto ai ruoli `AcrPull`/`AcrPush`?
**Risposta:**
Nel modello RBAC Registry Permissions si impiegano i ruoli classici a livello di registry, cioé AcrPull e AcrPush. Nel modello RBAC + ABAC, i ruoli legacy non si applicano all'accesso ai repository e vengono sostituiti da ruoli più granulari, cioé Container Registry Repository Reader e Container Registry Repository Writer.

## 13. Perché il self-hosted Agent di UD09 non è necessario per svolgere UD11, pur essendo collegato alla progressione del corso?
**Risposta:**
In UD11 i comandi vengono lanciati direttamente da terminale e l'Agent non è necessario per questo laboratorio. Lo stesso ambiente e Docker verranno riutilizzati dall'Agent nelle pipeline delle unità successive.

## 14. Distingui system-assigned e user-assigned managed identity.
**Risposta:**
Managed Identity consente ad un'applicazione di autenticarsi in modo sicuro verso altri servizi Azure. System-assigned significa che l'identità è creata direttamente per la Container App e il suo lifecycle è legato alla risorsa e viene eliminata con essa. User-assigned significa che l'identità è creata come risorsa Azure indipendente, con un ciclo di vita autonomo, e riutilizzabile su più risorse.

## 15. Che cosa significa scale-to-zero?
**Risposta:**
Scale-to-zero è la capacità dell'applicazione di dismettere tutte le repliche runtime in assenza di traffico, azzerando il consumo di compute finché non arriva una nuova richiesta. Questo si applica quando minReplicas = 0.

## 16. Quali controlli eseguiresti se il FQDN restituisce errore dopo una nuova revision?
**Risposta:**
Bisogna seguire il flusso di troubleshooting:
- Verificare che l'ingress sia impostato come external.
- Verificare che la target port corrisponda alla porta reale dell'applicazione.
- Controllare lo stato della revision, cioé se è attiva e healthy.
- Controllare lo stato e il numero delle repliche.
- Esaminare i log applicativi del container.
- Verificare che l'immagine e il tag esistano nel registry ACR.
- Controllare la configurazione della Managed Identity e l'assegnazione del ruolo AcrPull.
- Verificare la correttezza delle variabili d'ambiente passate al container.
