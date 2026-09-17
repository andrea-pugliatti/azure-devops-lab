# UD09 — Verifica individuale

## 1. B. un insieme di cultura, pratiche e tecnologie che collegano sviluppo, delivery e operation

## 2. C. Agile riguarda soprattutto lavoro iterativo/adattivo; DevOps estende il flusso a delivery e operation

## 3. A. un elenco ordinato di lavoro da realizzare

## 4. B. integrare frequentemente e verificare automaticamente build/test

## 5. B. un risultato di build destinato a una fase successiva

## 6. B. può portare automaticamente in produzione ogni modifica che supera i gate previsti

## 7. B. container image

## 8. A. gestire scheduling, repliche, scaling e lifecycle di workload containerizzati

## 9. A. planning e tracking del lavoro

## 10. B. GitHub

## 11. B. il processo/macchina che esegue un job

## 12. A. la capacità di eseguire job contemporaneamente

## 13. Distingui Scrum e Kanban.
Lo Scrum è un framework strutturato basato su iterazioni a tempo fisso (sprint), ruoli definiti (Product Owner, Scrum Master, Development Team), eventi e artefatti. Il Kanban è una board organizzata in modo da mostrare un flusso continuo di lavoro tramite colonne e card, mirato a identificare colli di bottiglia e a limitare il lavoro simultaneo mediante WIP limit (Work In Progress limit).

## 14. Distingui Epic, Feature, User Story e Task.
Epic, Feature, User Story, Task e Bug sono tutti dei work item, inquadrati in una gerarchia.
Una epic è un macro-obiettivo ad ampio raggio. Una feature è una funzionalità significativa del prodotto. Una user story è un requisito descritto dal punto di vista dell'utente finale. Un task è un'attività tecnica necessaria all'implementazione della user story. Un bug è una segnalazione e tracciamento di un difetto del sistema.

## 15. Distingui build e artifact.
Build è il processo che compila o trasforma il codice sorgente in un formato eseguibile o pubblicabile. L'Artifact è risultato generato dalla build destinato a essere distribuito e/o validato.

## 16. Distingui unit test, integration test e smoke test.
Lo Unit test testa una singola unità di codice isolata. L'Integration test verifica la corretta interazione tra due o più servizi integrati (es. logica applicativa e database). Lo Smoke test è un test preliminare e rapido eseguito dopo il deployment per verificare che il servizio sia raggiungibile e che le funzioni basilari rispondano, per esempio l'endpoint `/health`.

## 17. Che cosa significa shift-left?
Significa anticipare e spostare i controlli di qualità e test il più presto possibile nel ciclo di vita dello sviluppo, invece di riscontrare gli errori solo in ambienti di test più avanzati o in produzione.

## 18. Distingui Stage, Job e Step.
Stage è il raggruppamento logico di alto livello della pipeline, cioé Test, Build, Deploy. Job è l'insieme di step eseguiti consecutivamente sul medesimo agent. Step è la singola istruzione eseguita all'interno di un job.

## 19. Distingui Azure Repos, Azure Pipelines, Azure Test Plans e Azure Artifacts.
Azure Repos, Azure Pipelines, Azure Test Plans e Azure Artifacts sono tutti servizi di Azure DevOps. Azure Repos è il servizio di version control basato su repository Git. Azure Pipelines è il servizio di automazione CI/CD per eseguire build, test e deployment. Azure Test Plans è lo strumento per la gestione, pianificazione e tracciamento di test manuali ed esplorativi. Azure Artifacts è il servizio dedicato al package management di librerie.

## 20. Distingui Organization, Project, Agent Pool e Agent.
Organization è il contenitore amministrativo principale a livello aziendale e governa utenti e billing. Il Project è il workspace logico interno all'Organization che contiene Boards, Pipelines e impostazioni specifiche. L'Agent Pool è il perimetro organizzativo e di autorizzazione che raggruppa un insieme di esecutori. L'Agent è la macchina o container che riceve il job ed esegue materialmente i comandi previsti.

