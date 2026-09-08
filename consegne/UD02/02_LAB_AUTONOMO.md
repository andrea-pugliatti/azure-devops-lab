# Consegna UD02 — Laboratorio autonomo

Tutte le risorse sono state aggregate nel medesimo Resource Group affinché ne condividano rigorosamente il ciclo di vita, garantendo che provisioning, riconfigurazione e dismissione avvengano in modo coordinato e privo di componenti orfane. 
La scelta della regione geografica risponde a precisi requisiti del cliente. La Virtual Network definisce l'infrastruttura di connettività privata, l'indirizzamento IP e le regole di filtraggio del traffico, mentre lo Storage Account opera come servizio dati serverless e resiliente dedicato alla persistenza scalabile di file e oggetti non strutturati. 
L'adozione dei tag facilita la tracciabilità delle proprietà e una puntuale ripartizione dei costi nei report di fatturazione. L'efficacia del cleanup finale sarà infine attestata tramite la cancellazione del Resource Group, verificando via riga di comando che una query mirata sulle risorse e sul gruppo restituisca un esito nullo, a conferma del completo azzeramento dei costi e dell'assenza di residui orfani nella sottoscrizione.

## Requisito e piano

- requisito interpretato: Creazione di una rete isolata e uno spazio di archiviazione vuoto.
- risorse previste: Resource Group, Virtual Network con Subnet, Storage Account.
- nomi e tag scelti: 
    RG: rg-cea-ud02-auto-3a5a3394
    VNet: vnet-cea-auto
    Subnet: snet-workload
    Storage: stceaauto3a5a3394
    `course=cloud-engineer-academy`
    `unit=UD02`
    `environment=dev`
    `scenario=autonomous`
    `deleteAfter=2026-09-10`
- verifiche preliminari:
    Per il controllo di Azure CLI: `az version`
    Per il controllo della sottoscrizione:
```bash
    az account show \
        --query "{Name:name,State:state,IsDefault:isDefault}" \
        --output table
```
    Per il controllo dei provider necessari:
```bash
        az provider show \
        --namespace Microsoft.Network \
        --query "{Namespace:namespace,State:registrationState}" \
        --output table

        az provider show \
            --namespace Microsoft.Storage \
            --query "{Namespace:namespace,State:registrationState}" \
            --output table
```


## Svolgimento

Riporta i passaggi essenziali, i controlli da portale e CLI e gli output anonimizzati che dimostrano il risultato.

Dopo aver fatto le verifiche preliminare e alla preparazione delle variabili, passo alla creazione del Resource Group sul portale. Clicco su 'Create', inserisco il nome e seleziono la regione. Inserisco i tag e poi clicco su Review and Create.
Per il controllo inserisco su CLI:
```bash
az group show \
  --name "$AUTO_RG" \
  --query "{Name:name,Location:location,State:properties.provisioningState,Tags:tags}" \
  --output jsonc
```
Ritorna:
```json
{
  "Location": "italynorth",
  "Name": "rg-cea-ud02-auto-3a5a3394",
  "State": "Succeeded",
  "Tags": {
    "course": "cloud-engineer-academy",
    "deleteAfter": "2026-09-10",
    "environment": "dev",
    "scenario": "autonomous",
    "unit": "UD02"
  }
}
```
Procedo alla creazione di un Virtual network. Lo associo al Resource Group appena creato, e gli do il nome scelto prima. Scelgo il prefisso 10.30.0.0/16 e creo la subnet con nome `snet-workload` e spazio 10.30.10.0/24. Inserisco i tag e dopo clicco su Review and Create.
Per controllare inserisco su CLI:
```bash
az network vnet show \
  --resource-group "$AUTO_RG" \
  --name "$AUTO_VNET" \
  --query "{Name:name,Location:location,Address:addressSpace.addressPrefixes,Subnets:subnets[].{Name:name,Prefix:addressPrefixes},Tags:tags}" \
  --output jsonc
```
Che ritorna:
```json
{
  "Address": [
    "10.30.0.0/16"
  ],
  "Location": "italynorth",
  "Name": "vnet-cea-auto",
  "Subnets": [
    {
      "Name": "snet-workload",
      "Prefix": [
        "10.30.10.0/24"
      ]
    }
  ],
  "Tags": {
    "course": "cloud-engineer-academy",
    "deleteAfter": "2026-09-10",
    "environment": "dev",
    "scenario": "autonomous",
    "unit": "UD02"
  }
}
```
Controlliamo la disponibilità globale del nome per lo Storage Account:
```bash 
az storage account check-name \
  --name "$AUTO_STORAGE" \
  --query "{Available:nameAvailable,Reason:reason,Message:message}" \
  --output jsonc
```
Ritorna `Available: true`, quindi procediamo sul portale. Clicco su Create, associo al Resource Group, do il nome e controllo che siano selezionati 'Standard' per performance e 'Locally-redundant storage' per ridondanza. Inoltre verifico che lo storage account sia `StorageV2`, che HTTPS sia obbligatorio, che TLS minimo sia 1.2 e che l'accesso Blob pubblico sia disabilitato. Aggiungo i tag e clicco su Review and Create.
Verifico con il comando su CLI:
```bash
az storage account show \
  --resource-group "$AUTO_RG" \
  --name "$AUTO_STORAGE" \
  --query "{Name:name,Location:location,Kind:kind,Sku:sku.name,HttpsOnly:enableHttpsTrafficOnly,MinimumTls:minimumTlsVersion,PublicBlobAccess:allowBlobPublicAccess,State:provisioningState,Tags:tags}" \
  --output jsonc
```
Che ritorna:
```json
{
  "HttpsOnly": true,
  "Kind": "StorageV2",
  "Location": "italynorth",
  "MinimumTls": "TLS1_2",
  "Name": "stceaauto3a5a3394",
  "PublicBlobAccess": false,
  "Sku": "Standard_LRS",
  "State": "Succeeded",
  "Tags": {
    "course": "cloud-engineer-academy",
    "deleteAfter": "2026-09-10",
    "environment": "dev",
    "scenario": "autonomous",
    "unit": "UD02"
  }
}
```


## Diagnosi

- errore o anomalia analizzata:
- ipotesi:
- controllo:
- correzione:
- verifica successiva:

## Cleanup e consegna

- risorse eliminate:
- controllo finale:
- hash abbreviato e messaggio del commit:

