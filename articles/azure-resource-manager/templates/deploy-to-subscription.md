---
title: Distribuire risorse in una sottoscrizione
description: Questo articolo descrive come creare un gruppo di risorse in un modello di Azure Resource Manager. Illustra anche come distribuire le risorse nell'ambito della sottoscrizione di Azure.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 4f8bcbfc6467969c9d8ca8b1511e6e8ffff94b14
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653350"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Creare gruppi di risorse e risorse a livello di sottoscrizione

Per semplificare la gestione delle risorse, è possibile distribuire le risorse a livello della sottoscrizione di Azure. È ad esempio possibile distribuire [criteri](../../governance/policy/overview.md) e [controlli degli accessi in base al ruolo](../../role-based-access-control/overview.md) nella sottoscrizione. Tali risorse vengono applicate per l'intera sottoscrizione. È anche possibile creare gruppi di risorse e distribuire le risorse in tali gruppi di risorse.

> [!NOTE]
> È possibile eseguire una distribuzione a livello di sottoscrizione in un massimo di 800 gruppi di risorse diversi.

Per distribuire modelli a livello di sottoscrizione, usare l'interfaccia della riga di comando di Azure, PowerShell o l'API REST. Il portale di Azure non supporta la distribuzione nel livello di sottoscrizione.

## <a name="supported-resources"></a>Risorse supportate

A livello di sottoscrizione è possibile distribuire i tipi di risorse seguenti:

* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [budgets](/azure/templates/microsoft.consumption/budgets)
* [deployments](/azure/templates/microsoft.resources/deployments) - Per modelli annidati che vengono distribuiti a gruppi di risorse.
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/2019-07-01/remediations)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)
* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [tag](/azure/templates/microsoft.resources/tags)
* [workspacesettings](/azure/templates/microsoft.security/workspacesettings)

### <a name="schema"></a>SCHEMA

Lo schema usato per le distribuzioni a livello di sottoscrizione è diverso rispetto allo schema per le distribuzioni di gruppi di risorse.

Per i modelli, usare:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Lo schema per un file di parametri è lo stesso per tutti gli ambiti di distribuzione. Per i file di parametri, usare:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandi di distribuzione

I comandi per le distribuzioni a livello di sottoscrizione sono diversi rispetto ai comandi per le distribuzioni di gruppi di risorse.

Per l'interfaccia della riga di comando di Azure usare [az deployment sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create). L'esempio seguente distribuisce un modello per creare un gruppo di risorse:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Come comando di distribuzione di PowerShell, usare [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) o **New-AzSubscriptionDeployment**. L'esempio seguente distribuisce un modello per creare un gruppo di risorse:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Per l'API REST, usare [Distribuzioni - Crea nell'ambito della sottoscrizione](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Percorso e nome della distribuzione

Per le distribuzioni a livello di sottoscrizione, è necessario specificare un percorso di distribuzione. Il percorso di distribuzione è separato dal percorso delle risorse distribuite e specifica dove archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione oppure usare il nome predefinito. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato **azuredeploy.json** crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione il percorso non è modificabile. Non è possibile creare una distribuzione in un percorso se esiste una distribuzione con lo stesso nome in un percorso diverso. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="use-template-functions"></a>Usare le funzioni di modello

Per le distribuzioni a livello di sottoscrizione, esistono alcune considerazioni importanti quando si usano funzioni di modello:

* La funzione [resourceGroup()](template-functions-resource.md#resourcegroup)**non** è supportata.
* Le funzioni [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) sono supportate.
* Usare la funzione [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) per ottenere l'ID risorsa per le risorse distribuite a livello di sottoscrizione.

  Ad esempio, per ottenere l'ID risorsa per una definizione di criteri usare:

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  Il formato dell'ID risorsa restituito è il seguente:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Creare gruppi di risorse

Per creare un gruppo di risorse in un modello di Azure Resource Manager, definire una risorsa [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) con un nome e un percorso specifici. È possibile creare un gruppo di risorse e distribuire risorse a tale gruppo di risorse nello stesso modello.

Il modello seguente crea un gruppo di risorse vuoto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2019-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Per creare più gruppi di risorse usare l'[elemento copy](copy-resources.md).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2019-10-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Per informazioni sull'iterazione delle risorse, vedere [Distribuire più istanze di una risorsa nei modelli di Azure Resource Manager](./copy-resources.md) ed [Esercitazione: Creare più istanze di risorse con modelli di Resource Manager](./template-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Gruppo di risorse e risorse

Per creare un gruppo di risorse e distribuire risorse a tale gruppo, usare un modello annidato. Questo tipo di modello definisce le risorse da distribuire al gruppo. Impostare il modello annidato come dipendente dal gruppo di risorse per assicurarsi che il gruppo sia presente prima della distribuzione delle risorse. È possibile eseguire la distribuzione in un massimo di 800 gruppi di risorse.

L'esempio seguente crea un gruppo di risorse e distribuisce un account di archiviazione al gruppo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2019-10-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="azure-policy"></a>Criteri di Azure

### <a name="assign-policy-definition"></a>Assegnare una definizione di criteri

L'esempio seguente assegna una definizione di criteri esistente alla sottoscrizione. Se la definizione di criteri accetta parametri, specificarli come oggetto. Se non accetta parametri, usare l'oggetto vuoto predefinito.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Per distribuire questo modello con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Per distribuire questo modello con PowerShell, usare:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>Creare e assegnare definizioni di criteri

È possibile [definire](../../governance/policy/concepts/definition-structure.md) e assegnare una definizione di criteri nello stesso modello.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Per creare la definizione di criteri nella sottoscrizione e assegnarla alla sottoscrizione, usare il comando seguente dell'interfaccia della riga di comando:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Per distribuire questo modello con PowerShell, usare:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprint

### <a name="create-blueprint-definition"></a>Creare una definizione di progetto

È possibile [creare](../../governance/blueprints/tutorials/create-from-sample.md) una definizione di progetto da un modello.

:::code language="json" source="~/quickstart-templates/subscription-level-deployments/blueprints-new-blueprint/azuredeploy.json":::

Per creare la definizione del progetto nella sottoscrizione, usare il comando dell'interfaccia della riga di comando seguente:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-level-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Per distribuire questo modello con PowerShell, usare:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-level-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="template-samples"></a>Esempi di modelli

* [Create a resource group, lock it and give permissions to it](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment) (Creare un gruppo di risorse, bloccarlo e concedere autorizzazioni a questo).
* [Create a resource group, a policy, and a policy assignment](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json) (Creare un gruppo di risorse, un criterio e un'assegnazione di criteri).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'assegnazione di ruoli, vedere [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e i modelli di Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Per un esempio di distribuzione delle impostazioni dell'area di lavoro per il Centro sicurezza di Azure, vedere [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Modelli di esempio sono disponibili in [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* È anche possibile distribuire modelli a [livello di gruppo di gestione](deploy-to-management-group.md) e [a livello di tenant](deploy-to-tenant.md).
