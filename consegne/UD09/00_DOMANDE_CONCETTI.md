# UD09 — Risposte alle domande sui concetti

## 1. Perché DevOps non può essere ridotto a un prodotto?
**Risposta:**
DevOps è un insieme di principi e pratiche organizzative, non un software. Un prodotto come Azure DevOps supporta queste pratiche, ma non le sostituisce. Senza cultura e processi adeguati, l'automazione rende soltanto più veloce la propagazione degli errori.

## 2. Quali sono le tre dimensioni principali che abbiamo associato a DevOps?
**Risposta:**
- Cultura: collaborazione, responsabilità condivisa, feedback.
- Processi: planning, versioning, review, testing, release, operation.
- Tecnologia: Git, pipeline, container, IaC, monitoraggio, security automation.

## 3. Perché il lifecycle DevOps è rappresentato come un ciclo?
**Risposta:**
Perché non è una catena lineare con un termine. Le fasi finali di monitoraggio e feedback producono dati su prestazioni, incident, bug e richieste utente che ritornano alla fase di pianificazione iniziale (Plan), rendendo il processo iterativo e continuo.

## 4. Distingui Agile e DevOps.
**Risposta:**
L'Agile è un approccio che organizza e guida il team nello sviluppo di valore in cicli brevi, iterativi e adattivi. Il DevOps estende la continuità operativa collegando lo sviluppo alle fasi di integrazione, testing, delivery, deployment, manutenzione operativa e feedback.

## 5. Che cos'è un backlog?
**Risposta:**
È una lista ordinata di elementi da realizzare. Tali elementi vengono descritti, prioritizzati, stimati, raffinati e progressivamente selezionati per le iterazioni.

## 6. Che cos'è uno sprint?
**Risposta:**
È un intervallo di tempo definito e breve, tipico degli Scrum, durante il quale il team si impegna a realizzare, verificare e consegnare un insieme di elementi estratti dal backlog per raccogliere feedback.

## 7. Distingui Scrum e Kanban.
**Risposta:**
Lo Scrum è un framework strutturato basato su iterazioni a tempo fisso (sprint), ruoli definiti (Product Owner, Scrum Master, Development Team), eventi e artefatti. Il Kanban è una board organizzata in modo da mostrare un flusso continuo di lavoro tramite colonne e card, mirato a identificare colli di bottiglia e a limitare il lavoro simultaneo mediante WIP limit (Work In Progress limit).

## 8. Distingui Epic, Feature, User Story, Task e Bug.
**Risposta:**
Epic, Feature, User Story, Task e Bug sono tutti dei work item, inquadrati in una gerarchia.
Una epic è un macro-obiettivo ad ampio raggio. Una feature è una funzionalità significativa del prodotto. Una user story è un requisito descritto dal punto di vista dell'utente finale. Un task è un'attività tecnica necessaria all'implementazione della user story. Un bug è una segnalazione e tracciamento di un difetto del sistema.

## 9. A che cosa servono gli Acceptance Criteria?
**Risposta:**
Definiscono le condizioni oggettive e verificabili che una specifica funzionalità deve obbligatoriamente soddisfare per essere considerata valida e completa per l'utente.

## 10. Perché il Version Control è importante anche per IaC e pipeline YAML?
**Risposta:**
Permette di tracciare chi, come, quando e perché ha modificato le configurazioni dell'infrastruttura e dei flussi di build. Questo permette di fare review delle configurazioni e di creare deployment ripetibili.

## 11. Distingui build e artifact.
**Risposta:**
Build è il processo che compila o trasforma il codice sorgente in un formato eseguibile o pubblicabile. L'Artifact è risultato generato dalla build destinato a essere distribuito e/o validato.

## 12. Distingui unit test, integration test e smoke test.
**Risposta:**
Lo Unit test testa una singola unità di codice isolata. L'Integration test verifica la corretta interazione tra due o più servizi integrati (es. logica applicativa e database). Lo Smoke test è un test preliminare e rapido eseguito dopo il deployment per verificare che il servizio sia raggiungibile e che le funzioni basilari rispondano, per esempio l'endpoint `/health`.

