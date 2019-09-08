---
title: Creare un gruppo di risorse e le risorse alla sottoscrizione - Modello di Azure Resource Manager
description: Questo articolo descrive come creare un gruppo di risorse in un modello di Azure Resource Manager. Illustra anche come distribuire le risorse nell'ambito della sottoscrizione di Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: tomfitz
ms.openlocfilehash: 37f2b04a62d94cce42b095540380460c38bc5b79
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772950"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Creare gruppi di risorse e risorse a livello di sottoscrizione

In genere, le risorse di Azure vengono distribuite a un gruppo nell'ambito della sottoscrizione di Azure. Tuttavia, è anche possibile creare gruppi di risorse di Azure e risorse di Azure a livello di sottoscrizione. Per distribuire i modelli a livello di sottoscrizione, usare l'interfaccia della riga di comando di Azure e Azure PowerShell. Il portale di Azure non supporta la distribuzione nel livello di sottoscrizione.

Per creare un gruppo di risorse in un modello di Azure Resource Manager, definire una risorsa [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) con un nome e un percorso specifici. È possibile creare un gruppo di risorse e distribuire risorse a tale gruppo di risorse nello stesso modello. Le risorse che è possibile distribuire a livello di sottoscrizione includono: [criteri](../governance/policy/overview.md) e [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md).

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

La distribuzione a livello di sottoscrizione si differenzia dalla distribuzione di un gruppo di risorse per gli aspetti seguenti:

### <a name="schema-and-commands"></a>Schema e comandi

Lo schema e i comandi usati per le distribuzioni a livello di sottoscrizione sono diversi rispetto alle distribuzioni di gruppi di risorse. 

Per lo schema, usare `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Per il comando di distribuzione dell'interfaccia della riga di comando di Azure, usare [az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Ad esempio, il comando dell'interfaccia della riga di comando seguente consente di distribuire un modello per creare un gruppo di risorse:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Per il comando di distribuzione di PowerShell, usare [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). Ad esempio, il comando PowerShell seguente consente di distribuire un modello per creare un gruppo di risorse:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Nome e percorso della distribuzione

Quando si distribuisce per la sottoscrizione, è necessario fornire un percorso per la distribuzione. È anche possibile fornire un nome per la distribuzione. Se non si specifica un nome per la distribuzione, il nome del modello viene utilizzato come nome della distribuzione. Ad esempio, la distribuzione di un modello denominato **azuredeploy.json** crea un nome di distribuzione predefinito di **azuredeploy**.

Il percorso delle distribuzioni a livello di sottoscrizione non è modificabile. È possibile creare una distribuzione in un percorso quando è presente una distribuzione con lo stesso nome, ma percorso diverso. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

### <a name="use-template-functions"></a>Usare le funzioni di modello

Per le distribuzioni a livello di sottoscrizione, esistono alcune considerazioni importanti quando si usano funzioni di modello:

* La funzione [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) **non** è supportata.
* La funzione [resourceId()](resource-group-template-functions-resource.md#resourceid) funzione è supportata. Usare la funzione per ottenere l'ID risorsa per le risorse che vengono usate in distribuzioni a livello di sottoscrizione. Ad esempio, ottenere l'ID risorsa per una definizione di criteri con `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* Le funzioni [reference()](resource-group-template-functions-resource.md#reference) e [list()](resource-group-template-functions-resource.md#list) sono supportate.

## <a name="create-resource-groups"></a>Creare gruppi di risorse

Il modello seguente crea un gruppo di risorse vuoto.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Lo schema dei modelli è reperibile [qui](/azure/templates/microsoft.resources/allversions). Modelli simili sono reperibili in [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Creare più gruppi di risorse

Per creare più gruppi di risorse usare l'[elemento copy](resource-group-create-multiple.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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

Per informazioni sull'iterazione delle risorse, vedere [Distribuire più istanze di una risorsa o di una proprietà nei modelli di Azure Resource Manager](./resource-group-create-multiple.md), ed [Esercitazione: Creare più istanze di risorse con modelli di Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="create-resource-group-and-deploy-resources"></a>Creare un gruppo di risorse e distribuire risorse

Per creare un gruppo di risorse e distribuire risorse a tale gruppo, usare un modello annidato. Questo tipo di modello definisce le risorse da distribuire al gruppo. Impostare il modello annidato come dipendente dal gruppo di risorse per assicurarsi che il gruppo sia presente prima della distribuzione delle risorse.

L'esempio seguente crea un gruppo di risorse e distribuisce un account di archiviazione al gruppo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
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

## <a name="create-policies"></a>Crea criteri

### <a name="assign-policy"></a>Assegna criteri

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
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
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

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Per distribuire questo modello con PowerShell, usare:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Definire e assegnare criteri

È possibile [definire](../governance/policy/concepts/definition-structure.md) e assegnare criteri nello stesso modello.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
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
            "name": "location-lock",
            "apiVersion": "2018-05-01",
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
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Per distribuire questo modello con PowerShell, usare:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'assegnazione dei ruoli, vedere [gestire l'accesso alle risorse di Azure usando i modelli RBAC e Azure Resource Manager](../role-based-access-control/role-assignments-template.md).
* Per un esempio di distribuzione delle impostazioni dell'area di lavoro per il Centro sicurezza di Azure, vedere [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Per informazioni sulla creazione di modelli di Gestione risorse di Azure, vedere [Creazione di modelli](resource-group-authoring-templates.md). 
* Per un elenco delle funzioni disponibili in un modello, vedere [Funzioni di modelli](resource-group-template-functions.md).
