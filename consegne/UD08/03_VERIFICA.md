# UD08 — Verifica

## Parte A

1. DevOps è:
B. un insieme di cultura, pratiche, automazione e feedback

2. Continuous Integration significa soprattutto:
A. integrare frequentemente modifiche verificabili

3. `git add`:
B. sposta modifiche nella staging area

4. In una Pull Request `feature/x → main`, la head branch è:
B. feature/x

5. Una review con `Request changes`:
B. consente di chiedere modifiche prima del merge

6. Un merge conflict si verifica quando:
B. Git non può determinare in sicurezza il contenuto finale

7. Nel Catalogo prodotti, `/api/products` appartiene principalmente a:
B. backend/API

8. Perché eseguire `git diff --cached`?
A. per vedere le modifiche che entreranno nel prossimo commit

## Parte B

9. Distingui Continuous Delivery e Continuous Deployment.
In Continuous Delivery il software viene mantenuto in uno stato pronto per il rilascio tramite verifiche automatizzate, ma il passaggio in produzione richiede una decisione umana. In Continuous Deployment ogni modifica che supera i controlli automatizzati viene distribuita direttamente, senza interventi manuali.

10. Perché è utile creare un feature branch invece di lavorare sempre direttamente su `main`?
Permette di isolare le modifiche in corso dallo stato stabile del progetto. In questo modo si possono sviluppare e revisionare nuove funzionalità senza rischiare di rompere il `main`.

11. Che cosa succede alla stessa Pull Request quando vengono aggiunti nuovi commit al suo head branch?
La Pull Request si aggiorna automaticamente e include i nuovi commit nella cronologia della proposta e aggiorna la vista del diff, senza bisogno di chiudere la PR o aprirne una nuova.

12. Elenca almeno quattro controlli utili in una code review.
- Rispetto dei requisiti della modifica proposta.
- Assenza di segreti, token, password o file di configurazione privati.
- Assenza di file estranei, residui temporanei o modifiche non pertinenti.
- Leggibilità del codice e presenza di test o verifiche adeguate.

13. Spiega perché l'accesso temporaneo di un collaboratore deve essere rimosso.
Si applica il principio del least privilege. L'accesso deve essere concesso solo ai collaboratori attuali e solo per il tempo necessario. Mantenere permessi attivi dopo la fine dell'attività espande inutilmente la superficie di rischio e di attacco del repository.

14. Distingui frontend, backend/API, configurazione e dati nel Catalogo prodotti.
Il frontend, nel nostro caso static/index.html, è l'interfaccia utente mostrata nel browser che interroga le API tramite JavaScript.
Il backend/API, nel nostro caso server.py è il server HTTP in Python che implementa la logica, serve i file, legge dati e configurazioni e risponde alle richieste REST in JSON.
La configurazione è data da config.json e sono le impostazioni di runtime dell'applicazione, separati dal codice.
I dati, nel nostro caso data/products.json, è l'archivio JSON contenente l'elenco e le proprietà dei prodotti.

## Parte C

15. Quali problemi individui prima del merge?
- La presenza di token.txt indica la possibilità che siano state lasciate delle credenziali che non deve mai risiedere nel versionamento Git.
- La presenza del file di un'altra UD indica che ci potrebbero essere delle modifiche fuori dal contesto della PR.
- L' assenza di test HTTP indica che la PR manca di verificabilità.

16. Quale sequenza di correzione e verifica richiederesti prima di approvare?
- Rimuovere token.txt dalla cronologia e inserirlo nel file .gitignore, procedendo alla rotazione del token.
- Rimuovere il file non correlato dell'altra UD.
- Verifica locale, avviando il server in locale e testando gli endpoint.
- Eseguire commit, effettuare il push sullo stesso branch e allegare l'output dei test HTTP della PR.
