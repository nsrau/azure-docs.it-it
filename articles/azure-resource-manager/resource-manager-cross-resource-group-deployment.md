---
title: "Distribuire le risorse di Azure in più gruppi di sottoscrizioni e risorse | Microsoft Docs"
description: "Illustra come specificare come destinazione più gruppi di sottoscrizioni e risorse di Azure durante la distribuzione."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: tomfitz
ms.openlocfilehash: 40b2d04fe829c51a58fb3bec1519a590a12cfdb8
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Distribuire le risorse di Azure in più gruppi di sottoscrizioni e risorse

In genere si distribuiscono tutte le risorse del modello in un unico [gruppo di risorse](resource-group-overview.md). ma in alcuni scenari può essere preferibile distribuire insieme un set di risorse, inserendole tuttavia in gruppi di sottoscrizioni e risorse diversi. Potrebbe essere necessario, ad esempio, distribuire la macchina virtuale di backup per Azure Site Recovery in un gruppo di risorse e in una posizione separati. Resource Manager consente di usare modelli annidati per specificare come destinazione gruppi di sottoscrizioni e risorse diversi da quello usato per il modello padre.

> [!NOTE]
> Un'unica distribuzione può interessare solo cinque gruppi di risorse.

## <a name="specify-a-subscription-and-resource-group"></a>Specificare un gruppo di sottoscrizioni e risorse

Per specificare come destinazione una risorsa diversa, usare un modello annidato o collegato. Il tipo di risorsa `Microsoft.Resources/deployments` fornisce parametri per `subscriptionId` e `resourceGroup`. Queste proprietà consentono di specificare un gruppo di sottoscrizioni e risorse diverso per la distribuzione nidificata. Tutti i gruppi di risorse devono esistere prima di eseguire la distribuzione. Se non si specifica un ID sottoscrizione o un gruppo di risorse, viene usato il gruppo di sottoscrizioni e risorse del modello padre.

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

L'esempio seguente distribuisce due account di archiviazione, uno nel gruppo di risorse specificato durante la distribuzione e uno in un gruppo di risorse specificato nel parametro `secondResourceGroup`:

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
        },
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
        }
    ]
}
```

Se si imposta `resourceGroup` sul nome di un gruppo di risorse che non esiste, la distribuzione ha esito negativo.

Per distribuire il modello di esempio, usare Azure PowerShell 4.0.0 o versione successiva o l'interfaccia della riga di comando di Azure 2.0.0 o versione successiva.

## <a name="use-the-resourcegroup-function"></a>Usare la funzione resourceGroup()

Per le distribuzioni tra gruppi di risorse, la [funzione resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) viene risolta in modo diverso in base al modo in cui si specifica il modello annidato. 

Se si incorpora un modello in un altro modello, la funzione resourceGroup() nel modello nidificato si risolve nel gruppo di risorse padre. Un modello incorporato usa il formato seguente:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Se si crea un collegamento a un modello separato, la funzione resourceGroup() nel modello collegato si risolve nel gruppo di risorse annidato. Un modello collegato usa il formato seguente:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="example-templates"></a>Modelli di esempio

I modelli seguenti mostrano distribuzioni tra più gruppi di risorse. Dopo la tabella sono mostrati script per distribuire i modelli.

|Modello  |DESCRIZIONE  |
|---------|---------|
|[Modello tra più sottoscrizioni](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Distribuisce un account di archiviazione in un gruppo di risorse e un account di archiviazione in un secondo gruppo di risorse. Includere un valore per l'ID sottoscrizione quando il secondo gruppo di risorse è in una sottoscrizione diversa. |
|[Modello di proprietà a più gruppi di risorse](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Mostra in che modo si risolve la funzione `resourceGroup()`. Non distribuisce alcuna risorsa. |

### <a name="powershell"></a>PowerShell

Per PowerShell, per distribuire due account di archiviazione in due gruppi di risorse nella **stessa sottoscrizione**, usare:

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Per PowerShell, per distribuire due account di archiviazione in **due sottoscrizioni**, usare:

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Per PowerShell, per testare in che modo l'**oggetto gruppo di risorse** si risolve per il modello padre, modello inline e modello collegato, usare:

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure, per distribuire due account di archiviazione in due gruppi di risorse nella **stessa sottoscrizione**, usare:

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

Per l'interfaccia della riga di comando di Azure, per distribuire due account di archiviazione in **due sottoscrizioni**, usare:

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

Per l'interfaccia della riga di comando di Azure, per testare in che modo l'**oggetto gruppo di risorse** si risolve per il modello padre, modello inline e modello collegato, usare:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-powershell-sas-token.md).
