# Consegna UD03 — Laboratorio autonomo

## Analisi dell'accesso

| Principal anonimizzato | Ruolo | Scope | Origine | Accesso effettivo |
|---|---|---|---|---|
| User | Owner | /subscriptions/<omitted> | Ereditata | Il ruolo Owner è stato assegnato al livello superiore della Subscription | 
| Group | Reader | /subscriptions/<omitted>/resourceGroups/rg-finops-03b413 | Diretta | Il team deve consultare costi e risorse di un solo ambiente applicativo, ma non deve modificare risorse né gestire accessi. Reader è sufficiente per consultare lo scope e non permette di modificarne le risorse. Contributor o Owner non sono necessari. Essi hanno permessi completi di lettura, creazione, modifica ed eliminazione su tutte le risorse dello scope. In più Owner gestisce gli accessi. |
| User | Reader | /subscriptions/<omitted>/resourceGroups/rg-finops-03b413 | Ereditata | Il team deve consultare costi e risorse di un solo ambiente applicativo, ma non deve modificare risorse né gestire accessi. Reader è sufficiente per consultare lo scope e non permette di modificarne le risorse. Contributor o Owner non sono necessari. Essi hanno permessi completi di lettura, creazione, modifica ed eliminazione su tutte le risorse dello scope. In più Owner gestisce gli accessi. |


Per ricreare il nostro caso specifico vanno creati gli User per il team, va creato il gruppo Readers e vanno aggiunti gli User al gruppo. Poi va assegnato il ruolo Reader al gruppo. Controlliamo i ruoli con il comando `az role assignment list`.
L'User Owner è l'identità dell'amministratore. L'assegnazione è di tipo ereditato.
Il Group Reader è il principal registrato del Security Group di Entra ID. L'assegnazione è di tipo diretto.
Le utenze che fanno parte del gruppo non vengono visualizzate con `az role assignment list`. Queste sono di tipo ereditato.
L'assegnazione Reader temporanea non riduce privilegi più ampi già posseduti. Il modello RBAC di Azure è di tipo additivo. Ciò significa che i permessi concessi si sommano.
Il budget creato `budget-finops-03b413` è associato al Resource Group, ha un importo di 100 euro e un periodo di ripristino mensile e la soglia dell'90% del costo effettivo. Il destinatario inserito è l'email del team FinOps.
Il lock creato è di tipo `CanNotDelete`. Si può controllare la presenza del Lock tramite il comando `az lock list --resource-group rg-finops-03b413 --output table`. Si può verificare che la lettura sia consentita tramite `az group show --name rg-finops-03b413 --output table`. Si può verificare che l'operazione di eliminazione del resource group non è consentita con il comando `az group delete --name rg-finops-03b413 --yes --no-wait`.

## Diagnosi dei casi

Per ogni caso separa sintomo, causa probabile, verifica, correzione e risultato atteso.

Caso A
Sintomo: Please run 'az login' to setup account.
Causa probabile: Nella sessione locale della CLI di Azure non è presente alcun token di identità valido. Il token potrebbe essere scaduto, invalidato per timeout, revocato dopo un cambio credenziali, oppure è stato eseguito un az logout. Azure Resource Manager (ARM) non sa chi stia effettuando la richiesta.
Verifica: `az account show --output table` Se non si è autenticati, il comando fallisce restituendo lo stesso errore o indicando l'assenza di subscription attive.
Correzione: Eseguire il comando di autenticazione `az login` oppure `az login --use-device-code`.

Caso B
Sintomo: AuthorizationFailed ... Microsoft.Authorization/roleAssignments/write ...
Causa probabile: L'utente è autenticato, ma la sua identità non possiede i privilegi RBAC necessari per compiere l'azione richiesta sullo scope di destinazione. Nello specifico, l'operazione richiede il permesso Microsoft.Authorization/roleAssignments/write. L'utente corrente ha probabilmente un ruolo come Contributor o Reader.
Verifica: Verificare quale utente sta eseguendo il comando con il comando `az account show --query user.name --output tsv` e verificare ruoli correnti possiede l'utente sullo scope con il comando `az role assignment list --scope /subscriptions/<omitted>/resourceGroups/rg-finops-03b413 --include-inherited --output table`.
Correzione: Per gestire le assegnazioni di ruolo si può assegnare allo scope specifico il ruolo di User Access Administrator. Si può elevare l'utente a Owner ma non è necessario per l'operazione richiesta.

Caso C
Sintomo: ScopeLocked: The scope is locked and can't be deleted.
Causa probabile: L'utente è autenticato e dispone dei permessi per cancellare la risorsa, ma l'operazione di eliminazione viene bloccata da un vincolo di tipo CanNotDelete (o ReadOnly) applicato al Resource Group o a un livello superiore. I lock hanno la precedenza sui permessi RBAC.
Verifica: Individuare il lock applicato con il comando `az lock list --resource-group rg-finops-03b413 --output table`.
Correzione: Rimuovere il lock prima di lanciare la cancellazione con il comando `az lock delete --name lock-finops-delete --resource-group rg-finops-03b413`. Una volta rimosso il vincolo, procedere con l'eliminazione della risorsa o del resource group.

## Budget, lock e cleanup

Spiega funzione e limiti dei tre controlli e documenta l'ordine di rimozione degli oggetti temporanei.

L'Autenticazione verifica l'identità del soggetto (utente, gruppo, service principal, managed identity) tramite Microsoft Entra ID. Questo meccanismo certifica chi sta effettuando la chiamata ad Azure Resource Manager (ARM) ma non autorizza alcuna azione. Essere autenticati con successo significa solo che Azure riconosce chi sei, non che tu abbia il diritto di vedere o creare risorse.
L'Autorizzazione definisce cosa un'identità autenticata può fare su uno scope. Concede i permessi minimi necessari tramite definizioni di ruolo. Il modello è esclusivamente additivo (Allow-based) e i normali ruoli non consentono di negare selettivamente un'azione se questa è già stata ereditata da uno scope superiore. Inoltre non previene l'errore umano e un utente Owner o Contributor può comunque distruggere o alterare risorse per distrazione.
I Resource Locks introducono un vincolo strutturale a livello di ARM che blocca l'eliminazione (CanNotDelete) o la modifica (ReadOnly) delle risorse, a prescindere dal livello di privilegi dell'utente. Proteggono l'integrità dell'ambiente contro cancellazioni accidentali ma non sono un controllo di sicurezza. Niente impedisce a un utente o a un amministratore di rimuovere prima il lock e poi cancellare la risorsa.

Ordine di rimozione degli oggetti temporanei

Si parte necessariamente con la rimozione del lock di risorsa. Se il lock CanNotDelete rimane attivo, qualsiasi tentativo di cancellare il Resource Group o le risorse al suo interno fallirà con errore ScopeLocked. Utilizziamo il comando 
```bash
az lock delete --name lock-finops-delete --resource-group rg-finops-03b413
```
Procediamo poi alla pulizia delle Identità in Microsoft Entra ID. Una volta eliminate le eventuali dipendenze lato Azure Resource Manager, si ripulisce la directory. Prima si rimuovono gli utenti di test, poi si elimina il gruppo di sicurezza temporaneo.  
Infine si rimuove il Resource Group.

## Risultato finale

- output anonimizzati utilizzati:
- cleanup verificato:
- hash abbreviato e messaggio del commit:
