---
title: Creare un gruppo di consumer e lo spazio dei nomi di Hub eventi di Azure usando un modello | Microsoft Docs
description: Creare uno spazio dei nomi dell'hub eventi con Hub eventi e un gruppo di consumer usando i modelli di Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 584696303bfbaed07f416fb0b3febbcf59d05b35
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085741"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>Guida introduttiva: Creare un hub eventi usando un modello di Azure Resource Manager
Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

In questa guida introduttiva si crea un hub eventi usando un modello di Azure Resource Manager. Il modello viene usato per creare uno spazio dei nomi di tipo [Hub eventi](event-hubs-what-is-event-hubs.md), con un hub eventi e un gruppo di consumer. L'articolo descrive come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze. Per informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Azure Resource Manager][Authoring Azure Resource Manager templates].


> [!NOTE]
> Per il modello completo, vedere il [modello di Hub eventi e gruppo di consumer][Event Hub and consumer group template] in GitHub. Questo modello ha creato un gruppo di consumer oltre a uno spazio dei nomi dell'hub eventi e un hub eventi. Per verificare gli ultimi modelli, vedere la raccolta [Modelli di avvio rapido di Azure][Azure Quickstart Templates] e cercare Hub eventi.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida introduttiva è necessaria una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si desidera usare **Azure PowerShell** per distribuire il modello di Resource Manager [installare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

Se si desidera usare l'**interfaccia della riga di comando di Azure** per distribuire il modello di Resource Manager [installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Creare il modello di Azure Resource Manager JSON
Creare un file JSON denominato MyEventHub.json con il contenuto seguente e salvarlo in una cartella (ad esempio: C:\EventHubsQuickstarts\ResourceManagerTemplate).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>Creare i parametri JSON
Creare un file JSON denominato MyEventHub-Parameters.json contenente i parametri per il modello di Azure Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
        }
  }
}
```



## <a name="use-azure-powershell-to-deploy-the-template"></a>Usare Azure PowerShell per distribuire il modello

### <a name="sign-in-to-azure"></a>Accedere ad Azure
1. Avviare Azure PowerShell

2. Eseguire questo comando per accedere ad Azure:

   ```azurepowershell
   Login-AzureRmAccount
   ```
3. Eseguire i comandi seguenti per impostare il contesto corrente di sottoscrizione:

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="provision-resources"></a>Effettuare il provisioning delle risorse
Per distribuire o effettuare il provisioning di risorse con Azure PowerShell, passare alla cartella C:\EventHubsQuickStart\ARM\ ed eseguire i comandi seguenti:

> [!IMPORTANT]
> Specificare un nome per il gruppo di risorse di Azure come valore per $resourceGroupName prima di eseguire i comandi. 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzureRmResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>Usare l'interfaccia della riga di comando di Azure per distribuire il modello

## <a name="sign-in-to-azure"></a>Accedere ad Azure

I passaggi seguenti non sono necessari se si eseguono i comandi in Cloud Shell. Se si esegue l'interfaccia della riga di comando in locale, eseguire la procedura seguente per accedere ad Azure e impostare la sottoscrizione corrente:

Eseguire questo comando per accedere ad Azure:

```azurecli
az login
```

Impostare il contesto della sottoscrizione corrente. Sostituire `MyAzureSub` con il nome della sottoscrizione di Azure che si desidera usare:

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>Effettuare il provisioning delle risorse
Per distribuire le risorse con l'interfaccia della riga di comando di Azure, passare alla cartella C:\EventHubsQuickStart\ARM\ ed eseguire i comandi seguenti:

> [!IMPORTANT]
> Specificare un nome per il gruppo di risorse di Azure nel comando di creazione del gruppo az. .

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

Congratulazioni! Si è usato il modello di Azure Resource Manager per creare uno spazio dei nomi di Hub eventi e un hub eventi all'interno di questo spazio dei nomi.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato lo spazio dei nomi di Hub eventi e sono state usate applicazioni di esempio per inviare e ricevere eventi dall'hub eventi. Per istruzioni dettagliate relative all'invio o alla ricezione di eventi da un hub eventi, vedere le esercitazioni seguenti: 

- **Inviare eventi a un hub eventi**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Ricevere eventi da un hub eventi**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