## 21. Distingui Microsoft-hosted e self-hosted Agent.
Un agent Microsoft-hosted viene eseguito in un ambiente temporaneo, isolato e mantenuto da Microsoft. Un agent self-hosted viene installato e gira su una macchina on-premises, cloud VM o WSL2 gestita direttamente dall'utente, con ambiente persistente e pieno controllo su tool e configurazioni.

## 22. Perché `Grant access permission to all pipelines` non è la scelta predefinita consigliabile?
Perché viola il principio del least privilege. Autorizzare indistintamente qualsiasi pipeline consente anche a pipeline compromesse di usare le credenziali verso sistemi esterni. L'accesso va concesso in modo esplicito solo alle pipeline autorizzate.

## 23. Perché il PAT di registrazione dell'agent può essere revocato dopo la configurazione?
Perché il PAT viene utilizzato esclusivamente durante l'esecuzione di ./config.sh per autenticare la registrazione della macchina e ottenere le credenziali di sessione dedicate. Nelle successive esecuzioni con ./run.sh, l'agent comunica con Azure DevOps tramite queste credenziali e non richiede più il PAT.

## 24. Azure Pipelines e Azure Pipelines Agent sono la stessa cosa? Spiega la differenza.
No, operano su due livelli architetturali distinti. Azure Pipelines è il servizio di orchestrazione che definisce che cosa, quando e dove eseguire le attività della pipeline. Azure Pipelines Agent è l'esecutore materiale, ossia il processo in esecuzione sull'ambiente host incaricato di eseguire i comandi.

## 25. Associa correttamente:
Azure Pipelines -> Azure Pipelines Agent
Jenkins -> Jenkins Agent
GitHub Actions -> Runner
GitLab CI/CD -> GitLab Runner

## 26. Perché Jenkins viene citato nella UD09 anche se non lo utilizzeremo operativamente?
Viene citato per chiarire che i concetti di pipeline, controller, agent, job e build sono metodologie universali del DevOps e non peculiarità di Azure DevOps.

## 27. Quali compiti svolgerà concretamente un Agent nelle UD13–UD15?
Nell'UD13 l'agent esegue la validazione di template IaC (Terraform/Bicep) e i primi job di pipeline. Nell'UD14 l'agent esegue test automatici Python, build dell'immagine Docker e push verso Azure Container Registry. Nell'UD15 l'agent esegue verifiche, deployment e smoke test finali.

## 28. Perché prepariamo sia self-hosted sia Microsoft-hosted?
Perché il self-hosted permette di comprendere concretamente dove e come avvengono le esecuzioni locali dei tool (Docker, Terraform, Azure CLI), mentre il Microsoft-hosted insegna a lavorare con gli ambienti effimeri tipici delle pipeline industriali, consentendo di confrontare vantaggi e limiti di entrambi i modelli.

## 29. Qual è la prima causa da verificare e quale azione eseguiresti?
Causa possibile: L'agent è correttamente registrato ma il relativo listener non è in esecuzione (registrato != in esecuzione).
Azione: Eseguire il comando `./run.sh`.

## 30. Perché non creeresti subito un nuovo PAT e non riconfigureresti l'agent?
Perché la registrazione è già valida e salvata e, infatti, l'agent compare sul portale. Il PAT serviva unicamente per la fase di registrazione, quindi la sua revoca è la procedura corretta. Riconfigurare da capo l'agent non risolverebbe il vero motivo dell'inattività.

## Mappa finale

| Area | Strumento del percorso |
|---|---|
| Planning | Azure Boards, a livello concettuale |
| Version Control | GitHub |
| CI/CD | Azure Pipelines |
| Container | Docker |
| Registry | Azure Container Registry |
| IaC | Bicep + Terraform |
| Runtime | Bicep + Terraform |
| Monitoring | Azure Container Apps |
