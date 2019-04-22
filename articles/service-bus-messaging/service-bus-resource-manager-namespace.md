---
title: Creare uno spazio dei nomi della messaggistica del bus di servizio tramite un modello di Azure Resource Manager | Microsoft Docs
description: Usare il modello di Azure Resource Manager per creare uno spazio dei nomi della messaggistica del bus di servizio
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 4471c9d5b6c09bcf4d9100cccfa725f36cf9a3f8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045083"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi del bus di servizio tramite il modello di Azure Resource Manager
In questa guida introduttiva viene creato un modello di Azure Resource Manager che crea uno spazio dei nomi del bus di servizio di tipo **Messaggistica** con uno SKU **Standard**. L'articolo definisce anche i parametri specificati per eseguire la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze. Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Azure Resource Manager][Authoring Azure Resource Manager templates]. Per il modello completo, vedere il [modello dello spazio dei nomi del bus di servizio][Service Bus namespace template] su GitHub.

> [!NOTE]
> Questi modelli di Azure Resource Manager sono disponibili per il download e la distribuzione. 
> 
> * [Creare uno spazio dei nomi del bus di servizio con coda](service-bus-resource-manager-namespace-queue.md)
> * [Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
> * [Creare uno spazio dei nomi del bus di servizio con coda e regola di autorizzazione](service-bus-resource-manager-namespace-auth-rule.md)
> * [Creare uno spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Per verificare la disponibilità di nuovi modelli, visitare la raccolta [Modelli di avvio rapido di Azure][Azure Quickstart Templates] e cercare "service bus".


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="quick-deployment"></a>Distribuzione rapida
Per eseguire l'esempio senza scrivere codice JSON ed eseguire comandi di PowerShell/CLI, selezionare il pulsante seguente:

[![Distribuzione in Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

Per creare e distribuire il modello manualmente, consultare le sezioni seguenti in questo articolo.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida introduttiva è necessaria una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si desidera usare **Azure PowerShell** per distribuire il modello di Resource Manager [installare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Se si desidera usare l'**interfaccia della riga di comando di Azure** per distribuire il modello di Resource Manager [installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Creare il modello di Azure Resource Manager JSON 
Creare un file JSON denominato **MyServiceBusNamespace.json** con il contenuto seguente: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

Questo modello crea uno spazio dei nomi del bus di servizio standard. Per la sintassi e le proprietà JSON, vedere le informazioni di riferimento sui modelli [spazi dei nomi](/azure/templates/microsoft.servicebus/namespaces).

## <a name="create-the-parameters-json"></a>Creare i parametri JSON
Il modello creato nel passaggio precedente include una sezione denominata `Parameters`. Definire i parametri per i valori che variano in base al progetto distribuito o all'ambiente di destinazione. Questo modello definisce i parametri seguenti: **serviceBusNamespaceName**, **serviceBusSku** e **location**. Per altre informazioni sugli SKU del bus di servizio, vedere [Prezzi di Bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/) da creare.

Creare un file JSON denominato **MyServiceBusNamespace-Parameters.json** con il contenuto seguente: 

> [!NOTE] 
> Specificare un nome per lo spazio dei nomi del bus di servizio. 


```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>Usare Azure PowerShell per distribuire il modello

### <a name="sign-in-to-azure"></a>Accedi ad Azure
1. Avviare Azure PowerShell

2. Eseguire questo comando per accedere ad Azure:

   ```azurepowershell
   Login-AzAccount
   ```
3. Eseguire i comandi seguenti per impostare il contesto corrente di sottoscrizione:

   ```azurepowershell
   Select-AzSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>Distribuire le risorse
Per distribuire le risorse usando Azure PowerShell, passare alla cartella in cui sono stati salvati i file JSON ed eseguire questi comandi:

> [!IMPORTANT]
> Specificare un nome per il gruppo di risorse di Azure come valore per $resourceGroupName prima di eseguire i comandi. 

1. Dichiarare una variabile per il nome del gruppo di risorse e specificarne un valore. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Creare un gruppo di risorse di Azure.

    ```azurepowershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ```
3. Distribuire il modello di Resource Manager. Specificare i nomi della distribuzione stessa, del gruppo di risorse, del file JSON per il modello, del file JSON per i parametri

    ```azurepowershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Usare l'interfaccia della riga di comando di Azure per distribuire il modello

### <a name="sign-in-to-azure"></a>Accedi ad Azure

1. Eseguire questo comando per accedere ad Azure:

    ```azurecli
    az login
    ```
2. Impostare il contesto della sottoscrizione corrente. Sostituire `MyAzureSub` con il nome della sottoscrizione di Azure che si desidera usare:

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>Distribuire le risorse
Per distribuire le risorse usando l'interfaccia della riga di comando di Azure, passare alla cartella con i file JSON ed eseguire questi comandi:

> [!IMPORTANT]
> Specificare un nome per il gruppo di risorse di Azure nel comando di creazione del gruppo az. .

1. Creare un gruppo di risorse di Azure. 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Distribuire il modello di Resource Manager. Specificare i nomi del gruppo di risorse, della distribuzione, del file JSON per il modello, del file JSON per i parametri.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato creato uno spazio dei nomi del bus di servizio. Vedere le altre guide introduttive per informazioni su come creare e usare code, argomenti/sottoscrizioni: 

- [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
- [Introduzione agli argomenti del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: https://azure.microsoft.com/pricing/details/service-bus/
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