## 13. Che cosa significa shift-left?
**Risposta:**
Significa anticipare verifiche, controlli di qualità e test di sicurezza nelle primissime fasi dello sviluppo. Questo riduce costi, rischi e complessità rispetto al rilevamento in produzione.

## 14. Definisci Continuous Integration.
**Risposta:**
È la pratica per cui gli sviluppatori integrano frequentemente le proprie modifiche in un repository condiviso, innescando l'esecuzione automatica di build e test ad ogni aggiornamento per ottenere riscontri immediati.

## 15. Distingui Stage, Job e Step.
**Risposta:**
Stage è il raggruppamento logico di alto livello della pipeline, cioé Test, Build, Deploy. Job è l'insieme di step eseguiti consecutivamente sul medesimo agent. Step è la singola istruzione eseguita all'interno di un job.

## 16. Distingui Continuous Delivery e Continuous Deployment.
**Risposta:**
Continuous Delivery significa mantenere il codice in uno stato sempre pronto per la produzione con rilascio automatizzato nei vari ambienti, ma l'effettiva pubblicazione in produzione richiede un'approvazione umana manuale. Continuous Deployment significa che l'intero percorso è del tutto automatizzato. Ogni modifica che supera con successo tutti i controlli della pipeline viene rilasciata direttamente senza intervento manuale.

## 17. Distingui Dockerfile, image e container.
**Risposta:**
Un Dockerfile è un file di testo contenente le istruzioni sequenziali necessarie per costruire l'image. L'Image è un artifact immutabile risultante dalla build, che include filesystem, dipendenze e configurazioni. Il Container è un'istanza in esecuzione generata a partire dall'immagine.

## 18. Che cos'è un registry?
**Risposta:**
È un archivio centralizzato dedicato alla memorizzazione e distribuzione delle container image.

## 19. Che problema risolve un orchestrator?
**Risposta:**
Automatizza la gestione scalata e distribuita dei container su più nodi, facendosi carico di scheduling, allocazione repliche, self-healing, service discovery, scaling e rolling update.

## 20. Che cos'è Infrastructure as Code?
**Risposta:**
È la gestione, configurazione e provisioning dell'infrastruttura IT tramite file dichiarativi versionabili (es. file Bicep o Terraform), garantendo deployment ripetibili, consistenti e tracciabili anziché interventi manuali da interfaccia web.

## 21. Che cosa aggiunge DevSecOps al lifecycle?
**Risposta:**
Integra le pratiche di sicurezza in ciascuna fase operativa del DevOps anziché relegarle a una revisione isolata al termine del ciclo.

## 22. Che cos'è una DevOps toolchain?
**Risposta:**
È l'insieme di strumenti software selezionati da un team per supportare l'intero lifecycle, dalla pianificazione al monitoraggio.

## 23. Quali sono i cinque principali servizi Azure DevOps?
**Risposta:**
Azure Boards, Azure Repos, Azure Pipelines, Azure Test Plans, Azure Artifacts

## 24. A che cosa serve Azure Boards?
**Risposta:**
Serve a pianificare, monitorare e tracciare le attività del team attraverso Work Item, backlog, sprint, query, dashboard e schede Kanban.

## 25. Perché nel corso usiamo GitHub invece di Azure Repos?
**Risposta:**
Perché mantenere lo stesso codice sincronizzato su due provider differenti genererebbe complessità, confusione e rischi di divergenza.

## 26. Distingui Azure Test Plans e test automatici in pipeline.
**Risposta:**
Azure Test Plans è la piattaforma che supporta attività di test management, in particolare test manuali ed esplorativi. I test in pipeline sono suite automatizzate, composti da unit test e integration test, eseguite programmaticamente dall'agent durante il flusso CI/CD.

## 27. Distingui Azure Artifacts e Azure Container Registry.
**Risposta:**
Azure Artifacts è la piattaforma di package management e gestisce i package di librerie applicative e dipendenze, ad esempio NuGet, npm, Maven, package Python. Azure Container Registry conserva e distribuisce container image.

