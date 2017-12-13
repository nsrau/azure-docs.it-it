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
ms.date: 12/01/2017
ms.author: tomfitz
ms.openlocfilehash: 763f46b9b5be7edf06ee0604bfc51a2482405b60
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Distribuire le risorse di Azure in più gruppi di sottoscrizioni e risorse

In genere si distribuiscono tutte le risorse del modello in un unico gruppo di risorse, ma in alcuni scenari può essere preferibile distribuire insieme un set di risorse, inserendole tuttavia in gruppi di sottoscrizioni e risorse diversi. Potrebbe essere necessario, ad esempio, distribuire la macchina virtuale di backup per Azure Site Recovery in un gruppo di risorse e in una posizione separati. Resource Manager consente di usare modelli annidati per specificare come destinazione gruppi di sottoscrizioni e risorse diversi da quello usato per il modello padre.

Il gruppo di risorse è il contenitore del ciclo di vita per l'applicazione e la raccolta di risorse. Si crea il gruppo di risorse al di fuori del modello e si specifica il gruppo di risorse da usare come destinazione durante la distribuzione. Per un'introduzione ai gruppi di risorse, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).

## <a name="specify-a-subscription-and-resource-group"></a>Specificare un gruppo di sottoscrizioni e risorse

Per specificare come destinazione una risorsa diversa, è necessario usare un modello annidato o collegato durante la distribuzione. Il tipo di risorsa `Microsoft.Resources/deployments` fornisce parametri per `subscriptionId` e `resourceGroup`. Queste proprietà consentono di specificare un gruppo di sottoscrizioni e risorse diverso per la distribuzione nidificata. Tutti i gruppi di risorse devono esistere prima di eseguire la distribuzione. Se non si specifica un ID sottoscrizione o un gruppo di risorse, viene usato il gruppo di sottoscrizioni e risorse del modello padre.

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

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello di esempio, usare una versione di Azure PowerShell o dell'interfaccia della riga di comando di Azure a partire da maggio 2017. Per questi esempi, usare il [modello di sottoscrizione incrociata](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) in GitHub.

### <a name="two-resource-groups-in-the-same-subscription"></a>Due gruppi di risorse nella stessa sottoscrizione

Per PowerShell, per distribuire due account di archiviazione in due gruppi di risorse nella stessa sottoscrizione, usare:

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

Per l'interfaccia della riga di comando di Azure, per distribuire due account di archiviazione in due gruppi di risorse nella stessa sottoscrizione, usare:

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

Al termine della distribuzione, vengono visualizzati due gruppi di risorse. Ogni gruppo di risorse contiene un account di archiviazione.

### <a name="two-resource-groups-in-different-subscriptions"></a>Due gruppi di risorse in sottoscrizioni diverse

Per PowerShell, per distribuire due account di archiviazione in due sottoscrizioni, usare:

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

Per l'interfaccia della riga di comando di Azure, per distribuire due account di archiviazione in due sottoscrizioni, usare:

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

Per testare i diversi modi in cui la funzione `resourceGroup()` viene risolta, distribuire un [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) che restituisce l'oggetto gruppo di risorse per il modello padre, il modello inline e il modello collegato. Il modello padre e il modello inline restituiscono entrambi lo stesso gruppo di risorse. Il modello collegato restituisce il gruppo di risorse collegato.

Per PowerShell, usare:

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Per l'interfaccia della riga di comando di Azure usare:

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
