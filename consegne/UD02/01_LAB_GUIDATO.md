# Consegna UD02 — Laboratorio guidato

## Contesto verificato

- Azure Portal accessibile: Si
- Azure CLI autenticata: Si
- sottoscrizione corretta verificata senza pubblicarne l'ID: Si
- località scelta e motivo: `italynorth`, in quanto disponibile

## Ambiente creato

| Elemento | Nome tecnico | Tipo | Località | Scopo |
|---|---|---|---|---|
| Resource group | rg-cea-ud02-5d62e4df | Microsoft.Resources/resourceGroups | italynorth | Un Resource Group (Gruppo di risorse) è un contenitore logico progettato per aggregare, organizzare e gestire le risorse cloud (macchine virtuali, database, reti virtuali, storage account) che condividono lo stesso ciclo di vita. |
| Rete virtuale | vnet-cea-ud02 | Microsoft.Network/virtualNetworks | italynorth | È lìequivalente di una rete locale. Lo scopo è quello di fornire un perimetro logico isolato in cui eseguire le risorse e gestire il traffico di rete. |
| Storage account | stceae7160ccc | Microsoft.Storage/storageAccounts | italynorth | Lo scopo principale è fornire una piattaforma unificata per memorizzare i dati senza doversi preoccupare dell'infrastruttura fisica sottostante. |

## Decisioni e verifiche

Descrivi perché le risorse appartengono allo stesso resource group, quali responsabilità rimangono al cliente, perché sono stati applicati nomi e tag e quale differenza hai osservato tra portale e CLI. Riporta soltanto comandi essenziali e output anonimizzati.

Le risorse vengono inserite tutte nello stesso resourse group perché sono create, girano e vengono decommissionate insieme. Un resource group delimita i componenti che cooperano per erogare un unico servizio. Se il servizio o l'ambiente viene dismesso, l'eliminazione del Resource Group rimuove contestualmente tutti i componenti associati, evitando risorse orfane.
Anche delegando l'infrastruttura hardware e l'ipervisore a Microsoft, ricadono interamente sotto la responsabilità del cliente la gestione dei dati, la configurazione degli account e permessi, la configurazione della rete, la gestione dei segreti e la gestione del sistema operativo.
Le convenzioni usate per nomi e tag permettono di risalire molto velocemente al tipo di risorsa, progetto e ambiente ed evitano collisioni sui nomi globali.
Per quanto riguarda le differenze tra portale e CLI, ho notato che il portale è più semplice da usare ma nel lungo termine c'è il rischio di sbagliare l'inserimento dei dati, al contrario della CLI. Inoltre nella guida uno dei comandi era errato, per poter cercare il nome corretto per effettuare la query, ho trovato più semplice visitare il portale.

## Cleanup

- operazione di eliminazione:
```bash
az group delete \
  --name "$LAB_RG" \
  --yes \
  --no-wait
```
- controllo utilizzato:
```bash
az group exists --name "$LAB_RG"
```
- risultato finale:
false
- eventuale anomalia e soluzione:

## Rilevanza professionale

Spiega come inventario, tag e verifica del cleanup rendono una procedura ripetibile e controllabile.

L'integrazione di inventario, tag e verifica del cleanup rende una procedura ripetibile e controllabile inserendola nella sequenza operativa 
`inventario → creazione → verifica → evidenza → eliminazione → verifica dell'eliminazione`, garantendo piena visibilità e l'assenza di residui tra un'esecuzione e l'altra. 
I tag facilitano l'inventario e la governance, permettendo di identificare ogni risorsa associata al resource group. La successiva verifica del cleanup certifica che Azure abbia effettivamente rimosso il resource group e tutti i componenti correlati anziché limitarsi all'invio del comando di cancellazione, impedendo che risorse orfane continuino a generare costi o a causare conflitti e garantendo il ripristino di uno stato di partenza certo per qualsiasi deployment successivo.