## 28. Distingui Organization e Project.
**Risposta:**
L'Organization è il contenitore amministrativo principale di Azure DevOps che governa utenti globali, policy di sicurezza, billing e Agent Pool condivisi. Un Project è il workspace all'interno dell'Organization in cui risiedono i singoli servizi e flussi (Boards, Pipelines ecc.).

## 29. Distingui Agent, Agent Pool e Parallel Job.
**Risposta:**
Un Agent è una macchina o container che esegue concretamente gli step previsti dal job. Un Agent Pool è un raggruppamento logico che contiene e gestisce uno o più agent. Un Parallel Job determina il limite massimo di job che possono essere eseguiti contemporaneamente dall'infrastruttura.

## 30. Distingui Microsoft-hosted e self-hosted Agent.
**Risposta:**
Un agent Microsoft-hosted viene eseguito in un ambiente temporaneo, isolato e mantenuto da Microsoft. Un agent self-hosted viene installato e gira su una macchina on-premises, cloud VM o WSL2 gestita direttamente dall'utente, con ambiente persistente e pieno controllo su tool e configurazioni.

## 31. Che cos'è una Service Connection?
**Risposta:**
È una connessione autenticata e sicura configurata in Azure DevOps per autorizzare le pipeline a interagire con risorse o piattaforme esterne, ad esempio GitHub e subscription Azure.

## 32. Perché il PAT di registrazione può essere revocato dopo che l'agent è Online?
**Risposta:**
Perché il Personal Access Token viene impiegato esclusivamente per registrare l'agent nell'Organization e non viene riutilizzato per l'esecuzione delle pipeline successive.

## 33. Perché DevOps non coincide con Azure DevOps?
**Risposta:**
Perché DevOps è una metodologia universale fatto di cultura, processi condivisi e standard tecnologici, mentre Azure DevOps è una specifica suite proprietaria prodotta da Microsoft per implementare tali concetti e possono essere implementati con strumenti differenti.

## 34. Qual è la differenza tra Azure Pipelines e Azure Pipelines Agent?
**Risposta:**
L'Azure Pipelines è la piattaforma di orchestrazione e schedulazione dei job. L'Azure Pipelines Agent è l'effettivo esecutore che riceve il job e lo esegue.

## 35. Qual è la relazione concettuale tra Azure Pipelines Agent, Jenkins Agent, GitHub Runner e GitLab Runner?
**Risposta:**
Ricoprono lo stesso ruolo architetturale nei rispettivi ecosistemi. Sono gli effettivi esecutori incaricati di scaricare ed eseguire i job assegnati dalla piattaforma di orchestrazione di riferimento.

## 36. Quali attività svolgerà concretamente l'Agent nelle UD13–UD15?
**Risposta:**
Nell'UD13 l'agent esegue la validazione di template IaC (Terraform/Bicep) e i primi job di pipeline. Nell'UD14 l'agent esegue test automatici Python, build dell'immagine Docker e push verso Azure Container Registry. Nell'UD15 l'agent esegue verifiche, deployment e smoke test finali.

## 37. Perché un Job Microsoft-hosted non dovrebbe dipendere da file lasciati dal Job precedente?
**Risposta:**
Perché gli ambienti Microsoft-hosted sono effimeri. Al completamento del job la macchina viene distrutta e il job successivo viene allocato su un'istanza nuova e completamente reimpostata.

## 38. Perché il WSL2 personale del corso non rappresenta la topologia self-hosted tipica di un team?
**Risposta:**
Perché in un contesto produttivo un pool self-hosted poggia su server fisici o virtual machine condivise gestite a livello infrastrutturale dall'azienda.

## 39. Come può essere organizzato un Agent Pool aziendale?
**Risposta:**
Viene strutturato come un pool condiviso di container o macchine virtuali su cloud o on-premises gestito in modo centralizzato e resa accessibile ai diversi progetti dell'Organization per servire le build accodate dagli sviluppatori.

## 40. Perché più Agent non implicano automaticamente più Job eseguibili in parallelo?
**Risposta:**
Perché la concorrenza reale è limitata dalla capacità di Parallel Jobs abilitata a livello di Organization. Se sono presenti più agent Online ma è disponibile un solo slot di parallel job, l'infrastruttura elaborerà comunque un singolo job per volta, mantenendo gli altri in coda.
