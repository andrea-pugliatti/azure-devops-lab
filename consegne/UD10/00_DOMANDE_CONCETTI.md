# UD10 — Risposte alle domande sui concetti

## 1. Qual è la differenza fra codice sorgente, image e container?
**Risposta:**
Il codice sorgente è l'insieme dei file scritti dallo sviluppatore che compongono l'applicazione, ma che da soli non possono essere eseguiti senza un ambiente predisposto. L'Image è un artefatto immutabile che impacchetta il codice sorgente insieme al runtime, alle librerie di sistema, al filesystem e alle configurazioni di base. Il container è l'istanza creata a partire da un'immagine. Da una singola immagine è possibile generare molteplici container indipendenti.

## 2. Che cosa fa `docker build` e che cosa **non** fa?
**Risposta:**
`docker build` esegue la sequenza di istruzioni definita nel Dockerfile all'interno del build context, combinando l'immagine base con il codice e le dipendenze per produrre localmente una nuova immagine Docker immutabile. Non avvia l'applicazione e non crea processi runtime o container in esecuzione.

## 3. Che ruolo hanno Dockerfile, build context e `.dockerignore` nella creazione di un'image?
**Risposta:**
Il Dockerfile è la ricetta dichiarativa contenente le istruzioni necessarie a costruire l'immagine. Il build context è l'insieme di file e directory della macchina host, definito dal percorso passato a docker build, che viene inviato al Docker daemon per consentire le operazioni di copia.
.dockerignore definisce quali file e directory escludere dall'invio al build context. Riduce le dimensioni del contesto ed evita di includere file sensibili o inutili nell'immagine.

## 4. Che cosa succede se l'image indicata da `FROM` non è disponibile localmente?
**Risposta:**
Docker verifica la cache locale se l'immagine base non è presente, si collega automaticamente al registry remoto configurato, di default Docker Hub, e scarica i layer necessari prima di procedere con le istruzioni successive.

## 5. Distingui `RUN` e `CMD`.
**Risposta:**
`RUN` viene eseguito durante la build dell'immagine e serve a eseguire operazioni. In genere si usa per preparare il filesystem, ad esempio installare pacchetti, creare cartelle ecc. Il suo risultato viene congelato in un nuovo layer dell'immagine. `CMD` viene eseguito quando nasce il container con `docker run`. Non modifica l'immagine, ma specifica il processo predefinito che il container deve avviare all'avvio.

## 6. Perché una modifica a `server.py` non modifica automaticamente un'image già costruita?
**Risposta:**
Perché le immagini sono immutabili. Durante il comando docker build, l'istruzione COPY ha fatto una copia del file nei layer dell'immagine. Qualsiasi modifica successiva effettuata sull'host a server.py riguarda solo il file locale e non impatta l'immagine già compilata finché non si esegue una nuova build.

## 7. A che cosa serve il tag `catalog-backend:ud10`?
**Risposta:**
Serve a dare un'identità leggibile all'immagine costruita, dove catalog-backend rappresenta il nome del repository locale e ud10 funge da versione (tag). Questo permette di referenziarla in modo univoco nei comandi successivi.

## 8. Che cosa succede, in ordine, quando eseguiamo `docker run catalog-backend:ud10`?
**Risposta:**
1. Docker individua l'immagine locale catalog-backend:ud10.
2. Crea un container isolato con il proprio filesystem a partire dai layer dell'immagine.
3. Applica la configurazione runtime.
4. Avvia il processo specificato dall'istruzione CMD.

## 9. Distingui `EXPOSE 8000` e `--publish 127.0.0.1:8000:8000`.
**Risposta:**
`EXPOSE 8000` documenta su quale porta interna l'applicazione è predisposta ad ascoltare. È solo un metadato inserito nell'immagine, non apre né pubblica alcuna porta sull'host. `--publish 127.0.0.1:8000:8000` è una direttiva che istruisce Docker a instradare il traffico proveniente dalla porta 8000 dell'host direttamente sulla porta 8000 interna del container.

## 10. Che problema risolve Docker Compose rispetto a molti comandi `docker run` manuali?
**Risposta:**
Docker Compose permette di descrivere l'intera architettura desiderata in modo dichiarativo e ripetibile dentro un unico file YAML, invece di dover eseguire molteplici comandi sequenziali. Questo fa diminuire il rischio di errore umano insiti nella gestione manuale di stack multi-container.

## 11. Distingui il ruolo di un Dockerfile dal ruolo di `compose.yaml`.
**Risposta:**
Il Dockerfile definisce in modo imperativo la costruzione di una singola immagine. Il file compose.yaml, invece, definisce e orchestra il funzionamento congiunto di più container.

