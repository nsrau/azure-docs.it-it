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
ms.date: 10/01/2020
ms.openlocfilehash: 3470f969034a051b17e762b685a89c0f910e0cbb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747126"
---
# <a name="monitor-azure-machine-learning"></a>Monitorare Azure Machine Learning

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati da Azure Machine Learning e come analizzare e inviare avvisi su questi dati con monitoraggio di Azure.

> [!TIP]
> Le informazioni contenute in questo documento sono destinate principalmente agli amministratori, in quanto descrive il monitoraggio per il Azure Machine Learning a livello di *area di lavoro* . Se si è un esperto di dati o uno sviluppatore e si desidera monitorare le informazioni specifiche per le *esecuzioni di training del modello* , vedere i documenti seguenti:
>
> * [Avviare, monitorare e annullare le esecuzioni di training](how-to-manage-runs.md)
> * [Registrare le metriche per le esecuzioni di training](how-to-track-experiments.md)
> * [Tenere traccia di esperimenti con MLflow](how-to-use-mlflow.md)
> * [Visualizzare le esecuzioni con TensorBoard](how-to-monitor-tensorboard.md)

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure

Azure Machine Learning crea dati di monitoraggio tramite [monitoraggio di Azure](/azure/azure-monitor/overview), un servizio di monitoraggio dello stack completo in Azure. Monitoraggio di Azure offre un set completo di funzionalità per il monitoraggio delle risorse di Azure. Consente inoltre di monitorare le risorse in altri cloud e in locale.

Iniziare con l'articolo [monitoraggio delle risorse di Azure con monitoraggio di Azure](/azure/azure-monitor/insights/monitor-azure-resource), che descrive i concetti seguenti:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Dati di monitoraggio raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo, descrivendo i dati specifici raccolti per Azure Machine Learning. Queste sezioni forniscono anche esempi per la configurazione della raccolta dei dati e l'analisi di questi dati con gli strumenti di Azure.

> [!TIP]
> Per comprendere i costi associati a monitoraggio di Azure, vedere [utilizzo e costi stimati](/azure/azure-monitor/platform/usage-estimated-costs). Per informazioni sul tempo necessario per la visualizzazione dei dati in monitoraggio di Azure, vedere [tempo di inserimento dei dati di log](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitoraggio dei dati da Azure Machine Learning

Azure Machine Learning raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure descritte in [monitoraggio dei dati dalle risorse di Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources). 

Per un riferimento dettagliato dei log e delle metriche creati da Azure Machine Learning, vedere [Azure Machine Learning riferimento ai dati di monitoraggio](monitor-resource-reference.md) .

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Raccolta e routing

Le metriche della piattaforma e il log attività vengono raccolti e archiviati automaticamente, ma possono essere indirizzati ad altri percorsi tramite un'impostazione di diagnostica.  

I log delle risorse non vengono raccolti e archiviati fino a quando non si crea un'impostazione di diagnostica e la si instrada a una o più posizioni.

Vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](/azure/azure-monitor/platform/diagnostic-settings) per il processo dettagliato per la creazione di un'impostazione di diagnostica usando il portale di Azure, l'interfaccia della riga di comando o PowerShell. Quando si crea un'impostazione di diagnostica, è necessario specificare le categorie di log da raccogliere. Le categorie per Azure Machine Learning sono elencate in [Azure Machine Learning riferimento ai dati di monitoraggio](monitor-resource-reference.md#resource-logs).

> [!IMPORTANT]
> Per abilitare queste impostazioni sono necessari altri servizi di Azure (account di archiviazione, Hub eventi o Log Analytics), che possono aumentare i costi. Per calcolare un costo stimato, visitare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator).

Per Azure Machine Learning è possibile configurare i log seguenti:

| Category | Descrizione |
|:---|:---|
| AmlComputeClusterEvent | Eventi da Azure Machine Learning cluster di elaborazione. |
| AmlComputeClusterNodeEvent | Eventi dai nodi all'interno di un cluster di calcolo Azure Machine Learning. |
| AmlComputeJobEvent | Eventi dei processi in esecuzione in Azure Machine Learning calcolo. |

> [!NOTE]
> Quando si abilitano le metriche in un'impostazione di diagnostica, le informazioni sulle dimensioni non sono attualmente incluse come parte delle informazioni inviate a un account di archiviazione, a un hub eventi o a log Analytics.

Le metriche e i log che è possibile raccogliere sono descritti nelle sezioni seguenti.

## <a name="analyzing-metrics"></a>Analisi delle metriche

È possibile analizzare le metriche per Azure Machine Learning, insieme alle metriche di altri servizi di Azure, aprendo le **metriche** dal menu **monitoraggio di Azure** . Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](/azure/azure-monitor/platform/metrics-getting-started).

