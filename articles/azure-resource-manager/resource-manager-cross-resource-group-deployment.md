---
title: Distribuire le risorse tra sottoscrizioni & gruppo di risorse
description: Illustra come specificare come destinazione più gruppi di sottoscrizioni e risorse di Azure durante la distribuzione.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0754895215384f76b1cb44224f3ba06c80181827
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978765"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Distribuire le risorse di Azure in più gruppi di sottoscrizioni e risorse

In genere si distribuiscono tutte le risorse del modello in un unico [gruppo di risorse](resource-group-overview.md). ma in alcuni scenari può essere preferibile distribuire insieme un set di risorse, inserendole tuttavia in gruppi di sottoscrizioni e risorse diversi. Potrebbe essere necessario, ad esempio, distribuire la macchina virtuale di backup per Azure Site Recovery in un gruppo di risorse e in una posizione separati. Gestione risorse consente di usare modelli annidati per indirizzare più di una sottoscrizione e un gruppo di risorse.

> [!NOTE]
> Un'unica distribuzione può interessare solo cinque gruppi di risorse. Questa limitazione significa in genere che è possibile eseguire la distribuzione in un solo gruppo di risorse specificato per il modello padre e in un massimo di quattro gruppi di risorse nelle distribuzioni annidate o collegate. Tuttavia, se il modello padre contiene solo modelli annidati o collegati e non distribuisce risorse, è possibile includere fino a cinque gruppi di risorse nelle distribuzioni annidate o collegate.

## <a name="specify-subscription-and-resource-group"></a>Specificare la sottoscrizione e il gruppo di risorse

Per fare riferimento a un gruppo di risorse o a una sottoscrizione diversa, usare un [modello annidato o collegato](resource-group-linked-templates.md). Il tipo di risorsa `Microsoft.Resources/deployments` fornisce parametri per `subscriptionId` e `resourceGroup`che consentono di specificare la sottoscrizione e il gruppo di risorse per la distribuzione nidificata. Se non si specifica l'ID sottoscrizione o il gruppo di risorse, viene usata la sottoscrizione e il gruppo di risorse del modello padre. Tutti i gruppi di risorse devono esistere prima di eseguire la distribuzione.

L'account usato per distribuire il modello deve disporre delle autorizzazioni per la distribuzione per l'ID sottoscrizione specificato. Se la sottoscrizione specificata è presente in un tenant di Azure Active Directory diverso, è necessario [aggiungere gli utenti guest da un'altra directory](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Per specificare un gruppo di risorse e una sottoscrizione differenti, usare:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Se i gruppi di risorse si trovano nella stessa sottoscrizione, è possibile rimuovere il valore **subscriptionId**.

L'esempio seguente distribuisce due account di archiviazione. Il primo account di archiviazione viene distribuito nel gruppo di risorse specificato durante la distribuzione. Il secondo account di archiviazione viene distribuito nel gruppo di risorse specificato nei parametri `secondResourceGroup` e `secondSubscriptionID`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
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
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        }
    ]
}
```

Se si imposta `resourceGroup` sul nome di un gruppo di risorse che non esiste, la distribuzione avrà esito negativo.

Per testare il modello precedente e visualizzare i risultati, usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per distribuire due account di archiviazione in due gruppi di risorse nella **stessa sottoscrizione**, usare:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Per distribuire due account di archiviazione in **due sottoscrizioni**, usare:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

Per distribuire due account di archiviazione in due gruppi di risorse nella **stessa sottoscrizione**, usare:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Per distribuire due account di archiviazione in **due sottoscrizioni**, usare:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Usare le funzioni

Le funzioni [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) e [Subscription ()](resource-group-template-functions-resource.md#subscription) vengono risolte in modo diverso in base alla modalità di specifica del modello. Quando si esegue il collegamento a un modello esterno, le funzioni si risolvono sempre nell'ambito del modello. Quando si annida un modello all'interno di un modello padre, usare la proprietà `expressionEvaluationOptions` per specificare se le funzioni vengono risolte nel gruppo di risorse e nella sottoscrizione per il modello padre o il modello annidato. Impostare la proprietà su `inner` per risolvere l'ambito del modello annidato. Impostare la proprietà su `outer` per risolvere l'ambito del modello padre.

La tabella seguente mostra se le funzioni vengono risolte nel gruppo di risorse padre o incorporato e nella sottoscrizione.

| Tipo di modello | Scope | Risoluzione |
| ------------- | ----- | ---------- |
| nidificati        | Outer (impostazione predefinita) | Gruppo di risorse padre |
| nidificati        | interno | Gruppo di risorse secondario |
| collegate        | N/D   | Gruppo di risorse secondario |

Il [modello di esempio]((https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)) seguente mostra:

* modello annidato con ambito predefinito (esterno)
* modello annidato con ambito interno
* modello collegato

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "defaultScopeTemplate",
            "apiVersion": "2017-05-10",
            "resourceGroup": "inlineGroup",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                    ],
                    "outputs": {
                        "resourceGroupOutput": {
                            "type": "string",
                            "value": "[resourceGroup().name]"
                        }
                    }
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "name": "innerScopeTemplate",
            "apiVersion": "2017-05-10",
            "resourceGroup": "inlineGroup",
            "properties": {
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                    ],
                    "outputs": {
                        "resourceGroupOutput": {
                            "type": "string",
                            "value": "[resourceGroup().name]"
                        }
                    }
                },
                "parameters": {}
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "linkedGroup",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/resourceGroupName.json"
                },
                "parameters": {}
            }
        }
    ],
    "outputs": {
        "parentRG": {
            "type": "string",
            "value": "[concat('Parent resource group is ', resourceGroup().name)]"
        },
        "defaultScopeRG": {
            "type": "string",
            "value": "[concat('Default scope resource group is ', reference('defaultScopeTemplate').outputs.resourceGroupOutput.value)]"
        },
        "innerScopeRG": {
            "type": "string",
            "value": "[concat('Inner scope resource group is ', reference('innerScopeTemplate').outputs.resourceGroupOutput.value)]"
        },
        "linkedRG": {
            "type": "string",
            "value": "[concat('Linked resource group is ', reference('linkedTemplate').outputs.resourceGroupOutput.value)]"
        }
    }
}
```

Per testare il modello precedente e visualizzare i risultati, usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

L'output dell'esempio precedente è:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

L'output dell'esempio precedente è:

```azurecli
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-powershell-sas-token.md).
