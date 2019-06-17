---
title: Monitorare Azure Batch | Microsoft Docs
description: Informazioni su servizi di monitoraggio Azure, metriche, log di diagnostica e altre funzionalità di monitoraggio per Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: b0243b37f725fc977337b72998d610e9bda71a86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128856"
---
# <a name="monitor-batch-solutions"></a>Monitorare le soluzioni Batch

Azure e il servizio Batch offrono un'ampia gamma di servizi, strumenti e API per monitorare le soluzioni Batch. Questo articolo di panoramica consente di scegliere un approccio di monitoraggio adatto alle proprie esigenze.

Per una panoramica dei componenti e dei servizi di Azure disponibili per il monitoraggio delle risorse di Azure, vedere [Monitoraggio di applicazioni e risorse di Azure](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Monitoraggio a livello di sottoscrizione

Al livello della sottoscrizione, che include gli account Batch, il [log attività di Azure](../azure-monitor/platform/activity-logs-overview.md) raccoglie i dati di eventi operativi in [diverse categorie](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log).

Per gli account Batch in particolare, il log attività raccoglie gli eventi correlati alla creazione ed eliminazione degli account e alla gestione delle chiavi.

Un modo per recuperare gli eventi dal log attività consiste nell'usare il portale di Azure. Fare clic su **Tutti i servizi** > **Log attività**. In alternativa, eseguire query per recuperare gli eventi tramite l'interfaccia della riga di comando di Azure, cmdlet di PowerShell o l'API REST di Monitoraggio di Azure. È anche possibile esportare il log attività oppure configurare gli [avvisi del log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Monitoraggio a livello di account Batch

Monitorare ogni account Batch usando le funzionalità di [Monitoraggio di Azure](../azure-monitor/overview.md). Monitoraggio di Azure raccoglie [metriche](../azure-monitor/platform/data-platform-metrics.md) e facoltativamente [i log di diagnostica](../azure-monitor/platform/diagnostic-logs-overview.md) per le risorse con ambito a livello di un account Batch, ad esempio, pool, processi e attività. Raccogliere e usare questi dati manualmente o a livello di codice per monitorare le attività nell'account Batch e per diagnosticare eventuali problemi. Per informazioni dettagliate, vedere [Metriche, avvisi e log di Batch per la valutazione diagnostica e il monitoraggio](batch-diagnostics.md).
 
> [!NOTE]
> Le metriche sono disponibili per impostazione predefinita nell'account Batch senza alcuna configurazione aggiuntiva e hanno una cronologia incrementale di 30 giorni. È necessario abilitare la registrazione diagnostica per un account Batch e si potrebbe incorrere in costi aggiuntivi per archiviare o elaborare i dati dei log di diagnostica. 

## <a name="batch-resource-monitoring"></a>Monitoraggio delle risorse Batch

Nelle applicazioni Batch usare le API di Batch per monitorare o recuperare lo stato delle risorse, inclusi processi, attività, nodi e pool. Ad esempio:

* [Conteggiare le attività e i nodi di calcolo in base allo stato](batch-get-resource-counts.md)
* [Creare query per elencare le risorse di Batch in modo efficiente](batch-efficient-list-queries.md)
* [Creare relazioni tra attività](batch-task-dependencies.md)
* Usare un'[attività di gestione dei processi](/rest/api/batchservice/job/add#jobmanagertask)
* Monitorare lo [stato dell'attività](/rest/api/batchservice/task/list#taskstate)
* Monitorare lo [stato del nodo](/rest/api/batchservice/computenode/list#computenodestate)
* Monitorare lo [stato del pool](/rest/api/batchservice/pool/get#poolstate)
* Monitorare l'[utilizzo del pool nell'account](/rest/api/batchservice/pool/listusagemetrics)
* [Conteggio dei nodi del pool in base allo stato](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Contatori delle prestazioni delle macchine virtuali e monitoraggio delle applicazioni

* [Application Insights](../azure-monitor/app/app-insights-overview.md) è un servizio di Azure che è possibile usare per monitorare a livello di codice la disponibilità, le prestazioni e l'utilizzo dei processi e delle attività di Batch. È possibile ottenere facilmente contatori delle prestazioni dai nodi di calcolo (macchine virtuali) e informazioni personalizzate per le attività dalle macchine virtuali. 

  Per un esempio, vedere [Monitorare ed eseguire il debug di un'applicazione .NET di Azure Batch con Application Insights](monitor-application-insights.md) e l'[esempio di codice](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) associato.

  > [!NOTE]
  > È possibile incorrere in costi aggiuntivi per l'uso di Application Insights. Vedere le [opzioni per i prezzi](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) è uno strumento client autonomo, gratuito e ricco di funzionalità che consente di creare, eseguire il debug e monitorare le applicazioni di Azure Batch. È possibile scaricare un [pacchetto di installazione](https://azure.github.io/BatchExplorer/) per Mac, Linux o Windows. Facoltativamente, configurare la soluzione Batch per [visualizzare i dati di Application Insights](https://github.com/Azure/batch-insights), ad esempio i contatori delle prestazioni delle macchine virtuali in Batch Explorer.


## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
* Altre informazioni sulla [registrazione diagnostica](batch-diagnostics.md) con Batch.