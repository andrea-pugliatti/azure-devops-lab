# Consegna UD03 — Laboratorio guidato

## Contesto anonimizzato

- sottoscrizione e tenant verificati: Sì
- percorso Entra eseguito: A
- resource group temporaneo: rg-cea-identity-d2109c

## Identità e assegnazione RBAC

| Principal anonimizzato | Ruolo | Scope | Diretta/ereditata | Motivo |
|---|---|---|---|---|
| User | Owner | /subscriptions/<omitted> | Ereditata | Il ruolo Owner è stato assegnato al livello superiore della Subscription |
| Group | Reader | /subscriptions/<omitted>/resourceGroups/rg-cea-identity-d2109c | Diretta | Reader è sufficiente per consultare lo scope e non permette di modificarne le risorse |
| User | Reader | /subscriptions/<omitted>/resourceGroups/rg-cea-identity-d2109c | Ereditata | L'utente cea-lab-d2109c fa parte del gruppo |

Descrivi gli oggetti letti o creati, l'autorizzazione necessaria e l'accesso effettivo osservato.

L'User Owner è l'identità dell'amministratore. L'assegnazione è di tipo ereditato.
Il Group Reader è il principal registrato del Security Group di Entra ID. L'assegnazione è di tipo diretto.
Le utenze che fanno parte del gruppo non vengono visualizzate con il comando `az role assignment list`.

Ho creato il resource group tramite `az group create`
Ho creato l'User cea-lab-d2109c e l'ho aggiunto al gruppo grp-cea-readers-d2109c
Ho assegnato il ruolo Reader al gruppo grp-cea-readers-d2109c. Ho controllato l'assegnazione sia da portale che da CLI con `az role assignment list`.
Ho creato il budget `budget-cea-d2109c` per il resource group.
Ho creato il lock sul portale e verificato tramite `az lock list` (lock-cea-delete  CanNotDelete  Lock temporaneo).
Provando a eliminare il resource group ritorna il codice ScopeLocked.

## Governance e costi

- tag e significato:
- lock e operazione impedita: `CanNotDelete`, operazione di eliminazione.
- stato di Cost Analysis: Il resource group non mostra nulla.
- budget creato o limitazione documentata: nome budget-cea-d2109c, budget 150 euro, soglia 80%.
- motivo per cui il budget non blocca la spesa: Perché il budget funge solo da strumento di monitoraggio e soglia di avviso.

## Cleanup

Registra rimozione degli oggetti temporanei e verifica finale del resource group.

## Rilevanza professionale

Spiega come distinguere autenticazione, autorizzazione RBAC e blocco di governance.

L'autenticazione verifica l'identità che invia la richiesta. L'autorizzazione specifica i permessi dell'identità rispetto a una risorsa specifica. Il blocco di governance funge da salvaguardia della risorsa o dello scope stesso.
