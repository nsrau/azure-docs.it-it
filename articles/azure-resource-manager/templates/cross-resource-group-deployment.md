---
title: Distribuire risorse tra sottoscrizioni & gruppo di risorseDeploy resources cross subscription & resource group
description: Illustra come specificare come destinazione più gruppi di sottoscrizioni e risorse di Azure durante la distribuzione.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 70868f5a3598c26ffff81f0ad3536a6c5c0a7e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460348"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Distribuire le risorse di Azure in più gruppi di sottoscrizioni e risorse

In genere si distribuiscono tutte le risorse del modello in un unico [gruppo di risorse](../management/overview.md). ma in alcuni scenari può essere preferibile distribuire insieme un set di risorse, inserendole tuttavia in gruppi di sottoscrizioni e risorse diversi. Potrebbe essere necessario, ad esempio, distribuire la macchina virtuale di backup per Azure Site Recovery in un gruppo di risorse e in una posizione separati. Gestione risorse consente di usare i modelli annidati per scegliere come destinazione più sottoscrizioni e gruppi di risorse.

> [!NOTE]
> Un'unica distribuzione può interessare solo cinque gruppi di risorse. Questa limitazione significa in genere che è possibile eseguire la distribuzione in un solo gruppo di risorse specificato per il modello padre e in un massimo di quattro gruppi di risorse nelle distribuzioni annidate o collegate. Tuttavia, se il modello padre contiene solo modelli annidati o collegati e non distribuisce risorse, è possibile includere fino a cinque gruppi di risorse nelle distribuzioni annidate o collegate.

## <a name="specify-subscription-and-resource-group"></a>Specificare la sottoscrizione e il gruppo di risorseSpecify subscription and resource group

Per scegliere come destinazione un gruppo di risorse o una sottoscrizione diversa, usare un [modello nidificato o collegato.](linked-templates.md) Il `Microsoft.Resources/deployments` tipo di `subscriptionId` risorsa `resourceGroup`fornisce parametri per e , che consentono di specificare la sottoscrizione e il gruppo di risorse per la distribuzione annidata. Se non si specifica l'ID sottoscrizione o il gruppo di risorse, vengono usati la sottoscrizione e il gruppo di risorse del modello padre. Tutti i gruppi di risorse devono esistere prima di eseguire la distribuzione.

L'account usato per distribuire il modello deve disporre delle autorizzazioni per la distribuzione per l'ID sottoscrizione specificato. Se la sottoscrizione specificata è presente in un tenant di Azure Active Directory diverso, è necessario [aggiungere gli utenti guest da un'altra directory](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

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

Nell'esempio seguente vengono distribuiti due account di archiviazione. Il primo account di archiviazione viene distribuito nel gruppo di risorse specificato durante la distribuzione. Il secondo account di archiviazione viene distribuito nel gruppo di risorse specificato nei parametri e :The second storage account is deployed to the resource group specified in the `secondResourceGroup` e `secondSubscriptionID` parameters:

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
      "apiVersion": "2017-06-01",
      "name": "[variables('firstStorageName')]",
      "location": "[resourceGroup().location]",
      "sku":{
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate",
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
            "apiVersion": "2017-06-01",
            "name": "[variables('secondStorageName')]",
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

Se si `resourceGroup` imposta il nome di un gruppo di risorse che non esiste, la distribuzione non riesce.

Per testare il modello precedente e visualizzare i risultati, usare PowerShell o l'interfaccia della riga di comando di Azure.To test the preceding template and see the results, use PowerShell or Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Per distribuire due account di archiviazione in due gruppi di risorse nella **stessa sottoscrizione,** usare:To deploy two storage accounts to two resource groups in the same subscription , use:

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

Per distribuire due account di archiviazione in **due sottoscrizioni,** usare:To deploy two storage accounts to two subscriptions , use:

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

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per distribuire due account di archiviazione in due gruppi di risorse nella **stessa sottoscrizione,** usare:To deploy two storage accounts to two resource groups in the same subscription , use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Per distribuire due account di archiviazione in **due sottoscrizioni,** usare:To deploy two storage accounts to two subscriptions , use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Usare le funzioni

Le funzioni [resourceGroup()](template-functions-resource.md#resourcegroup) e [subscription()](template-functions-resource.md#subscription) si risolvono in modo diverso in base alla modalità di specifica del modello. Quando si crea un collegamento a un modello esterno, le funzioni vengono sempre risolte nell'ambito di tale modello. Quando si annida un modello all'interno di un modello padre, usare la `expressionEvaluationOptions` proprietà per specificare se le funzioni vengono risolte nel gruppo di risorse e nella sottoscrizione per il modello padre o il modello annidato. Impostare la `inner` proprietà su da risolvere nell'ambito per il modello annidato. Impostare la `outer` proprietà su da risolvere nell'ambito del modello padre.

Nella tabella seguente viene illustrato se le funzioni vengono risolte nel gruppo di risorse padre o nella sottoscrizione.

| Tipo di modello | Scope | Risoluzione |
| ------------- | ----- | ---------- |
| nidificati        | esterno (predefinito) | Gruppo di risorse padre |
| nidificati        | interno | Gruppo di risorse secondarieSub resource group |
| collegato        | N/D   | Gruppo di risorse secondarieSub resource group |

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) seguente mostra:The following example template shows:

* modello nidificato con ambito predefinito (esterno)
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
      "apiVersion": "2017-05-10",
      "name": "defaultScopeTemplate",
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
      "apiVersion": "2017-05-10",
      "name": "innerScopeTemplate",
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
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
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

Per testare il modello precedente e visualizzare i risultati, usare PowerShell o l'interfaccia della riga di comando di Azure.To test the preceding template and see the results, use PowerShell or Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

L'output dell'esempio precedente è:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

L'output dell'esempio precedente è:

```output
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

* Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](common-deployment-errors.md).
* Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](secure-template-with-sas-token.md).