Per un elenco delle metriche della piattaforma raccolte, vedere [monitoraggio Azure Machine Learning metriche di riferimento ai dati](monitor-resource-reference.md#metrics).

Tutte le metriche per Azure Machine Learning si trovano nell' **area di lavoro Machine Learning servizio** spazio dei nomi.

![Esplora metriche con Machine Learning area di lavoro del servizio selezionata](./media/monitor-azure-machine-learning/metrics.png)

Come riferimento, è possibile visualizzare un elenco di [tutte le metriche delle risorse supportate in monitoraggio di Azure](/azure/azure-monitor/platform/metrics-supported).

### <a name="filtering-and-splitting"></a>Filtro e suddivisione

Per le metriche che supportano le dimensioni, è possibile applicare filtri usando un valore della dimensione. Ad esempio, filtrando i **core attivi** per il nome di un **cluster** `cpu-cluster` . 

È inoltre possibile suddividere una metrica per dimensione per visualizzare il modo in cui i diversi segmenti della metrica si confrontano tra loro. Ad esempio, suddividere il **tipo di passaggio della pipeline** per visualizzare un conteggio dei tipi di passaggi usati nella pipeline.

Per altre informazioni sul filtro e la suddivisione, vedere [funzionalità avanzate di monitoraggio di Azure](/azure/azure-monitor/platform/metrics-charts).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Analisi dei log

L'uso di monitoraggio di Azure Log Analytics richiede la creazione di una configurazione di diagnostica e l'abilitazione __di Invia informazioni a log Analytics__ . Per ulteriori informazioni, vedere la sezione [raccolta e routing](#collection-and-routing) .

I dati nei log di monitoraggio di Azure vengono archiviati in tabelle, in cui ogni tabella dispone di un proprio set di proprietà univoche. Azure Machine Learning archivia i dati nelle tabelle seguenti:

| Tabella | Descrizione |
|:---|:---|
| AmlComputeClusterEvent | Eventi da Azure Machine Learning cluster di elaborazione. |
| AmlComputeClusterNodeEvent | Eventi dai nodi all'interno di un cluster di calcolo Azure Machine Learning. |
| AmlComputeJobEvent | Eventi dei processi in esecuzione in Azure Machine Learning calcolo. |

> [!IMPORTANT]
> Quando si seleziona **log** dal menu Azure Machine Learning, log Analytics viene aperto con l'ambito della query impostato sull'area di lavoro corrente. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Se si vuole eseguire una query che includa dati di altri database o dati di altri servizi di Azure, selezionare **Log** dal menu di **Monitoraggio di Azure** . Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](/azure/azure-monitor/log-query/scope/).

Per un riferimento dettagliato dei log e delle metriche, vedere [Azure Machine Learning riferimento ai dati di monitoraggio](monitor-resource-reference.md).

### <a name="sample-kusto-queries"></a>Query Kusto di esempio

> [!IMPORTANT]
> Quando si selezionano i **log** dal menu [nome servizio], log Analytics viene aperto con ambito query impostato sull'area di lavoro Azure Machine Learning corrente. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Se si vuole eseguire una query che includa dati da altre aree di lavoro o dati da altri servizi di Azure, selezionare **registri** dal menu **monitoraggio di Azure** . Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](/azure/azure-monitor/log-query/scope/).

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

+ Ottenere gli eventi cluster negli ultimi cinque giorni per i cluster in cui sono Standard_D1_V2 le dimensioni della macchina virtuale:

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

## <a name="alerts"></a>Avvisi

È possibile accedere agli avvisi per Azure Machine Learning aprendo gli **avvisi** dal menu **monitoraggio di Azure** . Per informazioni dettagliate sulla creazione di avvisi [, vedere creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](/azure/azure-monitor/platform/alerts-metric) .

La tabella seguente elenca le regole di avviso delle metriche comuni e consigliate per Azure Machine Learning:

| Tipo di avviso | Condizione | Descrizione |
|:---|:---|:---|
| Model Deploy Failed (Distribuzione di modelli non riuscita) | Tipo di aggregazione: totale, operatore: maggiore di, valore soglia: 0 | Quando una o più distribuzioni di modelli non sono riuscite |
| Quota Utilization Percentage (Percentuale di utilizzo quota) | Tipo di aggregazione: media, operatore: maggiore di, valore soglia: 90| Quando la percentuale di utilizzo della quota è superiore al 90% |
| Unusable Nodes (Nodi non utilizzabili) | Tipo di aggregazione: totale, operatore: maggiore di, valore soglia: 0 | Quando sono presenti uno o più nodi inutilizzabili |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sui log e sulle metriche, vedere [monitoraggio Azure Machine Learning riferimento ai dati](monitor-resource-reference.md).
- Per informazioni sull'uso delle quote correlate alla Azure Machine Learning, vedere [gestire e richiedere le quote per le risorse di Azure](how-to-manage-quotas.md).
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [monitoraggio delle risorse di Azure con monitoraggio di Azure](/azure/azure-monitor/insights/monitor-azure-resource).
