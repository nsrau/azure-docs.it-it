---
title: "Distribuire le risorse di Azure in più gruppi di risorse | Microsoft Docs"
description: "Illustra come specificare come destinazione più gruppi di risorse di Azure durante la distribuzione."
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
ms.date: 06/15/2017
ms.author: tomfitz
ms.openlocfilehash: d8b041213b269775175a810e585103d3c538557f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>Distribuire le risorse di Azure in più gruppi di risorse

In genere si distribuiscono tutte le risorse del modello in un unico gruppo di risorse, ma in alcuni scenari può essere preferibile distribuire insieme un set di risorse, inserendole tuttavia in gruppi di risorse diversi. Potrebbe essere necessario, ad esempio, distribuire la macchina virtuale di backup per Azure Site Recovery in un gruppo di risorse e in una posizione separati. Resource Manager consente di usare modelli annidati per specificare come destinazione gruppi di risorse diversi da quello usato per il modello padre.

Il gruppo di risorse è il contenitore del ciclo di vita per l'applicazione e la raccolta di risorse. Si crea il gruppo di risorse al di fuori del modello e si specifica il gruppo di risorse da usare come destinazione durante la distribuzione. Per un'introduzione ai gruppi di risorse, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).

## <a name="example-template"></a>Modello di esempio

Per specificare come destinazione una risorsa diversa, è necessario usare un modello annidato o collegato durante la distribuzione. Il tipo di risorsa `Microsoft.Resources/deployments` fornisce un parametro `resourceGroup` che consente di specificare un gruppo di risorse diverso per la distribuzione annidata. Tutti i gruppi di risorse devono esistere prima di eseguire la distribuzione. L'esempio seguente distribuisce due account di archiviazione, uno nel gruppo di risorse specificato durante la distribuzione e uno in un gruppo di risorse denominato `crossResourceGroupDeployment`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
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
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

Se si imposta `resourceGroup` sul nome di un gruppo di risorse che non esiste, la distribuzione ha esito negativo. Se non si specifica un valore per `resourceGroup`, Resource Manager usa il gruppo di risorse padre.  

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello di esempio, è possibile usare il portale, Azure PowerShell o l'interfaccia della riga di comando di Azure. Per Azure PowerShell o l'interfaccia della riga di comando di Azure è necessario usare una versione di maggio 2017 o successiva. Gli esempi presuppongono che l'utente abbia salvato il modello in locale come file denominato **crossrgdeployment.json**.

Per PowerShell:

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

Per l'interfaccia della riga di comando di Azure:

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

Al termine della distribuzione, vengono visualizzati due gruppi di risorse. Ogni gruppo di risorse contiene un account di archiviazione.

## <a name="use-resourcegroup-function"></a>Usare la funzione resourceGroup()

Per le distribuzioni tra gruppi di risorse, la [funzione resouceGroup()](resource-group-template-functions-resource.md#resourcegroup) viene risolta in modo diverso in base al modo in cui si specifica il modello annidato. 

Se si incorpora un modello in un altro modello, la funzione resouceGroup() nel modello annidato si risolve nel gruppo di risorse padre. Un modello incorporato usa il formato seguente:

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

Se si crea un collegamento a un modello separato, la funzione resouceGroup() nel modello collegato si risolve nel gruppo di risorse annidato. Un modello collegato usa il formato seguente:

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

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](resource-group-authoring-templates.md).
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](resource-manager-powershell-sas-token.md).
