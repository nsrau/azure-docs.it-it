---
title: "Informazioni di riferimento sui cmdlet PowerShell dell&quot;Utilità di pianificazione"
description: "Informazioni di riferimento sui cmdlet PowerShell dell&quot;Utilità di pianificazione"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: c40545833da86426d3e71955b8eb8627db3c1e4b
ms.openlocfilehash: 26eb1469bc508ef518103a955fa0fbc6db2be46d


---
# <a name="scheduler-powershell-cmdlets-reference"></a>Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione
La tabella seguente include descrizioni e collegamenti alla pagina di riferimento per ciascuno dei cmdlet principali nell'Utilità di pianificazione di Azure.

Per installare Azure PowerShell e associarlo alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs). 

Per altre informazioni sui [cmdlet di Azure Resource Manager](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx) vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

| Cmdlet | Descrizione del cmdlet |
| --- | --- |
| [Disable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |Disabilita una raccolta di processi. |
| [Enable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |Abilita una raccolta di processi. |
| [Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |Ottiene processi dell'Utilità di pianificazione. |
| [Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |Ottiene raccolte di processi. |
| [Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |Ottiene la cronologia processi. |
| [New-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |Crea un processo HTTP. |
| [New-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |Crea una raccolta di processi. |
| [New-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |Crea un processo di coda del bus di servizio. |
| [New-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |Crea un processo di argomento del bus di servizio. |
| [New-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |Crea un processo di coda di archiviazione. |
| [Remove-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |Elimina un processo dell'Utilità di pianificazione. |
| [Remove-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |Rimuove una raccolta di processi. |
| [Set-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |Modifica un processo HTTP dell'Utilità di pianificazione. |
| [Set-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |Modifica una raccolta di processi. |
| [Set-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |Modifica un processo di coda del bus di servizio. |
| [Set-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |Modifica un processo di argomento del bus di servizio. |
| [Set-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |Modifica un processo di coda di archiviazione. |

Per informazioni più dettagliate, è possibile eseguire i cmdlet seguenti: 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Vedere anche
 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione di Azure](scheduler-outbound-authentication.md)




<!--HONumber=Dec16_HO1-->


