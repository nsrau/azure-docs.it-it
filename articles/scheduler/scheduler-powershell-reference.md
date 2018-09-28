---
title: Informazioni di riferimento sui cmdlet PowerShell - Utilità di pianificazione di Azure
description: Informazioni sui cmdlet di PowerShell per Utilità di pianificazione di Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: a06439150ac255e7b436082ecc88702bf0c1dec1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991097"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Informazioni di riferimento sui cmdlet PowerShell per Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà l'Utilità di pianificazione di Azure di cui è in corso il ritiro. Per pianificare i processi, [provare App per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Per generare script per la creazione e la gestione dei processi dell'Utilità di pianificazione e delle raccolte dei processi, è possibile usare i cmdlet di PowerShell. Questo articolo elenca i principali cmdlet di PowerShell [ per Utilità di pianificazione Azure](/powershell/module/azurerm.scheduler) con collegamenti ai relativi articoli di riferimento. Per installare Azure PowerShell per l'abbonamento di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Per altre informazioni sui [cmdlet di Azure Resource Manager](/powershell/azure/overview) vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

| Cmdlet | DESCRIZIONE |
|--------|-------------|
| [Disable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Disabilita una raccolta di processi. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Abilita una raccolta di processi. |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Ottiene processi dell'Utilità di pianificazione. |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Ottiene raccolte di processi. |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Ottiene la cronologia processi. |
| [New-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Crea un processo HTTP. |
| [New-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Crea una raccolta di processi. |
| [New-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Crea un processo di coda del bus di servizio. |
| [New-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Crea un processo di argomento del bus di servizio. |
| [New-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Crea un processo di coda di archiviazione. |
| [Remove-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Elimina un processo dell'Utilità di pianificazione. |
| [Remove-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Rimuove una raccolta di processi. |
| [Set-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modifica un processo HTTP dell'Utilità di pianificazione. |
| [Set-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modifica una raccolta di processi. |
| [Set-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modifica un processo di coda del bus di servizio. |
| [Set-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modifica un processo di argomento del bus di servizio. |
| [Set-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modifica un processo di coda di archiviazione. |
||| 

Per altre informazioni, è possibile eseguire uno di questi cmdlet: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Vedere anche 

* [Informazioni sull'Utilità di pianificazione di Azure](scheduler-intro.md)
* [Concetti, terminologia e gerarchia di entità](scheduler-concepts-terms.md)
* [Crea e pianifica il primo processo - Portale di Azure](scheduler-get-started-portal.md)
* [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)
