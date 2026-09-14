# UD06 — Risposte alle domande sui concetti

## 1. Quali risorse e componenti principali costituiscono una VM Azure e quale ruolo svolge ciascuno?
**Risposta:** 
I componenti principali di una VM Azure sono l'image, la size, l'OS Disk, il Data Disk, e la NIC. L'Image è l'immagine base attraverso la quale viene predisposto il sistema operativo. La Size è una combinazione di vCPU, RAM, I/O e funzionalità disponibili e definisce le capacità della macchina. L'OS Disk è il disco su cui è installato il sistema operativo e da cui la macchina esegue il boot. Il Data Disk è un disco aggiuntivo dedicato alla memorizzazione di dati e applicazioni separato dal disco di sistema ed è opzionale. La NIC è la scheda di rete logica che collega la VM alla VNet, assegnandole un indirizzo IP privato e collegandosi a subnet, NSG ed eventuale Public IP.

## 2. Perché la presenza di un Public IP non garantisce che un servizio sulla VM sia raggiungibile da Internet?
**Risposta:**
Il Public IP si limita a rendere indirizzabile la VM da Internet, ma non autorizza né gestisce il traffico end-to-end. Perché il servizio risponda, devono essere soddisfatte diverse condizioni lungo tutta la catena:
- Le regole del NSG devono autorizzare esplicitamente la porta, il protocollo e la sorgente. 
- Il firewall interno del sistema operativo non deve bloccare i pacchetti in ingresso.
- Il servizio applicativo deve essere avviato e attivo. 
- L'applicazione deve essere configurata per rimanere in ascolto sulla porta corretta.

## 3. Distingui Availability Zone, Availability Set e VM Scale Set, spiegando quale problema affronta ciascuno.
**Risposta:**
Azure offre diversi modelli di disponibilità. Le Availability Zone sono zone fisicamente separate all'interno della stessa regione e affronta il rischio di guasti fisici estesi all'intero data center. L'Availability Set è un raggruppamento logico che distribuisce le VM all'interno dello stesso data center tra Fault Domain, utilizzato per isolare guasti hardware/elettrici, e Update Domain, utilizzato per evitare fermi macchina simultanei durante la manutenzione programmata. La VM Scale Set gestisce un gruppo omogeneo e coordinato di VM come capacità elastica. Affronta l'esigenza di gestire l'aumento o la riduzione automatizzata delle istanze (scaling orizzontale) al variare del carico di lavoro.

## 4. Qual è la differenza tra scale up e scale out?
**Risposta:**
Scale up e Scale out sono due strategie che risolvono lo stesso problema, la gestione della potenza di una VM, ma lo fanno in maniera differente. Lo Scale up o scaling verticale consiste nell'aumentare la potenza della singola istanza, per esempio passando da 2 a 4 vCPU. Presenta limiti fisici dovuti all'infrastruttura stessa e mantiene l'istanza su un singolo punto operativo.
Lo Scale out o scaling orizzontale consiste nell'incrementare il numero di istanze identiche che lavorano in parallelo, per esempio passando da 1 a 3 VM. Migliora elasticità e disponibilità, ma richiede un'architettura software progettata per operare su più istanze.

## 5. Come può Azure Monitor Autoscale modificare un VM Scale Set e perché sono importanti soglie e limiti minimo/massimo?
**Risposta:**
Azure Monitor Autoscale modifica dinamicamente il numero di istanze attive valutando regole basate su metriche prestazionali, per esempio CPU media oltre una certa percentuale per un dato intervallo di tempo, o schedulazioni temporali. Le soglie temporali sono molto importanti perché impediscono che il sistema reagisca a picchi temporanei, evitando continue creazioni e distruzioni di VM alla minima oscillazione di carico. Inoltre il limite minimo garantisce che resti sempre erogabile un livello base di servizio, mentre il limite massimo impedisce un'espansione incontrollata del numero di macchine, contenendo l'impatto economico.

