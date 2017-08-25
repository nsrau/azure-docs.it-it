---
title: Usare PowerShell per gestire le risorse Hub eventi di Azure| Microsoft Docs
description: Usare il modulo di PowerShell per creare e gestire Hub eventi
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 2b49c01153b1104612e6ebf9c88566fc40d1f635
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Usare PowerShell per gestire le risorse Hub eventi

Microsoft Azure PowerShell è un ambiente di scripting che può essere usato per controllare e automatizzare la distribuzione e la gestione dei servizi di Azure. Questo articolo descrive come usare il [modulo PowerShell di Resource Manager di Hub eventi](/powershell/module/azurerm.eventhub) per il provisioning e la gestione di entità di Hub eventi, come spazi dei nomi, singoli hub eventi e gruppi di consumer, tramite una console o uno script locale di Azure PowerShell.

È anche possibile gestire risorse Hub eventi usando i modelli di Azure Resource Manager. Per altre informazioni vedere l'articolo [Creare uno spazio dei nomi dell'hub eventi con Hub eventi e un gruppo di consumer usando un modello di Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario disporre di quanto segue:

* Una sottoscrizione di Azure. Per altre informazioni su come ottenere una sottoscrizione, vedere le [opzioni di acquisto][purchase options], le [offerte per i membri][member offers] oppure l'[account gratuito][free account].
* Un computer con Azure PowerShell. Per le istruzioni vedere [Get started with Azure PowerShell cmdlets](/powershell/azure/get-started-azureps) (Introduzione ai cmdlet di Azure PowerShell).
* Conoscenza generale degli script di PowerShell, dei pacchetti NuGet e di .NET Framework.

## <a name="get-started"></a>Introduzione

Il primo passaggio consiste nell'usare PowerShell per accedere all'account Azure e alla sottoscrizione di Azure. Seguire le istruzioni in [Get started with Azure PowerShell cmdlets](/powershell/azure/get-started-azureps) (Introduzione ai cmdlet di Azure PowerShell) per accedere al proprio account Azure, quindi recuperare e accedere alle risorse nella sottoscrizione di Azure.

## <a name="provision-an-event-hubs-namespace"></a>Eseguire il provisioning di uno spazio dei nomi di Hub eventi

Quando si usano gli spazi dei nomi di hub eventi, è possibile usare i cmdlet [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [Remove-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace) e [Set-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace).

Questo esempio crea alcune variabili locali nello script: `$Namespace` e `$Location`.

* `$Namespace` è il nome dello spazio dei nomi dell'Hub eventi che si vuole usare.
* `$Location` identifica il data center in cui si eseguirà il provisioning dello spazio dei nomi.
* `$CurrentNamespace` archivia lo spazio dei nomi di riferimento che viene recuperato (o creato).

In uno script effettivo, `$Namespace` e `$Location` possono essere passati come parametri.

Questa parte dello script esegue le operazioni seguenti:

1. Tenta di recuperare uno spazio dei nomi dell'Hub eventi con il nome specificato.
2. Se lo spazio dei nomi viene trovato, viene segnalato ciò che viene trovato.
3. Se lo spazio dei nomi non viene trovato, viene creato lo spazio dei nomi e quindi viene recuperato lo spazio dei nomi appena creato.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Creare un hub eventi

Per creare un hub eventi, eseguire un controllo dello spazio dei nomi usando lo script della sezione precedente. Usare quindi il cmdlet [New-AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) per creare l'hub eventi:

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Creare un gruppo di consumer

Per creare un gruppo di consumer in un hub eventi, eseguire il controllo dello spazio dei nomi e dell'hub eventi usando gli script della sezione precedente. Usare quindi il cmdlet [New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) per creare il gruppo di consumer all'interno dell'hub eventi. ad esempio:

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Impostare i metadati dell'utente

Dopo avere eseguito gli script nelle sezioni precedenti, è possibile usare il cmdlet [Set-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) per aggiornare le proprietà di un gruppo di consumer, come nell'esempio seguente:

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Rimuovere un hub eventi

Per rimuovere gli hub eventi creati, è possibile usare i cmdlet `Remove-*` come nell'esempio seguente:

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere la documentazione completa del modulo PowerShell di Resource Manager di Hub eventi [qui](/powershell/module/azurerm.eventhub). Questa pagina elenca tutti i cmdlet disponibili.
- Per altre informazioni sull'uso dei modelli di Azure Resource Manager vedere l'articolo [Creare uno spazio dei nomi dell'hub eventi con Hub eventi e un gruppo di consumer usando un modello di Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).
- Informazioni sulle [librerie di gestione .NET di hub eventi](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/

