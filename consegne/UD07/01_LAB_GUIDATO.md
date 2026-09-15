# UD07 — Consegna laboratorio guidato

## CLI

- prima esecuzione script:
```
Il Resource Group non esiste: lo creo.
Location    Name
----------  ----------------
westeurope  rg-ud07-cli-test

Aggiorno i tag...

Stato finale:
Name              Location    State
----------------  ----------  ---------
rg-ud07-cli-test  westeurope  Succeeded
```
- seconda esecuzione:
```
Il Resource Group esiste già: lo riutilizzo.

Aggiorno i tag...

Stato finale:
Name              Location    State
----------------  ----------  ---------
rg-ud07-cli-test  westeurope  Succeeded
```
- comportamento idempotente: Questo è un esempio di idempotenza. Rieseguire la procedura non provoca la creazione di un nuovo resource group, ci ritorna lo stesso già creato. È più sicuro perché non rischiamo di avere comportamenti inattesi e costi nascosti.
- esempio JMESPath: `{Name:name,Location:location,State:properties.provisioningState}`
- quando usare `tsv`: Quando dobbiamo recuperare un singolo valore da riutilizzare in un comando successivo, `tsv` ce lo restituisce senza virgolette al contrario di `json`.

## PowerShell

- `Get-AzContext` verificato:
```
   Tenant: <omitted>

SubscriptionName     SubscriptionId Account   Environment
----------------     -------------- -------   -----------
Azure subscription 1 <omitted>      MSI@50342 AzureCloud
```
- Resource Group test:
- prima esecuzione:
Viene creato il resource group
```
ResourceGroupName Location   ProvisioningState Tags
----------------- --------   ----------------- ----
rg-ud07-ps-test   westeurope Succeeded         {[State, Verified], [ManagedBy, PowerShell], [UD, 07]}
```
- seconda esecuzione:
Viene riutilizzato il medesimo
```
ResourceGroupName  Location   ProvisioningState Tags
-----------------  --------   ----------------- ----
rg-ud07-ps-test    westeurope Succeeded         {[State, Verified], [ManagedBy, PowerShell], [UD, 07]}
```
- perché il controllo `if` è utile: Ci permette di creare il resource group a condizione che esso non esista.

## Log Analytics

- workspace:
```
CreatedDate                   CustomerId  Location    ModifiedDate                  Name            ProvisioningState    PublicNetworkAccessForIngestion    PublicNetworkAccessForQuery    ResourceGroup    RetentionInDays
----------------------------  ----------  ----------  ----------------------------  --------------  -------------------  ---------------------------------  -----------------------------  ---------------  -----------------
2026-09-15T12:31:29.5535556Z  <omitted>   italynorth  2026-09-15T12:31:45.1187985Z  law-ud07-26242  Succeeded            Enabled                            Enabled                        rg-ud07-monitor  30
```
- regione: italynorth
- query `print`:
```
Course    Status    TableName      UD
--------  --------  -------------  ----
AZ-104    OK        PrimaryResult  7
```
- query `datatable`:
```
Count    Status    TableName
-------  --------  -------------
2        OK        PrimaryResult
1        WARN      PrimaryResult
```
- risultato sintetico: L'operatore summarize ha raggruppato correttamente i 3 record per Status ottenendo 2 OK e 1 WARN.

## Activity Log

- evento osservato: Update resource group
- status: Succeeded
- timestamp: 2026-09-15T12:40:34.8469725Z
- dati personali omessi: sì

## Diagnostic Setting

- esito: 
Diagnostic setting creato:
```
Name                  Workspace
--------------------  ----------------------------------------------------------------------------
ud07-activity-to-law  /subscriptions/<omitted>/resourceGroups/rg-ud07-monitor/providers/Microsoft.OperationalInsights/workspaces/law-ud07-26242
```
- destinazione: Log Analytics workspace `law-ud07-26242`
- AzureActivity disponibile: no, AzureActivity non ancora popolata nel time range osservato.
- fallback usato, se necessario:

## Metrics

- Storage Account:
```
AccessTier    AllowBlobPublicAccess    CreationTime                      EnableHttpsTrafficOnly    Kind       Location    MinimumTlsVersion    Name            PrimaryLocation    ProvisioningState    ResourceGroup    StatusOfPrimary
------------  -----------------------  --------------------------------  ------------------------  ---------  ----------  -------------------  --------------  -----------------  -------------------  ---------------  -----------------
Hot           False                    2026-09-15T12:51:49.104607+00:00  True                      StorageV2  italynorth  TLS1_0               stud0789469917  italynorth         Succeeded            rg-ud07-monitor  available
```
- metrica: UsedCapacity
- unità: Bytes
- aggregazione: Average
- punto dati disponibile: no, metrica supportata, campione non ancora disponibile
- interpretazione: UsedCapacity mostra lo spazio occupato in un intervallo di tempo.

- metrica: Transactions
- unità: Count
- aggregazione: Total
- punto dati disponibile: no, metrica supportata, campione non ancora disponibile
- interpretazione: Transactions mostra il numero di transazioni in un intervallo di tempo.

## Alert

- nome: ag-ud07
- scope corretto: sì (storage account)
- condition: Total Transactions Greater than 0
- severity: 3 - Informational
- Action Group: presente
- perché non è necessario che sia Fired: L'importante è che sia `Enabled`, cioé che la regola è attiva e può essere valutata. `Fired` significa che la condizione è stata effettivamente soddisfatta che non è importante nel momento in un cui la si sta solo configurando.

## Correlazione

- modifica osservata: Aggiornamento dei metadati dello Storage Account con applicazione del tag State=Changed
- evento Activity Log:
```
Time                          Operation                      Status
----------------------------  -----------------------------  ---------
2026-09-15T12:52:09.6408056Z  Create/Update Storage Account  Succeeded
2026-09-15T12:51:51.5695545Z  Create/Update Storage Account  Accepted
2026-09-15T12:51:48.7883053Z  Create/Update Storage Account  Started
```
- la correlazione prova causalità?: No.
- motivazione: La successione tra l'evento e la ricezione dell'alert dimostra solo una correlazione cronologica. Questo non dimostra automaticamente che un cambiamento di metrica sia stato causato dall'aggiornamento dei metadati. Vanno effettuati altri controlli.

## Cleanup

- diagnostic setting rimossa: Sì `az group exists --name rg-ud07-auto` ritorna false
- RG CLI test eliminato: Sì `az group exists --name rg-ud07-cli-test` ritorna false
- RG PowerShell test eliminato: Sì `az group exists --name rg-ud07-ps-test` ritorna false
- RG principale eliminato: Sì `az group exists --name rg-ud07-auto` ritorna false