## 6. Qual è la differenza tra App Service Plan e Web App?
**Risposta:**
L'App Service Plan rappresenta l'infrastruttura gestita e stabilisce le risorse computazionali, la regione, il sistema operativo, il tier di costo e le funzionalità della piattaforma mentre le Web App sono le singole applicazioni ospitate sul piano. Più Web App distinte possono risiedere nello stesso App Service Plan condividendone la capacità.

## 7. Qual è la differenza concettuale tra Azure Monitor Autoscale e App Service Automatic Scaling?
**Risposta:**
L'Azure Monitor Autoscale è gestito tramite delle regole e soglie esplicite impostate dall'amministratore. Al contrario l'App Service Automatic Scaling è gestito nativamente dalla piattaforma. In entrambi è possibile effettuare scale up o scale out, ma nell'Automatic Scaling consiste nel passare a un tier superiore invece di impostare soglie e limiti superiori.

## 8. Distingui Metrics e Logs/Log Analytics e indica che tipo di informazione fornisce ciascuno.
**Risposta:**
Le Metrics sono informazioni numeriche, per esempio CPU utilizzata, ordinate in serie temporali e servono per visualizzare dei trend, impostare soglie di allarme e creare grafici in tempo reale. I Logs sono record testuali memorizzati in un Log Analytics workspace e interrogabili via query KQL. Forniscono il dettaglio diagnostico necessario a comprendere il motivo per cui un evento si è verificato, per esempio errori dell'applicativo all'interno di un servizio.

## 9. Qual è il ruolo di Recovery Services vault, backup policy e recovery point e come sono collegati?
**Risposta:**
Il Recovery Services vault è una risorsa che organizza e gestisce la protezione e i recovery point dei workload. La Backup Policy è un insieme di regole che determina quando effettuare la copia,  frequenza e schedule, e per quanto tempo mantenerla memorizzata, la retention policy. Un Recovery Point è la copia dello stato del sistema a un istante temporale definito, utilizzabile come riferimento per il ripristino. Questi tre componenti agiscono insieme per creare un sistema completo di backup. La VM viene associata al Vault tramite una Backup Policy e l'esecuzione della policy genera periodicamente i Recovery Point conservati nel vault per i ripristini futuri.

## 10. Distingui High Availability, Backup e Disaster Recovery utilizzando un esempio di esigenza per ciascuno.
**Risposta:**
High Availability, Backup e Disaster Recovery partecipano tutti alla resilienza di un sistema, ma non sono intercambiabili. L'High Availability garantisce che il servizio rimanga in funzione a fronte del guasto di un singolo componente. Il Backup consente di recuperare dati o configurazioni cancellati, persi o corrotti a partire da uno stato precedente. Il Disaster Recovery permette di ripristinare il funzionamento di un'intera applicazione in un ambiente alternativo dopo un evento catastrofico che ha compromesso l'ambiente primario.

## 11. Che cosa indicano RPO e RTO e perché rappresentano due requisiti diversi?
**Risposta:**
RPO (Recovery Point Objective) e RTO (Recovery Time Objective) sono due indicatori fondamentali per definire una strategia di backup. L'RPO è una misura temporale della massima perdita di dati tollerabile e ci aiuta a definire la frequenza necessaria per backup e repliche. L'RTO è l'intervallo di tempo massimo concesso per rimettere in funzione il servizio dopo l'interruzione. Rappresentano due requisiti distinti perché rispondono a due domande diverse. L'RPO definisce la quantità di dati persi, mentre l'RTO stabilisce la durata tollerata del downtime.

## 12. Perché una VM `deallocated` può continuare a generare costi?
**Risposta:**
Il passaggio a `deallocated` rilascia unicamente le risorse compute, azzerandone la spesa. Tuttavia, la macchina virtuale esiste ancora come entità, pertanto continuano a generare costi le risorse di supporto che rimangono allocate, cioé i dischi di archiviazione e le backup policy attive.
