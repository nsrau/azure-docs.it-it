---
title: Distribuire le risorse nella sottoscrizioneDeploy resources to subscription
description: Questo articolo descrive come creare un gruppo di risorse in un modello di Azure Resource Manager. Illustra anche come distribuire le risorse nell'ambito della sottoscrizione di Azure.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 6bec29a07653ff5ad7d1e2f8317246049e127c8c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604997"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Creare gruppi di risorse e risorse a livello di sottoscrizione

Per semplificare la gestione delle risorse nella sottoscrizione di Azure, è possibile definire e assegnare [criteri](../../governance/policy/overview.md) o controlli di [accesso basati](../../role-based-access-control/overview.md) sui ruoli nella sottoscrizione. Con i modelli a livello di sottoscrizione, è possibile applicare in modo dichiarativo i criteri e assegnare ruoli nella sottoscrizione. È inoltre possibile creare gruppi di risorse e distribuire le risorse.

Per distribuire i modelli a livello di sottoscrizione, usare l'interfaccia della riga di comando di Azure, PowerShell o l'API REST. Il portale di Azure non supporta la distribuzione nel livello di sottoscrizione.

## <a name="supported-resources"></a>Risorse supportate

È possibile distribuire i tipi di risorse seguenti a livello di sottoscrizione:You can deploy the following resource types at the subscription level:

* [Budget](/azure/templates/microsoft.consumption/budgets)
* [distribuzioni](/azure/templates/microsoft.resources/deployments) - per i modelli annidati che vengono distribuiti nei gruppi di risorse.Deployments - for nested templates that deploy to resource groups.
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)
* [policyAssegnazioni](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [correzioni](/azure/templates/microsoft.policyinsights/2019-07-01/remediations)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [RoleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)
* [scopeAssegnazioni](/azure/templates/microsoft.managednetwork/scopeassignments)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Tag](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>SCHEMA

Lo schema usato per le distribuzioni a livello di sottoscrizione è diverso dallo schema per le distribuzioni di gruppi di risorse.

Per i modelli, utilizzare:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Lo schema per un file di parametri è lo stesso per tutti gli ambiti di distribuzione. Per i file di parametri, utilizzare:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandi di distribuzione

I comandi per le distribuzioni a livello di sottoscrizione sono diversi dai comandi per le distribuzioni di gruppi di risorse.

Per l'interfaccia della riga di comando di Azure usare [az deployment sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create). L'esempio seguente distribuisce un modello per creare un gruppo di risorse:The following example deploys a template to create a resource group:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Per il comando di distribuzione di PowerShell , utilizzare [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) o **New-AzSubscriptionDeployment**. L'esempio seguente distribuisce un modello per creare un gruppo di risorse:The following example deploys a template to create a resource group:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Per l'API REST, usare [Deployments - Create At Subscription Scope](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Percorso e nome di distribuzione

Per le distribuzioni a livello di sottoscrizione, è necessario specificare un percorso per la distribuzione. Il percorso della distribuzione è separato dal percorso delle risorse distribuite. Il percorso di distribuzione specifica dove archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione o usare il nome di distribuzione predefinito. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato **azuredeploy.json** crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione, il percorso non è modificabile. Non è possibile creare una distribuzione in una posizione quando è presente una distribuzione esistente con lo stesso nome in un percorso diverso. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="use-template-functions"></a>Usare le funzioni di modello

Per le distribuzioni a livello di sottoscrizione, esistono alcune considerazioni importanti quando si usano funzioni di modello:

* La funzione [resourceGroup()](template-functions-resource.md#resourcegroup)**non** è supportata.
* Le funzioni [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) sono supportate.
* Usare la funzione [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) per ottenere l'ID risorsa per le risorse distribuite a livello di sottoscrizione.

  Ad esempio, per ottenere l'ID risorsa per una definizione di criteri, usare:For example, to get the resource ID for a policy definition, use:
  
  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  L'ID risorsa restituito ha il seguente formato:

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
      "apiVersion": "2018-05-01",
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
      "apiVersion": "2018-05-01",
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

Per informazioni sull'iterazione delle risorse, vedere [Distribuire più istanze di una risorsa nei modelli](./copy-resources.md)di Azure Resource Manager ed [Esercitazione: Creare più istanze](./template-tutorial-create-multiple-instances.md)di risorse con i modelli di Resource Manager.

## <a name="resource-group-and-resources"></a>Gruppo di risorse e risorse

Per creare un gruppo di risorse e distribuire risorse a tale gruppo, usare un modello annidato. Questo tipo di modello definisce le risorse da distribuire al gruppo. Impostare il modello annidato come dipendente dal gruppo di risorse per assicurarsi che il gruppo sia presente prima della distribuzione delle risorse.

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
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
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
              "apiVersion": "2017-10-01",
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

## <a name="create-policies"></a>Creare criteri

### <a name="assign-policy"></a>Assegnare un criterio

L'esempio seguente assegna una definizione di criteri esistente alla sottoscrizione. Se i criteri accettano parametri, specificarli come oggetto. Se non accettano parametri, usare l'oggetto vuoto predefinito.

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
# Built-in policy that accepts parameters
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

### <a name="define-and-assign-policy"></a>Definire e assegnare criteri

È possibile [definire](../../governance/policy/concepts/definition-structure.md) e assegnare criteri nello stesso modello.

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

Per creare la definizione di criteri nella sottoscrizione e applicarla alla sottoscrizione, usare il comando seguente dell'interfaccia della riga di comando:

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

## <a name="template-samples"></a>Esempi di modelli

* Creare un gruppo di [risorse, bloccarlo e assegnargli le autorizzazioni.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)
* Creare un gruppo di [risorse, un criterio e un'assegnazione di criteri.](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'assegnazione di ruoli, vedere [Gestire l'accesso alle risorse di Azure usando i modelli RBAC e Azure Resource Manager.](../../role-based-access-control/role-assignments-template.md)
* Per un esempio di distribuzione delle impostazioni dell'area di lavoro per il Centro sicurezza di Azure, vedere [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* I modelli di esempio sono disponibili in [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* È inoltre possibile distribuire modelli a livello di gruppo di [gestione](deploy-to-management-group.md) e a livello di [tenant.](deploy-to-tenant.md)
