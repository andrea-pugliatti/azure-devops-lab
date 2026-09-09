# Consegna UD03 — Verifica

## Parte A — Scelta singola

Per le domande 1–8 riporta risposta e motivazione.

1. B. L'autenticazione è riuscita, ma manca un'autorizzazione applicabile
L'accesso al portale conferma che l'identità è stata verificata (autenticazione), ma senza un'assegnazione di ruolo (autorizzazione) sullo scope del resource group non è possibile leggerne i contenuti.
2. D. Password
Una role assignment è formata esclusivamente da: principal + role definition + scope.
3. C. Reader sul resource group
Per la sola consultazione è sufficiente il ruolo Reader limitato allo scope del resource group.
4. B. ereditato
Nella gerarchia di Azure Resource Manager, i permessi concessi a un livello superiore (sottoscrizione) vengono automaticamente ereditati dai livelli inferiori (resource group e risorse).
5. B. Contributor
Il ruolo Contributor consente di creare e modificare risorse, ma non include il permesso necessario per assegnare ruoli ad altri.
6. C. impedisce l'eliminazione finché applicabile
Il lock CanNotDelete protegge la risorsa/scope dalla cancellazione accidentale o intenzionale; per eliminare il resource group è necessario prima rimuovere il lock.
7. B. genera una condizione di notifica, ma non costituisce un tetto automatico
I budget in Azure generano notifiche per monitorare la spesa, ma non arrestano i servizi né bloccano i consumi in modo automatico.
8. B. Microsoft Entra ID con ruolo appropriato
Gli utenti sono oggetti di directory gestiti all'interno del tenant Microsoft Entra ID, non tramite ruoli Azure RBAC.

## Parte B — Risposte brevi

9. 
L'assegnazione a un gruppo è preferibile per manutenibilità e sicurezza. Per concedere o revocare l'accesso a un collaboratore non serve ricreare o cancellare role assignment su più scope, basta aggiungerlo o rimuoverlo dal gruppo Entra. Permette anche di evitare che rimangano degli accessi orfani nel momento in cui un utente esce dall'organizzazione.
10. 
Il ruolo Microsoft Entra opera sul piano di controllo dell'identità, autorizzando la gestione degli oggetti della directory (utenti, gruppi, registrazioni di app, domini). Un esempio è l'User Administrator che può creare e gestire utenti e gruppi nel tenant Entra, ma non ha permessi per gestire risorse Azure.
Il ruolo Azure (Azure RBAC) opera tramite Azure Resource Manager sulle risorse ospitate nelle sottoscrizioni (reti, macchine virtuali, storage). Un esempio è il Contributor che può gestire e riavviare risorse all'interno di un resource group, ma non ha i permessi per creare o modificare account utente.
11. 
L'accesso effettivo sul resource group è Contributor. In Azure RBAC le autorizzazioni sono additive, cioè i ruoli RBAC aggiungono permessi e l'ereditarietà propaga i permessi verso il basso lungo la gerarchia, in questo caso da Sottoscrizione a Resource Group.
12. 
Per diagnosticare correttamente l'errore possiamo innanzitutto verificare quale utente sta eseguendo il comando con il comando `az account show --query user.name --output tsv`.
Si possono elencare le sottoscrizioni `az account list` per verificare che sia corretta.
Inoltre si possono verificare i ruoli correnti che l'utente possiede sullo scope con il comando `az role assignment list --scope "<NOME_SCOPE>" --include-inherited --output table`.
13. 
Un tag deleteAfter è un semplice metadato usato per inventario o automazioni esterne. Non impone alcun vincolo tecnico e le risorse contrassegnate possono essere eliminate o modificate in qualsiasi momento se l'utente ha i permessi RBAC.
Un lock CanNotDelete è un blocco gestionale applicato a uno scope o risorsa. Impedisce tecnicamente la cancellazione della risorsa a qualunque utente (inclusi i ruoli dotati di permessi) finché il blocco non viene esplicitamente rimosso.
Il budget definisce una soglia monetaria di spesa che genera avvisi e notifiche quando il consumo effettivo o previsto supera la percentuale impostata, ma non blocca l'infrastruttura.

## Parte C — Caso situazionale

14. 
C'è una violazione del principio del minimo privilegio. Assegnare il ruolo sull'intera sottoscrizione anziché limitarlo alla singola VNet o al resource group rg-network-prod espone inutilmente tutte le risorse della sottoscrizione. Per la sola consultazione, assegnare Contributor, che conferisce permessi completi di creazione, modifica e cancellazione delle risorse, è una concessione eccessiva.
Si è anche creata confusione tra permessi di gestione risorse e gestione degli accessi (RBAC).
Contributor è un ruolo con ampi poteri operativi sulle risorse ma non ha i permessi per assegnare ruoli ad altri utenti.
Un'altro errore è la mancata comprensione dei lock, il quale impedisce la cancellazione di una risorsa a qualunque utente finché non viene rimosso. Infatti tentando un'operazione di cleanup ottiene `ScopeLocked`.
15. 
Il ruolo più appropriato è Reader perché concede i soli permessi di lettura.
Lo scope più appropriato è la singola VNet (/subscriptions/.../resourceGroups/rg-network-prod/providers/Microsoft.Network/virtualNetworks/).
16. Perché non riesce ad assegnare il ruolo:
Il tecnico non riesce ad assegnare il ruolo perché possiede il ruolo Contributor. Questo ruolo permette di gestire le risorse ma non concede l'azione /roleAssignments/write che richiede ruoli specifici di amministrazione degli accessi, per esempio Owner.
L'errore ScopeLocked indica la presenza di un Resource Lock configurato direttamente sul Resource Group o ereditato dalla sottoscrizione. Finché il lock non viene rimosso l'eliminazione viene bloccata anche a chi possiede permessi di cancellazione.
