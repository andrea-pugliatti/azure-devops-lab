# UD13 — Consegna LAB guidato

## Terraform
- network.tf: 
```
resource "azurerm_virtual_network" "lab" {
  name                = "vnet-ud13"
  address_space       = ["10.13.0.0/16"]
  location            = azurerm_resource_group.lab.location
  resource_group_name = azurerm_resource_group.lab.name

  tags = {
    Course    = "AZ104"
    UD        = "13"
    ManagedBy = "Terraform"
  }
}

resource "azurerm_subnet" "app" {
  name                 = "snet-app"
  resource_group_name  = azurerm_resource_group.lab.name
  virtual_network_name = azurerm_virtual_network.lab.name
  address_prefixes     = ["10.13.1.0/24"]
}
```
- plan: 
Plan: 4 to add, 0 to change, 0 to destroy. 
azurerm_resource_group.lab
azurerm_storage_account.lab
azurerm_subnet.app
azurerm_virtual_network.lab
- apply: Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
- VNet verificata:
```json
{
  "Address": [
    "10.13.0.0/16"
  ],
  "Name": "vnet-ud13"
}
```
- subnet verificata:
```json
{
  "Name": "snet-app",
  "Prefix": [
    "10.13.1.0/24"
  ]
}
```
- destroy: Destroy complete! Resources: 4 destroyed.
- RG Terraform eliminato: `az group exists --name rg-ud12-tf` ritorna false

## Delivery persistente
- Resource Group: rg-ud13-15-delivery creato
- service connection: sc-azure-ud13-15 creata
- WIF: Ho selezionato il Workload identity federation come credential
- scope: Legato al resource group rg-ud13-15-delivery
- accesso globale a tutte le pipeline: NO

## Pipeline
- YAML:
Trigger: `trigger: none`
Pool: 
```yaml
pool:
  name: pool-ud09-wsl
```
Variables:
```yaml
variables:
  azureServiceConnection: 'sc-azure-ud13-15'
  deliveryResourceGroup: 'rg-ud13-15-delivery'
  location: 'italynorth'
```
Stage Validate:
```yaml
- stage: Validate
  displayName: Validate IaC
  jobs:
  - job: ValidateIaC
    displayName: Validate Terraform and Bicep

    workspace:
      clean: all

    steps:

    - checkout: self
      clean: true

    - bash: |
        echo "Agent.Name=$(Agent.Name)"
        echo "Agent.OS=$(Agent.OS)"
        echo "Build.SourcesDirectory=$(Build.SourcesDirectory)"

        echo
        echo "Terraform:"
        terraform version

        echo
        echo "Azure CLI:"
        az version

        echo
        echo "Bicep:"
        az bicep version
      displayName: Inspect self-hosted agent and required tools

    - bash: |
        cd infra/terraform
        terraform fmt -check
        terraform init -backend=false
        terraform validate
      displayName: Terraform fmt and validate

    - bash: |
        az bicep lint --file infra/bicep/delivery.bicep
      displayName: Bicep lint
```
Stage Deploy:
```yaml
- stage: Deploy
  displayName: Deploy delivery infrastructure
  dependsOn: Validate
  condition: succeeded()
  jobs:
  - job: DeployIaC
    displayName: What-If and deploy ACR

    workspace:
      clean: all

    steps:

    - checkout: self
      clean: true

    - task: AzureCLI@2
      displayName: Bicep What-If and deployment
      inputs:
        azureSubscription: '$(azureServiceConnection)'
        scriptType: bash
        scriptLocation: inlineScript
        inlineScript: |
          az deployment group what-if \
            --resource-group "$(deliveryResourceGroup)" \
            --template-file infra/bicep/delivery.bicep \
            --parameters location="$(location)"

          az deployment group create \
            --name "ud13-delivery-$(Build.BuildId)" \
            --resource-group "$(deliveryResourceGroup)" \
            --template-file infra/bicep/delivery.bicep \
            --parameters location="$(location)" \
            --output table
```
Workspace clean:
```yaml
    workspace:
      clean: all
```
Checkout:
```yaml
    - checkout: self
      clean: true
```
- agent pool: pool-ud09-wsl
- Validate: PASS
- Deploy: PASS
- What-If: Resource changes: 1 to create.
- deployment:
```
Name             State      Timestamp                         Mode         ResourceGroup
---------------  ---------  --------------------------------  -----------  -------------------
ud13-delivery-3  Succeeded  2026-09-24T10:25:53.169526+00:00  Incremental  rg-ud13-15-delivery
```
- ACR: Nome: `acrud131546xe5qaf4ai56` login server: `acrud131546xe5qaf4ai56.azurecr.io`
- SKU: Basic
- admin user: False

## Fine UD13
- risorse delivery conservate: sì
