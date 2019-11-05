---
title: Azure Machine Learning di monitoraggio | Microsoft Docs
description: Informazioni su come usare monitoraggio di Azure per visualizzare, analizzare e creare avvisi sulle metriche da Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/04/2019
ms.openlocfilehash: dddc39a0b7b3c43f283750c95d4feaef2cf15b36
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514277"
---
# <a name="monitoring-azure-machine-learning"></a>Azure Machine Learning di monitoraggio

Questo articolo descrive i dati di monitoraggio generati da Azure Machine Learning. Viene inoltre descritto come è possibile utilizzare Monitoraggio di Azure per analizzare i dati e definire gli avvisi.

## <a name="azure-monitor"></a>Monitoraggio di Azure

Azure Machine Learning registra i dati di monitoraggio tramite monitoraggio di Azure, un servizio di monitoraggio completo dello stack in Azure. Monitoraggio di Azure offre un set completo di funzionalità per il monitoraggio delle risorse di Azure. Consente inoltre di monitorare le risorse in altri cloud e in locale.

Iniziare con l'articolo [Panoramica di monitoraggio di Azure](/azure/azure-monitor/overview), che offre una panoramica delle funzionalità di monitoraggio. Le sezioni seguenti si basano su queste informazioni specificando gli elementi OO con il monitoraggio di Azure con Azure Machine Learning.

Per comprendere i costi associati a monitoraggio di Azure, vedere [utilizzo e costi stimati](/azure/azure-monitor/platform/usage-estimated-costs). Per informazioni sul tempo necessario per la visualizzazione dei dati in monitoraggio di Azure, vedere [tempo di inserimento dei dati di log](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitoraggio dei dati da Azure Machine Learning

Azure Machine Learning raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure, descritti in [monitoraggio dei dati dalle risorse di Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources). Per un riferimento dettagliato dei log e delle metriche creati da Azure Machine Learning, vedere [Azure Machine Learning riferimento ai dati di monitoraggio](monitor-resource-reference.md) .

## <a name="analyzing-metric-data"></a>Analisi dei dati delle metriche

È possibile analizzare le metriche per Azure Machine Learning aprendo le **metriche** dal menu **monitoraggio di Azure** . Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione ad Azure Esplora metriche](/azure/azure-monitor/platform/metrics-getting-started) .

Tutte le metriche per Azure Machine Learning si trovano nell' **area di lavoro Machine Learning servizio**spazio dei nomi.

