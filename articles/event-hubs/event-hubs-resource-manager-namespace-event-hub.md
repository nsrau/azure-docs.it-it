---
title: 'Guida introduttiva: Creare un hub eventi con gruppo di consumer - Hub eventi di Azure'
description: "Guida introduttiva: Creare uno spazio dei nomi dell'hub eventi con Hub eventi e un gruppo di consumer usando i modelli di Azure Resource Manager"
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 6ddf17030da8b0ff50f10938221b9c1dba08084d
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964295"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Guida introduttiva: Creare un hub eventi usando un modello di Azure Resource Manager

Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md). In questa guida di avvio rapido viene creato un hub eventi usando un [modello di Azure Resource Manager](../azure-resource-manager/management/overview.md). Il modello di Azure Resource Manager viene distribuito per creare uno spazio dei nomi di tipo [Hub eventi](event-hubs-what-is-event-hubs.md) con un hub eventi.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

No.

## <a name="create-an-event-hub"></a>Creare un hub eventi

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-eventhubs-create-namespace-and-eventhub/).

:::code language="json" source="~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json" range="1-61" highlight="32-59":::

Nel modello sono definite le risorse seguenti:

- [**Microsoft.EventHub/namespaces**](/azure/templates/microsoft.eventhub/namespaces)
- [**Microsoft.EventHub/namespaces/eventhubs**](/azure/templates/microsoft.eventhub/namespaces/eventhubs)

Per altri esempi di modello, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello:

1. Selezionare **Prova** in questo blocco di codice e quindi seguire le istruzioni per accedere ad Azure Cloud Shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   La creazione di un hub eventi richiede pochi minuti.

1. Selezionare **Copia** per copiare lo script di PowerShell.
1. Fare clic con il pulsante destro del mouse sulla console della shell e quindi scegliere **Incolla**.

## <a name="verify-the-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, è possibile aprire il gruppo di risorse dal [portale di Azure](https://portal.azure.com) oppure usare lo script di Azure PowerShell riportato di seguito.  Se Cloud Shell è ancora aperto, non è necessario copiare/eseguire la prima riga (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse. Se Cloud Shell è ancora aperto, non è necessario copiare/eseguire la prima riga (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato uno spazio dei nomi Hub eventi e un hub eventi nello spazio dei nomi. Per istruzioni dettagliate relative all'invio o alla ricezione di eventi da un hub eventi, vedere le esercitazioni per l'**invio e la ricezione di eventi**:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (solo invio)](event-hubs-c-getstarted-send.md)
- [Apache Storm (solo ricezione)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
