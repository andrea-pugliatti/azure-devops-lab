# Consegna UD05 — Laboratorio autonomo

## Inventario iniziale

Riporta VNet, subnet, NIC, associazioni e regole in forma anonimizzata.

Riutilizzo il Resource Group `rg-cea-network-0e226c`, la VNet `vnet-cea-0e226c` e le Subnet `snet-web` e `snet-data`.
VNet `vnet-cea-0e226c` CIDR `10.50.0.0/16`
Subnet web `snet-web` CIDR `10.50.10.0/24`
Subnet data `snet-data` CIDR `10.50.20.0/24`

```json
[
  {
    "addressPrefix": "10.50.10.0/24",
    "defaultOutboundAccess": false,
    "delegations": [],
    "etag": "W/\"53e6dea4-2d2d-46d3-bded-bc86f86b20ab\"",
    "id": "/subscriptions/<omitted>/resourceGroups/rg-cea-network-0e226c/providers/Microsoft.Network/virtualNetworks/vnet-cea-0e226c/subnets/snet-web",
    "name": "snet-web",
    "privateEndpointNetworkPolicies": "Disabled",
    "privateLinkServiceNetworkPolicies": "Enabled",
    "provisioningState": "Succeeded",
    "resourceGroup": "rg-cea-network-0e226c",
    "type": "Microsoft.Network/virtualNetworks/subnets"
  },
  {
    "addressPrefix": "10.50.20.0/24",
    "delegations": [],
    "etag": "W/\"53e6dea4-2d2d-46d3-bded-bc86f86b20ab\"",
    "id": "/subscriptions/<omitted>/resourceGroups/rg-cea-network-0e226c/providers/Microsoft.Network/virtualNetworks/vnet-cea-0e226c/subnets/snet-data",
    "name": "snet-data",
    "privateEndpointNetworkPolicies": "Disabled",
    "privateLinkServiceNetworkPolicies": "Enabled",
    "provisioningState": "Succeeded",
    "resourceGroup": "rg-cea-network-0e226c",
    "type": "Microsoft.Network/virtualNetworks/subnets"
  }
]
```

Riutilizzo NSG `nsg-data-0e226c` e i NIC `nic-data-01` collegata al subnet `snet-data` e `nic-web-01` collegata al subnet `snet-web`.
Riutilizzo la regola `Allow-Web-Postgres` e aggiungo la regola `Deny-Web-Postgres-Auto`.
Verifico con `az network nsg show`:
```json
{
  "id": "/subscriptions/<omitted>/resourceGroups/rg-cea-network-0e226c/providers/Microsoft.Network/networkSecurityGroups/nsg-data-0e226c",
  "location": "italynorth",
  "name": "nsg-data-0e226c",
  "resourceGroup": "rg-cea-network-0e226c",
  "securityRules": [
    {
      "access": "Allow",
      "destinationAddressPrefix": "*",
      "destinationAddressPrefixes": [],
      "destinationPortRange": "5432",
      "destinationPortRanges": [],
      "direction": "Inbound",
      "etag": "W/\"9df07b06-9008-44ce-a77a-22d01c407dbb\"",
      "id": "/subscriptions/<omitted>/resourceGroups/rg-cea-network-0e226c/providers/Microsoft.Network/networkSecurityGroups/nsg-data-0e226c/securityRules/Allow-Web-Postgres",
      "name": "Allow-Web-Postgres",
      "priority": 300,
      "protocol": "TCP",
      "provisioningState": "Succeeded",
      "resourceGroup": "rg-cea-network-0e226c",
      "sourceAddressPrefix": "10.50.10.0/24",
      "sourceAddressPrefixes": [],
      "sourcePortRange": "*",
      "sourcePortRanges": [],
      "type": "Microsoft.Network/networkSecurityGroups/securityRules"
    },
    {
      "access": "Deny",
      "destinationAddressPrefix": "*",
      "destinationAddressPrefixes": [],
      "destinationPortRange": "5432",
      "destinationPortRanges": [],
      "direction": "Inbound",
      "etag": "W/\"9df07b06-9008-44ce-a77a-22d01c407dbb\"",
      "id": "/subscriptions/<omitted>/resourceGroups/rg-cea-network-0e226c/providers/Microsoft.Network/networkSecurityGroups/nsg-data-0e226c/securityRules/Deny-Web-Postgres-Auto",
      "name": "Deny-Web-Postgres-Auto",
      "priority": 250,
      "protocol": "TCP",
      "provisioningState": "Succeeded",
      "resourceGroup": "rg-cea-network-0e226c",
      "sourceAddressPrefix": "10.50.10.0/24",
      "sourceAddressPrefixes": [],
      "sourcePortRange": "*",
      "sourcePortRanges": [],
      "type": "Microsoft.Network/networkSecurityGroups/securityRules"
    }
  ],
  "tags": {
    "environment": "lab",
    "unit": "UD05"
  }
}
```

