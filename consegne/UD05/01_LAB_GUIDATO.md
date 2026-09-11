# Consegna UD05 — Laboratorio guidato

## Piano di indirizzamento

| Elemento | CIDR | Scopo | Sovrapposizioni |
|---|---|---|---|
| VNet | `10.50.0.0/16` | Rappresenta lo spazio complessivo della subnet | |
| subnet web | `10.50.10.0/24` | È uno spazio più piccolo (/24) interno alla VNet | Non sovrapposto |
| subnet data | `10.50.20.0/24` | È uno spazio più piccolo (/24) interno alla VNet | Non sovrapposto |

Dopo aver verificato che le subnet non siano sovrapposte, creo il resource group `rg-cea-network-0e226c` con il comando `az group create`, la vnet `vnet-cea-0e226c` con `az network vnet create` e le subnet con `az network vnet subnet create`.
In più ho verificato con `az network vnet subnet list`
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

## NSG e associazioni

| NSG | Scope associato | Regola | Priorità | Origine | Porta | Esito |
|---|---|---|---:|---|---|---|
| nsg-data-0e226c | /subscriptions/<omitted>/resourceGroups/rg-cea-network-0e226c/providers/Microsoft.Network/networkSecurityGroups/nsg-data-0e226c | Allow-Web-Postgres | 300 | 10.50.10.0/24 | "*" | Succeeded |
| nsg-data-0e226c | /subscriptions/<omitted>/resourceGroups/rg-cea-network-0e226c/providers/Microsoft.Network/networkSecurityGroups/nsg-data-0e226c | Deny-Web-Postgres | 200 | 10.50.10.0/24 | "*" | Succeeded |

Ho creato l'NSG `nsg-data-0e226c` sul portale con tag `unit=UD05` e `environment=lab`. 
Ho creato la regola `Allow-Web-Postgres`. Verifico con `az network nsg rule show`.
```json
{
  "Access": "Allow",
  "Direction": "Inbound",
  "Port": "5432",
  "Priority": 300,
  "Protocol": "TCP",
  "Source": "10.50.10.0/24"
}
```
Associo il NSG alla subnet dati `snet-data` dal portale. Verifico:
```json
{
  "Nsg": "/subscriptions/<omitted>/resourceGroups/rg-cea-network-0e226c/providers/Microsoft.Network/networkSecurityGroups/nsg-data-0e226c",
  "Prefix": "10.50.20.0/24"
}
```
Creo una NIC da portale `nic-data-01` collegata al subnet `snet-data` e una da CLI `nic-web-01` collegata al subnet `snet-web`.
Verifico con `az network nic show`
```json
{
  "PrivateIp": "10.50.20.4",
  "PublicIp": null,
  "Subnet": "/subscriptions/<omitted>/resourceGroups/rg-cea-network-0e226c/providers/Microsoft.Network/virtualNetworks/vnet-cea-0e226c/subnets/snet-data"
}
```
```json
{
  "PrivateIp": "10.50.10.4",
  "PublicIp": null,
  "Subnet": "/subscriptions/<omitted>/resourceGroups/rg-cea-network-0e226c/providers/Microsoft.Network/virtualNetworks/vnet-cea-0e226c/subnets/snet-web"
}
```

Provo a verificare le nsg effettive e le rotte effettive: 
1. Il comando `az network nic list-effective-nsg` ritorna l'errore NicMustBeAttachedToRunningVmToGetEffectiveSecurityGroups
2. Il comando `az network nic show-effective-route-table` ritorna l'errore NicMustBeAttachedToRunningVmToGetEffectiveRoutes

Dopo creo una regola che crea un conflitto con quella precedente `Deny-Web-Postgres`. Verifico con il comando `az network nsg rule list` e la rimuovo.

## Verifica effettiva

- NIC create e subnet: NIC `nic-data-01` collegata alla subnet `snet-data` e NIC `nic-web-01` collegata alla subnet `snet-web` 
- NSG effettivi osservati: Non è stato possibile osservare gli NSG effettivi in quanto il NIC non è connesso a una VM
- route effettive osservate: Non è stato possibile osservare le route effettive in quanto il NIC non è connesso a una VM
- ciò che è stato verificato: Ho fatto una verifica preventiva su NSG e route.
- ciò che richiede ancora un workload: Il test di flusso reale, cioé
```text
10.50.10.0/24:any → 10.50.20.0/24:5432 TCP
```


## Costi e cleanup

Registra risorse create, possibili costi, eliminazione e verifica finale.

Abbiamo creato 1 Resource Group, 1 Virtual Network, 2 Subnet, 1 NSG e 2 NIC che non prevedeno costi se non collegate all'uso della VM.

Per il cleanup finale elimino la Resource Group e verifico.
```bash
az group delete --name "$LAB_RG" --yes --no-wait
az group wait --name "$LAB_RG" --deleted
az group exists --name "$LAB_RG"
```

## Rilevanza professionale

Spiega perché progettazione CIDR e controllo delle regole devono precedere il deployment dei workload.

Progettare preventivamente il CIDR e validare le regole di rete prima del deployment dei workload è importante. Infatti, definire subnet prive di sovrapposizioni, evita blocchi futuri e modificare i range con risorse attive comporta downtime non pianificati. Allo stesso modo, istanziare le regole degli NSG garantisce che le interfacce di rete nascano già confinate secondo il principio del minimo privilegio, evitando esposizione a traffico indesiderato durante il provisioning.
