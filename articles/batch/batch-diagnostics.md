---
title: Abilitare la registrazione diagnostica per eventi di Batch - Azure | Documentazione Microsoft
description: "Registrare e analizzare gli eventi di registrazione diagnostica per le risorse dell'account Azure Batch, come pool e attività."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7bc6fd9921ab0f2374ace33ea5c1ab93a78f860
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>Registrare gli eventi per la diagnostica e il monitoraggio delle soluzioni Batch

Come per molti servizi di Azure, il servizio Batch genera eventi di log per determinate risorse durante il ciclo di vita della risorsa. È possibile abilitare i log di diagnostica di Azure Batch per registrare gli eventi per le risorse, come attività e pool, e quindi usare i log per l'analisi e il monitoraggio diagnostici. Eventi come la creazione e l'eliminazione di pool, l'avvio e il completamento di attività e di altro tipo sono inclusi nei log di diagnostica di Batch.

> [!NOTE]
> L'articolo illustra gli eventi di registrazione per le risorse degli account Batch, non i dati di output di attività e processi. Per i dettagli sull'archiviazione dei dati di output di processi e attività, vedere [Salvare in modo permanente l'output dei processi e delle attività di Azure Batch](batch-task-output.md).
> 
> 

## <a name="prerequisites"></a>prerequisiti
* [Account Azure Batch](batch-account-create-portal.md)
* [Account di archiviazione di Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  Per salvare in modo permanente i log di diagnostica di Batch, è necessario creare un account di archiviazione di Azure in cui verranno archiviati i log. È possibile specificare questo account di archiviazione quando si [abilita la registrazione diagnostica](#enable-diagnostic-logging) per l'account Batch. L'account di archiviazione specificato quando si abilita la raccolta di log non è lo stesso account di archiviazione collegato a cui si fa riferimento negli articoli relativi ai [pacchetti dell'applicazione](batch-application-packages.md) e alla [persistenza dell'output delle attività](batch-task-output.md).
  
  > [!WARNING]
  > Si riceverà un **addebito** per i dati archiviati nell'account di archiviazione di Azure. Sono inclusi i log di diagnostica descritti in questo articolo. Tenere presente questo aspetto quando si progetta il [criterio di conservazione dei log](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).
  > 
  > 

## <a name="enable-diagnostic-logging"></a>Abilitare la registrazione diagnostica
Per impostazione predefinita, la registrazione diagnostica non è abilitata per l'account Batch. È necessario abilitare esplicitamente la registrazione diagnostica per ogni account Batch da monitorare:

[Come abilitare la raccolta dei log di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

È consigliabile leggere fino in fondo l'articolo [Panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) per comprendere non solo come abilitare la registrazione, ma anche le categorie di log supportate dai vari servizi di Azure. Ad esempio, Batch di Azure supporta attualmente una categoria di log: i **log del servizio**.

## <a name="service-logs"></a>Log del servizio
I log del servizio di Azure Batch contengono gli eventi generati dal servizio Azure Batch durante il ciclo di vita di una risorsa di Batch, come un'attività o un pool. Ogni evento generato da Batch viene archiviato nell'account di archiviazione specificato nel formato JSON. Ad esempio, questo è il corpo di un **evento di creazione pool** di esempio:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Ogni corpo dell'evento risiede in un file con estensione json nell'account di archiviazione di Azure specificato. Per accedere direttamente ai log, si consiglia di esaminare lo [schema dei log di diagnostica nell'account di archiviazione](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Eventi del log del servizio
Il servizio Batch attualmente emette gli eventi di log del servizio seguenti. Questo elenco potrebbe non essere completo, poiché è possibile che eventi aggiuntivi siano stati aggiunti dopo l'ultimo aggiornamento di questo articolo.

| **Eventi del log del servizio** |
| --- |
| [Pool create][pool_create] (Creazione del pool) |
| [Pool delete start][pool_delete_start] (Avvio dell'eliminazione del pool) |
| [Pool delete complete][pool_delete_complete] (Completamento dell'eliminazione del pool) |
| [Pool resize start][pool_resize_start] (Avvio del ridimensionamento del pool) |
| [Pool resize complete][pool_resize_complete] (Completamento del ridimensionamento del pool) |
| [Task start][task_start] (Avvio dell'attività) |
| [Task complete][task_complete] (Completamento dell'attività) |
| [Task fail][task_fail] (Errore dell'attività) |

## <a name="next-steps"></a>Passaggi successivi
Oltre ad archiviare gli eventi dei log di diagnostica in un account di archiviazione di Azure, è possibile anche trasmettere gli eventi del log del servizio Batch a un [Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md) e inviarli ad [Azure Log Analytics](../log-analytics/log-analytics-overview.md).

* [Trasmettere log di diagnostica di Azure a Hub eventi](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Trasmettere in streaming il flusso di eventi diagnostici di Batch al servizio dati in ingresso a scalabilità elevata, Hub eventi. Hub eventi è in grado di inserire milioni di eventi al secondo, che è quindi possibile trasformare e archiviare tramite un qualsiasi provider di analisi in tempo reale.
* [Analizzare i log di diagnostica di Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)
  
  Inviare i log diagnostici a Log Analytics, in cui è possibile analizzarli nel portale di Operations Management Suite (OMS) o esportarli per l'analisi in Excel o Power BI.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
