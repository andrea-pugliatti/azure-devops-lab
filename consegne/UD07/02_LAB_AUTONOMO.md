# UD07 — Consegna laboratorio autonomo

## 1. Script CLI idempotente

- logica:
```sh
#!/usr/bin/env bash

RG="rg-ud07-auto"
LOCATION="westeurope"
EXISTS=$(az group exists --name "$RG")

if [ "$EXISTS" = "false" ]; then
  echo "Il Resource Group non esiste: lo creo."
  az group create \
    --name "$RG" \
    --location "$LOCATION" \
    --tags ManagedBy=Autonomo UD=07 \
    --output table
else
  echo "Il Resource Group esiste già: lo riutilizzo."
  az group update \
    --name "$RG" \
    --set tags.ManagedBy=Autonomo tags.UD=07 \
    --output none
fi

echo
echo "Stato finale:"
az group show \
  --name "$RG" \
  --query "{Name:name,Location:location,State:properties.provisioningState,Tags:tags}" \
  --output table
```
- prima esecuzione:
```
Il Resource Group non esiste: lo creo.
Location    Name
----------  ------------
westeurope  rg-ud07-auto

Stato finale:
Name          Location    State
------------  ----------  ---------
rg-ud07-auto  westeurope  Succeeded
```
- seconda esecuzione:
```
Il Resource Group esiste già: lo riutilizzo.

Stato finale:
Name          Location    State
------------  ----------  ---------
rg-ud07-auto  westeurope  Succeeded
```
- verifica: 
Creo delle variabili con i valori del rg, location e il test se il rg esiste.
Se il rg non esiste lo creo con il comando `az group create`, sennò aggiorno soltanto i tag con `az group update`.
Stampo lo stato finale del resource group con il comando `az group show`

## 2. PowerShell equivalente

- controllo esistenza:
```powershell
$RgName = "rg-ud07-auto-ps"
$Location = "westeurope"

$rg = Get-AzResourceGroup `
  -Name $RgName `
  -ErrorAction SilentlyContinue
```

- modifica:
```powershell
if (-not $rg) {
    $rg = New-AzResourceGroup `
      -Name $RgName `
      -Location $Location
}

Update-AzTag `
  -ResourceId $rg.ResourceId `
  -Tag @{ ManagedBy="PowerShell"; UD="07" } `
  -Operation Merge | Out-Null
```

- output:
```powershell
Get-AzResourceGroup `
  -Name $RgName |
  Select-Object ResourceGroupName, Location, ProvisioningState, Tags
```

## 3. Activity Log

- operazione: Update resource group
- status: Succeeded
- timestamp: 2026-09-15T13:44:00.3928874Z

## 4. KQL
```sh
az monitor log-analytics query \
  --workspace "$LAW_CUSTOMER_ID" \
  --analytics-query "datatable(
    Component:string,
    Status:string,
    DurationMs:int
  )[
    'WEB','OK',120,
    'API','OK',180,
    'DB','WARN',430,
    'API','WARN',510
  ]
  | summarize
      Requests=count(),
      AvgDuration=avg(DurationMs)
    by Status
  | sort by AvgDuration desc" \
  --output table
```

1. 2
2. WARN
3. Perché comprime i singoli record raggruppandoli per Status, eliminando i dettagli delle colonne non aggregate.

## 5. Metrics

Metriche possibili:
UsedCapacity
Transactions
Ingress
Egress
SuccessServerLatency
SuccessE2ELatency
Availability
ReplicationLagSeconds
MigrationProgress

- metrica: Availability
- unità: Percent
- aggregazione: Average
- dato presente: 100.0
- interpretazione: La capacità disponibile è al 100%

## 6. Alert

- scope: `/subscriptions/<omitted>/resourceGroups/rg-ud07-monitor/providers/Microsoft.Storage/storageAccounts/stud0789469917`
- condition: Total Transactions Greater Than 0
- severity: 3 - Informational
- evaluation frequency: PT5M
- Action Group: ag-ud07
- Enabled vs Fired:
Una regola è Enabled quando è configurata ed abilitata ed è in grado di valutare periodicamente la condizione impostata. Diventa Fired esclusivamente quando la query di valutazione rileva che i dati hanno superato la soglia configurata nella finestra di osservazione stabilita.

## 7. Guasto amministrativo

- sintomo: Il Resource Group non viene trovato
- errore: ResourceGroupNotFound
- ipotesi: Il Resource Group non esiste
- controllo: `az group exists --name rg-ud07-NON-ESISTE` ritorna false
- correzione: Se ho sbagliato a scrivere il nome del rg lo cambio, sennò creo il rg.
- verifica: `az group exists --name rg-ud07-NON-ESISTE`

## 8. Runbook

### Sintomo
Chiamate CLI, PowerShell o script falliscono con errore ResourceGroupNotFound o ResourceNotFound.

### Contesto
Accertare la sottoscrizione di lavoro attiva:
`az account show --query "{SubscriptionId:id, Name:name, TenantId:tenantId}" --output table`

### Controlli
Confronto maiuscole/minuscole e caratteri speciali nel nome.
Controllo che la risorsa esista: per esempio il resourse group `az group exists --name <NOME>`

### Comandi
Effettuo i comandi `az group exists --name <NOME>` 
e `az group list --query "[].name" --output table`

### Interpretazione
Se il comando exists ritorna true allora il RG esiste bisogna verificare che la subscription sia giusta. Se ritorna false il nome è errato oppure il RG non esiste nella subscription corrente.
Con `az group list` controlliamo quali RG sono presenti.

### Correzione minima
Cambiamo il nome del RG in quello corretto oppure selezionamo la subscription corretta.

### Verifica
Interroghiamo di nuovo la risorsa: `az group show --name "<NOME>" --output table`

### Cleanup
Rimuovere eventuali variabili di ambiente.

## 9. Cleanup

- rg-ud07-auto: `az group exists --name rg-ud07-auto` ritorna false
- rg-ud07-auto-ps: `az group exists --name rg-ud07-auto-ps` ritorna false
