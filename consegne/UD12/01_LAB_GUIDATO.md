# UD12 — Consegna LAB guidato

## Preparazione

- materiali UD12: presenti
- repository personale: presente
- directory `infra/bicep`: presente
- directory `infra/terraform`: presente
- directory consegne: presente

## Azure

- subscription verificata: sì
- identità verificata: sì

## Bicep

- Bicep version: 0.47.16
- lint: PASS
- Resource Group: rg-ud12-bicep
- Storage Account: stud12b90071753
- What-If change type: Create Microsoft.Storage/storageAccounts/stud12b90071753
- deployment:
```
Name               State      Timestamp                         Mode         ResourceGroup
-----------------  ---------  --------------------------------  -----------  ---------------
ud12-bicep-deploy  Succeeded  2026-09-22T10:14:41.225881+00:00  Incremental  rg-ud12-bicep
```
- output `storageAccountName`:
```json
{
    "type": "String",
    "value": "stud12b90071753"
}
```
- output `blobEndpoint`:
```json
{
    "type": "String",
    "value": "https://stud12b90071753.blob.core.windows.net/"
}
```
- verifica CLI:
```
Name             Location    SKU           TLS     PublicBlob
---------------  ----------  ------------  ------  ------------
stud12b90071753  italynorth  Standard_LRS  TLS1_2  False
```
- Resource Group Bicep eliminato: sì
- `az group exists`: false

## Terraform

- Terraform version: 1.16.3
- provider AzureRM: hashicorp/azurerm
- `terraform init`: Terraform has been successfully initialized!
- `terraform fmt -check`: Il comando non ha prodotto differenze con `terraform fmt`
- `terraform validate`: Success! The configuration is valid.
- plan add: 2, `azurerm_resource_group.lab` e `azurerm_storage_account.lab`
- plan change: 0
- plan destroy: 0
- apply: Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
- output Resource Group: rg-ud12-tf
- output Storage: stud12t90072709 https://stud12t90072709.blob.core.windows.net/
- verifica CLI:
Resource group:
```json
{
  "Location": "italynorth",
  "Name": "rg-ud12-tf",
  "Tags": {
    "Course": "AZ104",
    "ManagedBy": "Terraform",
    "UD": "12"
  }
}
```
Storage account:
```json
{
  "Location": "italynorth",
  "Name": "stud12t90072709",
  "SKU": "Standard_LRS"
}
```
- `terraform state list`:
```
azurerm_resource_group.lab
azurerm_storage_account.lab
```

## Fine LAB guidato

- risorse Terraform mantenute per LAB autonomo: sì
