## Domande di consolidamento della panoramica

1. Perché il deployment manuale precede la pipeline CD?

    Perché l'automazione deve arrivare solo dopo aver compreso a fondo l'operazione manuale e ne deve replicare gli stessi controlli. Questo evita che i file di configurazione diventino semplici istruzioni copiate senza conoscerne il funzionamento.

2. Quale differenza di responsabilità esiste tra ACR e Azure Container Apps?

    ACR (Azure Container Registry) ha l'unico scopo di memorizzare e versionare le immagini dei container, non esegue le applicazioni. Azure Container Apps serve a eseguire l'applicazione containerizzata all'interno dell'ambiente Azure, non funge da registro delle immagini.

3. Perché GitHub rimane il repository remoto anche quando vengono introdotte Azure Pipelines?

    Perché Azure Pipelines può leggere direttamente il codice dal repository GitHub personale. In questo modo si evita di duplicare il codice su due repository.

4. Quale vantaggio didattico offre l'uso continuativo del Catalogo prodotti?

    Fornisce un carico di lavoro applicativo stabile che non richiede di imparare un nuovo linguaggio o riscrivere il codice da zero. Consente di osservare come il codice rimanga identico mentre cambiano l'ambiente, la modalità di distribuzione e il livello di automazione, rendendo immediato il confronto tra gestione manuale e pipeline CI/CD.

5. In quale punto del percorso l'infrastruttura diventa descritta come codice?

    Nel Modulo 4: Infrastructure as Code, dove vengono introdotti Bicep e Terraform.

6. Quali elementi deve contenere un'evidenza tecnica utile?

    Deve essere contestualizzata e riportare lo scopo dell'operazione, l'operazione eseguita, il controllo utilizzato per verificarne l'esito, l'eventuale problema diagnosticato.

7. Quali strumenti sono esplicitamente esclusi dal percorso?

    Gli strumenti esclusi esplicitamente sono: Kubernetes, Jenkins, SonarQube, Ansible, Prometheus e Grafana.

8. Perché una versione più recente non determina automaticamente un aggiornamento?

    Perché prima di aggiornare occorre valutare la compatibilità e verificare se lo strumento esistente è già funzionante per gli scopi richiesti. Gli aggiornamenti non pianificati possono alterare dipendenze e rompere configurazioni preesistenti.

## Domande di controllo prima del laboratorio

1. Perché git --version può restituire due risultati diversi in PowerShell e in Ubuntu?

    Perché Windows e Ubuntu sono due ambienti operativi distinti con file system, utenti, percorsi e installazioni separati. Il comando lanciato in PowerShell interroga l'eseguibile di Git installato sull'host Windows, mentre eseguito nel terminale Ubuntu verifica la distinta installazione presente all'interno dell'ambiente Linux.

2. Quale comando distingue una distribuzione WSL 1 da una WSL 2?

    Il comando eseguito in PowerShell: `wsl --list --verbose`. La colonna VERSION indica se ciascuna distribuzione registrata usa l'architettura 1 o 2.

3. Perché conserveremo i progetti in ~/workspace e non principalmente in /mnt/c?

    /mnt/c fa riferimento al file system Windows montato in WSL e risulterebbe problematico per i successivi workflow con i container Docker e i relativi bind mount.

4. Che differenza c'è fra configurare l'autore di un commit e autenticarsi su GitHub?

    L' autore del commit (user.name e user.email) è una semplice dichiarazione di metadati all'interno della cronologia locale che indica chi ha generato la modifica. L'autenticazione è il meccanismo di sicurezza con cui si dimostra a GitHub che il proprio account dispone effettivamente dei permessi necessari per trasmettere modifiche al repository remoto tramite git push.

5. Perché eseguire git status sia prima sia dopo git add?

    Il primo git status consente di esaminare lo stato iniziale del working tree e identificare con precisione i file modificati, non tracciati o eliminati. Il secondo git status verifica l'effetto effettivo di git add, confermando quali modifiche sono entrate nella staging area ed evitando sia l'inclusione accidentale di file non voluti (o sensibili), sia la dimenticanza di file necessari prima del commit.