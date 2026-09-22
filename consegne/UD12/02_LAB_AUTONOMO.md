# UD12 — Consegna LAB autonomo

## Baseline

- `terraform plan`: 
- stato atteso: No changes
- stato osservato: No changes. Your infrastructure matches the configuration.

## Modifica

- tag aggiunto: Environment = "Training"
- plan add: 0
- plan change: 1, azurerm_storage_account.lab will be updated in-place
- plan destroy: 0
- motivazione change e non recreate: Terraform propone una modifica perché i tag sono metadati mutabili sia a livello di API di Azure sia all'interno dello schema del provider Terraform (azurerm).

## Apply e verifica

- apply: Apply complete! Resources: 0 added, 1 changed, 0 destroyed.
- tag verificato con Azure CLI:
```json
{
  "Course": "AZ104",
  "Environment": "Training",
  "ManagedBy": "Terraform",
  "UD": "12"
}
```

## Errore controllato

- riferimento errato: `location = azurerm_resource_group.training.location`
- messaggio `terraform validate`: Error: Reference to undeclared resource. A managed resource "azurerm_resource_group" "training" has not been declared in the root module.
- causa: Abbiamo utilizzato un riferimento ad una risorsa non dichiarata.
- correzione: `location = azurerm_resource_group.lab.location`
- validate finale: Success! The configuration is valid.

## Git

- `.gitignore` verificato: sì
- state non committato: non committato
- lock file: committato
- commit: 62dfac7
- push/PR:

## Cleanup Terraform

- `terraform plan -destroy`: Plan: 0 to add, 0 to change, 2 to destroy.
- risorse previste: rg-ud12-tf, stud12t90072709
- `terraform destroy`: Destroy complete! Resources: 2 destroyed.
- `az group exists`: false
- `terraform state list` finale: Le risorse non compaiono

## Elementi conservati per UD13–UD15

- Bicep: sì
- Terraform: sì
- file IaC: sì
- agent configurato: sì