## Guasto e diagnosi

- regola introdotta: `Deny-Web-Postgres-Auto`
- ordine di priorità osservato: `Deny-Web-Postgres-Auto` -> `Allow-Web-Postgres`
- sintomo: Non è possibile connettersi tramite la porta 5432
- ipotesi: Conflitto di rotte
- controllo: Verifica le nsg effettive e le rotte effettive.
Le regole effettive sono legate alla NIC, non alla VM. Quindi è possibile usare il comando `az network nic list-effective-nsg`.
Nel mio caso non è stato possibile per via di errori NicMustBeAttachedToRunningVmToGetEffectiveSecurityGroups e NicMustBeAttachedToRunningVmToGetEffectiveRoutes
- correzione minima: Rimozione regola incoerente tramite il comando:
```bash
az network nsg rule delete \
  --resource-group "$LAB_RG" \
  --nsg-name "$LAB_NSG" \
  --name Deny-Web-Postgres-Auto
```
- verifica dopo la correzione: Per verificare che la regola sia stata eliminata uso il comando:
```bash
az network nsg rule list \
  --resource-group "$LAB_RG" \
  --nsg-name "$LAB_NSG" \
  --query "sort_by([].{Priority:priority,Name:name,Access:access,Source:sourceAddressPrefix,Port:destinationPortRange}, &Priority)" \
  --output table
```

## Casi ulteriori

Distingui diagnosi di CIDR, DNS, routing, NSG e servizio non in ascolto, separando fatti osservati e prove non ancora eseguibili.

1. Sintomo: InvalidAddressPrefix
Livello: VNet
Controllo: Verificare che la stringa CIDR passata nel comando CLI sia valida.
Correzione minima: Adatto la maschera in modo che il prefisso ricada interamente all'interno dello spazio della VNet.
2. Sintomo: SecurityRuleConflict
Livello: NSG
Controllo: Ispezionare l'elenco delle regole di sicurezza già presenti all'interno dell'NSG per individuare duplicati di priorità numerica o collisioni sul nome della regola.
Correzione minima: Modifico la priorità della nuova regola assegnando un valore valido non ancora utilizzato da nessun'altra regola all'interno di quell'NSG.
3. Sintomo: un nome DNS non viene risolto
Livello: DNS
Controllo: Verificare se la Virtual Network se sono impostati server DNS custom non raggiungibili:
Correzione minima: Rimuovere le impostazioni personalizzate.
4. Sintomo: la porta risulta filtrata da un NSG
Livello: NSG
Controllo: Verifico le regole effettive per la NIC e identifico la prima regola ordinata di priorità che agisce sulla porta in oggetto.
Correzione minima: Elimino una regola Deny indesiderata oppure abbasso il valore di priorità della regola Allow.

## Cleanup e risultato finale

- regola autonoma rimossa: Sì con il comando:
```bash
az network nsg rule delete \
  --resource-group "$LAB_RG" \
  --nsg-name "$LAB_NSG" \
  --name Deny-Web-Postgres-Auto
```
- cleanup verificato: Sì il comando:
```bash
az group delete --name "$LAB_RG" --yes --no-wait
az group wait --name "$LAB_RG" --deleted
az group exists --name "$LAB_RG"
```
restituisce `false`.
- hash abbreviato e messaggio del commit:

