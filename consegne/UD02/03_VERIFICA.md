# Consegna UD02 — Verifica

## Parte A — Scelte operative

Per le domande 1–8 riporta risposta e motivazione.

1. B L'IaaS offre il controllo necessario all'azienda.
2. C Configurazione dell'applicazione, identità, accessi e dati.
3. C È un contenitore logico per risorse che possono condividere ciclo di vita e governance.
4. B La località indica dove Azure conserva i metadati del resource group; le risorse possono avere località proprie.
5. B `az account show`
6. C stcea02a7f9 perché richiede solo lettere minuscole e numeri
7. B Il tag documenta l'intenzione, ma serve ancora una procedura o una policy che esegua l'eliminazione.
8. C `az group exists --name <NOME>` restituisce false.

## Parte B — Risposte brevi

9. 
Nel caso del Cloud privato, l'applicazione viene eseguita interamente su un'infrastruttura dedicata all'organizzazione, garantendo il massimo controllo a fronte di costi e manutenzione a carico dell'azienda.
Nel caso del Cloud pubblico, l'applicazione sfrutta la piattaforma del provider e l'azienda non possiede l'infrastruttura fisica, ma mantiene la gestione di dati, identità, autorizzazioni e configurazioni.
Nel caso del Cloud ibrido, si hanno sia componenti locali e sia risorse su cloud pubblico. L'azienda conserva i dati all'interno del proprio server aziendale e si appoggia ad Azure in base alla necessità aziendale.
10. 
Tenant Microsoft Entra è chi gestisce le identità e gli accessi dell'organizzazione.
La sottoscrizione risiede nel tenant e definisce il confine di fatturazione, quote e limiti amministrativi.
Il Resource group è contenitore logico all'interno della sottoscrizione per raggruppare risorse che condividono ciclo di vita e governance.
Una risorsa è un'istanza di un servizio (per esempio macchina virtuale) creata all'interno di un unico resource group e legata a una sola sottoscrizione.
11. 
Una region è un perimetro geografico definito che ospita uno o più datacenter. Un'availability zone è invece una specifica zona fisica indipendente all'interno di quella region.
12. 
Ho notato che il portale è più semplice da usare ma nel lungo termine c'è il rischio di sbagliare l'inserimento dei dati, al contrario della CLI. Il portale è più efficace per esplorare proprietà non ancora note. La CLI è più efficace per ripetere controlli e selezionare esattamente i campi necessari.
13. 
I tag devono essere applicati in modo esplicito anche alle risorse perché non vengono ereditati automaticamente dal resource group alle risorse in esso create. 

## Parte C — Interpretazione tecnica

1. 
Sottoscrizione: la sottoscrizione è stata omessa
Resource Group: rg-cea-test
Provider: Microsoft.Storage
Tipo di risorsa: Microsoft.Storage/storageAccounts
Nome risorsa: stceatest01
2. 
Località: italynorth
Tag: environment=lab, unit=UD02
3. 
`az storage account show --name stceatest01 --resource-group rg-cea-test --output table`
4. 
`az group delete --name rg-cea-test --yes --no-wait`
5. 
`az group exists --name rg-cea-test`