## 12. Che cosa crea/gestisce Compose nella nostra UD10?
**Risposta:**
Nella UD10 Compose gestisce:
- Due servizi (container): backend (Python) e frontend (Nginx).
- Una rete bridge privata: catalog-net per la comunicazione interna.
- Un named volume: catalog-runtime per la persistenza dei file del backend.

## 13. Perché `docker compose up -d --build` può costruire sia backend sia frontend?
**Risposta:**
Perché all'interno di compose.yaml, sia per il servizio backend sia per il servizio frontend, è presente la direttiva build con i rispettivi riferimenti al context e ai relativi Dockerfile. Il flag --build impone a Compose di attivare la fase di compilazione per entrambi prima della loro istanziazione.

## 14. Perché il backend non viene pubblicato direttamente sull'host nello stack Compose?
**Risposta:**
Il backend non viene pubblicato direttamente sull'host nello stack Compose per aderire alle best practice di sicurezza. Il backend è un'API interna che deve essere accessibile esclusivamente dal frontend attraverso la rete privata. Esporre solo la porta del frontend (127.0.0.1:8080) riduce la superficie d'attacco e assicura che tutto il traffico passi dal reverse proxy.

## 15. Perché Nginx usa `http://backend:8000` e non `http://localhost:8000`?
**Risposta:**
Perché ciascun container vive in una propria rete isolata. Dentro il container frontend, localhost fa riferimento a se stesso e non all'host né al backend. Sfruttando la rete creata da Compose, Docker attiva un DNS resolver interno che mappa automaticamente il nome del servizio backend sull'indirizzo IP privato del rispettivo container.

## 16. Che funzione ha `catalog-net`?
**Risposta:**
È una rete virtuale isolata di tipo bridge che connette tra loro i container frontend e backend, isolandoli da altri container estranei ed abilitando la risoluzione automatica dei nomi di servizio tramite un DNS interno.

## 17. Che funzione ha `catalog-runtime`?
**Risposta:**
È un volume gestito da Docker (named volume) che monta la cartella /runtime del container backend. Il suo scopo è garantire che i dati scritti dall'applicazione sopravvivano al riavvio, all'arresto o alla ricreazione del container.

## 18. Perché `running` e `healthy` non significano la stessa cosa?
**Risposta:**
`running` significa che il processo all'interno del container è attivo, ma non garantisce che l'applicazione sia reattiva. `healthy` è il risultato di un controllo a livello applicativo, un healthcheck, e indica che l'applicazione è effettivamente pronta a servire richieste con successo.

## 19. Che cosa fa `depends_on: condition: service_healthy` nel nostro stack?
**Risposta:**
Impedisce l'avvio prematuro del servizio frontend fino a quando il backend non solo risulta avviato, ma ha superato i controlli dell'healthcheck e ha raggiunto lo stato healthy. Questo evita che il frontend riceva traffico mentre il backend si sta ancora inizializzando.

## 20. Distingui rebuild e recreate.
**Risposta:**
Rebuild, tramite `--build` significa ricompilare l'immagine Docker. È necessario quando cambiano i file sorgente, le dipendenze o le istruzioni dei Dockerfile. Recreate, invece, significa distruggere e ricreare il container a partire da un'immagine già esistente. È sufficiente quando cambiano solo i parametri di runtime definiti in Compose, per esempio una variabile d'ambiente.

## 21. Quale ordine useresti per diagnosticare uno stack Compose che non risponde?
**Risposta:**
L'ordine per raccogliere evidenze prima di intervenire è:
1. docker compose ps: verificare stato dei container, porte esposte e se sono healthy o unhealthy.
2. docker compose logs: leggere errori su stdout/stderr del processo.
3. docker compose config: verificare che Compose abbia interpretato correttamente lo YAML.
4. Verificare lo stato dell'healthcheck.
5. Verificare le variabili d'ambiente effettive, anche con `docker inspect`.
6. Verificare le regole di pubblicazione delle porte.
7. Verificare la rete e la risoluzione DNS interna.
8. Verificare lo stato dei volumi e dei mount.
9. Decidere se il fix richiede una ricreazione del container (recreate) o una ricompilazione dell'immagine (rebuild).
10.	Applicare una modifica minima. 
11. Ripetere il test.

## 22. In che modo ciò che facciamo manualmente in UD10 verrà riutilizzato nelle pipeline successive?
**Risposta:**
La conoscenza dei comandi e la scrittura dei Dockerfile costituiscono la base di automazione per le UD future. Nella UD11, le immagini create localmente verranno inviate a un registry (Azure Container Registry) e pubblicate su Azure Container Apps. Nelle UD14 e UD15, i vari step di build, test, push e rilascio non verranno più digitati manualmente, ma eseguiti in modo automatico da un Azure Pipelines Agent all'interno di pipeline CI/CD.
