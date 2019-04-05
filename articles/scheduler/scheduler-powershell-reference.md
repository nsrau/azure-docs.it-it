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
ms.openlocfilehash: 5c80e86699d671994a0989b99c0f97ebe2680592
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044998"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Informazioni di riferimento sui cmdlet PowerShell per Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà l'Utilità di pianificazione di Azure di cui è in corso il ritiro. Per pianificare i processi, [provare App per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per generare script per la creazione e la gestione dei processi dell'Utilità di pianificazione e delle raccolte dei processi, è possibile usare i cmdlet di PowerShell. Questo articolo elenca i principali cmdlet di PowerShell [ per Utilità di pianificazione Azure](/powershell/module/azurerm.scheduler) con collegamenti ai relativi articoli di riferimento. Per installare Azure PowerShell per l'abbonamento di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Per altre informazioni sui [cmdlet di Azure Resource Manager](/powershell/azure/overview) vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

| Cmdlet | DESCRIZIONE |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/az.scheduler/disable-azschedulerjobcollection) |Disabilita una raccolta di processi. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/az.scheduler/enable-azschedulerjobcollection) |Abilita una raccolta di processi. |
| [Get-AzSchedulerJob](/powershell/module/az.scheduler/get-azschedulerjob) |Ottiene processi dell'Utilità di pianificazione. |
| [Get-AzSchedulerJobCollection](/powershell/module/az.scheduler/get-azschedulerjobcollection) |Ottiene raccolte di processi. |
| [Get-AzSchedulerJobHistory](/powershell/module/az.scheduler/get-azschedulerjobhistory) |Ottiene la cronologia processi. |
| [New-AzSchedulerHttpJob](/powershell/module/az.scheduler/new-azschedulerhttpjob) |Crea un processo HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/az.scheduler/new-azschedulerjobcollection) |Crea una raccolta di processi. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/new-azschedulerservicebusqueuejob) | Crea un processo di coda del bus di servizio. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/new-azschedulerservicebustopicjob) |Crea un processo di argomento del bus di servizio. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/new-azschedulerstoragequeuejob) |Crea un processo di coda di archiviazione. |
| [Remove-AzSchedulerJob](/powershell/module/az.scheduler/remove-azschedulerjob) |Elimina un processo dell'Utilità di pianificazione. |
| [Remove-AzSchedulerJobCollection](/powershell/module/az.scheduler/remove-azschedulerjobcollection) |Rimuove una raccolta di processi. |
| [Set-AzSchedulerHttpJob](/powershell/module/az.scheduler/set-azschedulerhttpjob) |Modifica un processo HTTP dell'Utilità di pianificazione. |
| [Set-AzSchedulerJobCollection](/powershell/module/az.scheduler/set-azschedulerjobcollection) |Modifica una raccolta di processi. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/set-azschedulerservicebusqueuejob) |Modifica un processo di coda del bus di servizio. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/set-azschedulerservicebustopicjob) |Modifica un processo di argomento del bus di servizio. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/set-azschedulerstoragequeuejob) |Modifica un processo di coda di archiviazione. |
||| 

Per altre informazioni, è possibile eseguire uno di questi cmdlet: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Vedere anche 

* [Che cos'è l'Utilità di pianificazione di Azure?](scheduler-intro.md)
* [Concetti, terminologia e gerarchia di entità](scheduler-concepts-terms.md)
* [Creare e pianificare il primo processo - portale di Azure](scheduler-get-started-portal.md)
* [Riferimento API REST dell'utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)