![Esplora metriche con Machine Learning area di lavoro del servizio selezionata](media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtro e suddivisione

Per le metriche che supportano le dimensioni, è possibile applicare filtri usando un valore della dimensione. Ad esempio, se si filtrano i **core attivi** per il nome di un **cluster** `cpu-cluster`. 

È inoltre possibile suddividere una metrica per dimensione per visualizzare il modo in cui i diversi segmenti della metrica si confrontano tra loro. Ad esempio, suddividere il **tipo di passaggio della pipeline** per visualizzare un conteggio dei tipi di passaggi usati nella pipeline.

Per altre informazioni sul filtro e la suddivisione, vedere [funzionalità avanzate di monitoraggio di Azure](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Avvisi

È possibile accedere agli avvisi per Azure Machine Learning aprendo gli **avvisi** dal menu **monitoraggio di Azure** . Per informazioni dettagliate sulla creazione di avvisi [, vedere creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](/azure/azure-monitor/platform/alerts-metric) .

La tabella seguente elenca le regole di avviso delle metriche comuni e consigliate per Azure Machine Learning:

| Tipo di avviso | Condizione | Descrizione |
|:---|:---|:---|
| Distribuzione modello non riuscito | Tipo di aggregazione: totale, operatore: maggiore di, valore soglia: 0 | Quando una o più distribuzioni di modelli non sono riuscite |
| Percentuale di utilizzo della quota | Tipo di aggregazione: media, operatore: maggiore di, valore soglia: 90| Quando la percentuale di utilizzo della quota è superiore al 90% |
| Nodi inutilizzabili | Tipo di aggregazione: totale, operatore: maggiore di, valore soglia: 0 | Quando sono presenti uno o più nodi inutilizzabili |

## <a name="configuration"></a>Configurazione

> [!IMPORTANT]
> __Non è necessario configurare le metriche per Azure Machine Learning__, che vengono raccolte automaticamente e sono disponibili nell'Esplora metriche per il monitoraggio e l'invio di avvisi.

È possibile aggiungere un'impostazione di diagnostica per configurare le funzionalità seguenti:

* Archiviare le informazioni su log e metriche in un account di archiviazione di Azure.
* Trasmettere informazioni su log e metriche a un hub eventi di Azure.
* Inviare informazioni su log e metriche a monitoraggio di Azure Log Analytics.

Per abilitare queste impostazioni sono necessari altri servizi di Azure (account di archiviazione, Hub eventi o Log Analytics), che possono aumentare i costi. Per calcolare un costo stimato, visitare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator).

Per altre informazioni sulla creazione di un'impostazione di diagnostica, vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](/azure/azure-monitor/platform/diagnostic-settings).

Per Azure Machine Learning è possibile configurare i log seguenti:

| Categoria | Descrizione |
|:---|:---|
| AmlComputeClusterEvent | Eventi da Azure Machine Learning cluster di elaborazione. |
| AmlComputeClusterNodeEvent | Eventi dai nodi all'interno di un cluster di calcolo Azure Machine Learning. |
| AmlComputeJobEvent | Eventi dei processi in esecuzione in Azure Machine Learning calcolo. |

> [!NOTE]
> Quando si abilitano le metriche in un'impostazione di diagnostica, le informazioni sulle dimensioni non sono attualmente incluse come parte delle informazioni inviate a un account di archiviazione, a un hub eventi o a log Analytics.

## <a name="analyzing-log-data"></a>Analisi dei dati di log

L'uso di monitoraggio di Azure Log Analytics richiede la creazione di una configurazione di diagnostica e l'abilitazione __di Invia informazioni a log Analytics__. Per ulteriori informazioni, vedere la sezione [configurazione](#configuration) .

I dati nei log di monitoraggio di Azure vengono archiviati in tabelle, in cui ogni tabella dispone di un proprio set di proprietà univoche. Azure Machine Learning archivia i dati nelle tabelle seguenti:

| Tabella | Descrizione |
|:---|:---|
| AmlComputeClusterEvent | Eventi da Azure Machine Learning cluster di elaborazione. |
| AmlComputeClusterNodeEvent | Eventi dai nodi all'interno di un cluster di calcolo Azure Machine Learning. |
| AmlComputeJobEvent | Eventi dei processi in esecuzione in Azure Machine Learning calcolo. |

> [!IMPORTANT]
> Quando si seleziona **log** dal menu Azure Machine Learning, log Analytics viene aperto con l'ambito della query impostato sull'area di lavoro corrente. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Per eseguire una query che includa i dati di altri database o dati di altri servizi di Azure, selezionare **registri** dal menu **monitoraggio di Azure** . Per informazioni dettagliate, vedere [ambito di query di log e intervallo di tempo in monitoraggio di Azure log Analytics](/azure/azure-monitor/log-query/scope/) .

Per un riferimento dettagliato dei log e delle metriche, vedere [Azure Machine Learning riferimento ai dati di monitoraggio](monitor-resource-reference.md).

### <a name="sample-queries"></a>Query di esempio

Di seguito sono riportate le query che è possibile usare per monitorare le risorse di Azure Machine Learning: 

+ Ottenere i processi non riusciti negli ultimi cinque giorni:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Ottenere i record per un nome di processo specifico:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Ottenere gli eventi cluster negli ultimi cinque giorni per i cluster in cui la dimensione della macchina virtuale è Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Ottenere i nodi allocati negli ultimi otto giorni:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sui log e sulle metriche, vedere [Azure Machine Learning riferimento ai dati di monitoraggio](monitor-resource-reference.md).
- Per informazioni sull'uso delle quote correlate alla Azure Machine Learning, vedere [gestire e richiedere le quote per le risorse di Azure](how-to-manage-quotas.md).
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [monitoraggio delle risorse di Azure con monitoraggio di Azure](/azure/azure-monitor/insights/monitor-azure-resource).
