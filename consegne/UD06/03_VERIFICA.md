# UD06 — Verifica

## Parte A

1. Quale componente collega normalmente una VM Azure alla subnet?
Risposta: A. NIC
2. Quale tecnologia gestisce un insieme di VM e può supportare autoscaling?
Risposta: B. VM Scale Set
3. Passare da 2 a 4 istanze è:
Risposta: B. scale out
4. Quale elemento definisce capacità e tier di una Web App?
Risposta: A. App Service Plan
5. Azure Monitor Autoscale può usare:
Risposta: B. metriche e regole
6. Quale affermazione è corretta?
Risposta: B. Metrics rappresentano valori numerici nel tempo; Logs sono record interrogabili
7. Quale risorsa è centrale nel workflow tradizionale di Azure Backup per VM?
Risposta: A. Recovery Services vault
8. Azure Site Recovery è principalmente associato a:
Risposta: B. Disaster Recovery

## Parte B

9. Distingui Availability Zone, Availability Set e VM Scale Set.
Risposta: 
Availability Zone, Availability Set e VM Scale Set sono modelli diversi di disponibilità. Le Availability Zone sono zone fisicamente separate all'interno della stessa regione e Selezionarle serve a proteggersi dall'interruzione completa di un intero data center. L'Availability Set è un raggruppamento logico che distribuisce le VM all'interno dello stesso data center tra Fault Domain, utilizzato per isolare guasti hardware/elettrici, e Update Domain, utilizzato per evitare fermi macchina simultanei durante la manutenzione programmata. La VM Scale Set sono un insieme coordinato e identico di VM gestito come risorsa unica. Automatizza lo scale out e scale in al variare del workload.

10. Distingui scale up e scale out.
Risposta: Lo scale up o scaling verticale consiste nell'aumentare la potenza della singola istanza esistente, mantenendo una singola istanza. Lo scale out o scaling orizzontale consiste nell'aumentare il numero di istanze attive in parallelo, distribuendo il carico.

11. Distingui App Service Autoscale e Automatic Scaling.
Risposta: L'Azure Monitor Autoscale è il modello di scaling basato sulle soglie utilizzando delle metriche configurate dall'amministratore o su pianificazioni orarie. L'Automatic Scaling è, invece, una funzionalità nativa della piattaforma App Service che scala automaticamente in base all'andamento del traffico dell'applicazione.

12. Distingui High Availability, Backup e Disaster Recovery.
Risposta: L'High Availability significa mantenere il servizio attivo a fronte del guasto di un singolo componente o istanza. Il Backup consente di recuperare dati o configurazioni cancellati, persi o corrotti a partire da uno stato creato precedentemente. Il Disaster Recovery permette di ripristinare il funzionamento di un'intera applicazione in un ambiente alternativo dopo un evento catastrofico che ha compromesso l'ambiente primario.

13. Spiega Recovery Services vault, backup policy e recovery point.
Risposta: Il Recovery Services vault è una risorsa che organizza e gestisce la protezione e i recovery point dei workload. La Backup Policy è un insieme di regole che determina quando effettuare la copia,  frequenza e schedule, e per quanto tempo mantenerla memorizzata, la retention policy. Un Recovery Point è la copia dello stato del sistema a un istante temporale definito, utilizzabile come riferimento per il ripristino. Questi tre componenti agiscono insieme per creare un sistema completo di backup. La VM viene associata al Vault tramite una Backup Policy e l'esecuzione della policy genera periodicamente i Recovery Point conservati nel vault per i ripristini futuri.

14. Distingui RPO e RTO.
Risposta: L'RPO è la quantità massima di perdita dati tollerabile, espressa come intervallo temporale e determina la frequenza dei backup/repliche. L'RTO è il tempo massimo concesso affinché il servizio torni accessibile e operativo.

## Parte C

15. Qual è la causa più probabile e perché?
Risposta: La causa è la priorità di valutazione delle regole nell'NSG. La regola `Deny-HTTP` viene valutata prima della regola `Allow-HTTP`. Dato che curl localhost ha successo, il web server Nginx è correttamente avviato e in ascolto. Il blocco è interamente situato a livello di rete.

16. Qual è la correzione minima e quali verifiche useresti per dimostrare il ripristino end-to-end?
Risposta: La correzione minima è eliminare la regola Deny-HTTP, oppure abbassare la priorità di Allow-HTTP a un valore inferiore a 100. Una volta applicata la correzione, si può verificare il corretto funzionamento della rete con IP Flow Verify ed un test HTTP verso l'IP pubblico con il comando `curl -I http://<PUBLIC_IP>`. Se riceviamo una risposta 200 OK allora sapremo che l'intero flusso è funzionante.
