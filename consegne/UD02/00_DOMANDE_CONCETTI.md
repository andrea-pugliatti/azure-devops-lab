# Consegna UD02 — Domande sui concetti

Per ciascuna domanda 1–8 riporta una risposta motivata.

1. Una macchina virtuale (IaaS) richiede che il cliente amministri direttamente il sistema operativo, l'aggiornamento e la configurazione del software. Con Azure App Service, Microsoft gestisce interamente l'infrastruttura sottostante, lasciando al cliente la responsabilità dei dati, delle configurazioni del servizio e delle identità/accessi.
2. Tenant Microsoft Entra è chi gestisce le identità e gli accessi dell'organizzazione. La sottoscrizione risiede nel tenant e definisce il confine di fatturazione, quote e limiti amministrativi. Il Resource group è contenitore logico all'interno della sottoscrizione per raggruppare risorse che condividono ciclo di vita e governance.
3. La località assegnata al resource group indica esclusivamente l'area geografica in cui Azure memorizza i metadati del gruppo stesso e non vincola l'infrastruttura fisica sottostante che potrebbe avere dei rechiusiti diversi.
4. Una region è un perimetro geografico definito che ospita uno o più datacenter. Un'availability zone è invece una specifica zona fisica indipendente all'interno di quella region.
5. `az account show` serve a verificare quale sia la sottoscrizione attiva. Eseguire il comando ci permette di evitare di creare risorse nel perimetro di fatturazione sbagliato.
6.  I tag sono semplici metadati ideati per inventario, automazione operativa e tracciamento dei costi. Non sono un meccanismo di sicurezza. Inoltre, i tag sono visibili in chiaro e non devono mai contenere segreti o dati sensibili.
7. Azure CLI garantisce ripetibilità, verificabilità e automazione.
8. Lanciare il comando di eliminazione certifica soltanto che Azure ha preso in carico la richiesta, non che il processo sia andato a buon fine.
