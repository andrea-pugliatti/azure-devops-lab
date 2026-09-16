# UD08 — Risposte domande concetti

## 1. Perché DevOps non coincide con Azure DevOps?
**Risposta:**
DevOps è un modo di lavorare incentrato sulla continuità tra sviluppo, test e rilascio tramite flussi ripetibili e condivisi. Azure DevOps è, invece, una delle tante piattaforme software che possono supportare e automatizzare tale flusso.

## 2. Distingui Continuous Integration, Continuous Delivery e Continuous Deployment.
**Risposta:**
Per Continuous Integration (CI) intendiamo la pratica di integrare modifiche piccole e frequenti nella branch comune, verificandole rapidamente per minimizzare conflitti e rischi. Con la Continuous Delivery (CD) il codice è mantenuto sempre in uno stato pronto per il rilascio tramite test automatizzati, ma il passaggio effettivo in produzione richiede un'approvazione umana esplicita. Con la Continuous Deployment ogni modifica che supera con successo tutti i test e i controlli automatici viene rilasciata in produzione direttamente, senza interventi manuali.

## 3. Che differenza c'è tra working tree, staging area e commit?
**Risposta:**
Il working tree è l'area di lavoro locale contenente i file su cui si lavora. La staging area è l'area intermedia, popolata con git add, in cui si preparano le modifiche da includere nel commit successivo. Il commit è, inceve, lo snapshot registrato nella cronologia locale del repository tramite git commit.

## 4. Perché conviene creare un feature branch da `main` aggiornata?
**Risposta:**
Per basare il nuovo sviluppo sullo stato stabile e più recente del progetto, riducendo al minimo divergenze e il rischio di conflitti quando la funzionalità verrà reintegrata in main.

## 5. Che cosa rappresentano base branch e head branch in una Pull Request?
**Risposta:**
L'head branch è la branch che contiene le modifiche proposte mentre la base branch è la branch di destinazione destinata a ricevere e integrare le modifiche.

## 6. Perché una review non dovrebbe limitarsi a controllare che il codice "funzioni"?
**Risposta:**
Perché deve verificare la qualità complessiva e la sicurezza della modifica. Ci sono altri parametri da controllare, per esempio la leggibilità di codice e documentazione, la presenza di test coerenti ecc.

## 7. Che cosa succede a una Pull Request quando il contributor aggiunge un nuovo commit allo stesso branch?
**Risposta:**
La Pull Request si aggiorna automaticamente includendo i nuovi commit e ricalcolando il diff, senza richiedere l'apertura di una nuova richiesta.

## 8. Che cosa provoca tipicamente un merge conflict?
**Risposta:**
La presenza di modifiche incompatibili su una stessa porzione di codice, ad esempio quando due branch modificano diversamente la stessa riga, cioé una situazione in cui Git non può determinare autonomamente quale versione sia corretta.

## 9. Perché l'accesso del collaboratore deve essere rimosso al termine?
**Risposta:**
Per rispettare il principio del least privilege bisogna concedere l'accesso solo alle risorse necessarie e solo per il tempo strettamente indispensabile.

## 10. Distingui frontend, backend/API, configurazione e dati nel Catalogo prodotti.
**Risposta:**
Il frontend, nel nostro caso static/index.html, è l'interfaccia utente mostrata nel browser che interroga le API tramite JavaScript.
Il backend/API, nel nostro caso server.py è il server HTTP in Python che implementa la logica, serve i file, legge dati e configurazioni e risponde alle richieste REST in JSON.
La configurazione è data da config.json e sono le impostazioni di runtime dell'applicazione, separati dal codice.
I dati, nel nostro caso data/products.json, è l'archivio JSON contenente l'elenco e le proprietà dei prodotti.

## 11. Quali endpoint principali espone l'applicazione?
**Risposta:**
1. `GET /`: restituisce il frontend statico.
2. `GET /health`: verifica lo stato operativo del servizio HTTP.
3. `GET /api/products`: restituisce l'intera collezione dei prodotti in JSON.
4. `GET /api/products/`: restituisce i dettagli di un singolo prodotto (oppure 404 Not Found se inesistente).

## 12. Perché è utile eseguire `git diff` prima del commit?
**Risposta:**
Per ispezionare visivamente le righe modificate prima di registrarle, potendo distinguere cosa è fuori dallo staging (git diff) da cosa sta per entrare definitivamente nel commit (git diff --cached), garantendo commit privi di modifiche indesiderate.
