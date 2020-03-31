---
title: Monitorare le prestazioni, l'integrità & l'utilizzo di Azure Data Explorer con le metricheMonitor Azure Data Explorer performance, health & usage with metrics
description: Informazioni su come usare le metriche di Azure Data Explorer per monitorare le prestazioni, l'integrità e l'utilizzo del cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560305"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitorare le prestazioni, l'integrità e l'utilizzo di Azure Data Explorer con le metricheMonitor Azure Data Explorer performance, health, and usage with metrics

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. Le metriche di Azure Data Explorer forniscono indicatori chiave relativi all'integrità e alle prestazioni delle risorse del cluster. Usare le metriche descritte in dettaglio in questo articolo per monitorare l'integrità e le prestazioni del cluster di Azure Data Explorer nello scenario specifico come metriche autonome. È anche possibile usare le metriche come base per i dashboard di Azure operativi e gli avvisi di Azure.You can also use metrics as the basis for operational [Azure Dashboards](/azure/azure-portal/azure-portal-dashboards) and [Azure Alerts](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone di uno, è possibile creare un [account Azure gratuito.](https://azure.microsoft.com/free/)
* Un [cluster e](create-cluster-database-portal.md)un database .

## <a name="using-metrics"></a>Utilizzo delle metriche

1. Accedere al [portale](https://portal.azure.com/)di Azure .
1. Nel cluster Azure Data Explorer selezionare **Metriche** per aprire il riquadro delle metriche e avviare l'analisi nel cluster.
    ![Selezionare](media/using-metrics/select-metrics.png)Metriche .
1. Nel riquadro ![Metriche: Metriche](media/using-metrics/metrics-pane.png)
    1. Per creare un grafico metrico, selezionare **Nome metrica** e **Aggregazione** pertinente per metrica. Le selezioni Spazio dei nomi **delle risorse** e delle **metriche** sono preselezionate per il cluster Azure Data Explorer.The Resource and Metric Namespace pickers are pre-selected for your Azure Data Explorer cluster. Per altre informazioni sulle diverse metriche, vedere [Metriche](#supported-azure-data-explorer-metrics)di Azure Data Explorer supportate.
    1. Selezionare **Aggiungi metrica** per visualizzare più metriche tracciate nello stesso grafico.
    1. Per visualizzare più grafici in un'unica visualizzazione, selezionare **: Nuovo grafico.**
    1. Usa il controllo selezione ora per modificare l'intervallo di tempo (impostazione predefinita: ultime 24 ore).
    1. Usa [ **Aggiungi filtro** e **Applica divisione** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) per le metriche con dimensioni.
    1. Selezionare **Aggiungi al dashboard** per aggiungere la configurazione del grafico ai dashboard in modo da poterlo visualizzare di nuovo.
    1. Impostare **Nuova regola** di avviso per visualizzare le metriche utilizzando i criteri impostati. La nuova regola di avviso includerà la risorsa di destinazione, la metrica, la suddivisione e le dimensioni di filtro del grafico. Modificare queste impostazioni nel [riquadro di creazione della regola](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)di avviso.

Ulteriori informazioni sull'utilizzo di [Esplora metriche](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Metriche di Azure Data Explorer supportateSupported Azure Data Explorer metrics

Le metriche di Azure Data Explorer supportate sono suddivise in varie categorie in base all'utilizzo. 

### <a name="cluster-health-metrics"></a>Metriche di integrità del cluster

Le metriche di integrità del cluster tengono traccia dell'integrità generale del cluster. Ciò include l'utilizzo delle risorse e dell'inserimento e la velocità di risposta.

**Metrica** | **Unità** | **Aggregazione** | **Descrizione della metrica** | **Dimensioni** |
|---|---|---|---|---|
| Utilizzo della cache | Percentuale | Avg, Max, Min | Percentuale di risorse della cache allocate attualmente utilizzate dal cluster. Cache è la dimensione di SSD allocata per l'attività dell'utente in base ai criteri di cache definiti. Un utilizzo medio della cache pari o inferiore all'80% è uno stato sostenibile per un cluster. Se l'utilizzo medio della cache è superiore all'80%, il cluster deve essere [scalato fino](manage-cluster-vertical-scaling.md) a un piano tariffario ottimizzato per l'archiviazione o [eseguito](manage-cluster-horizontal-scaling.md) per più istanze. In alternativa, adattare i criteri di cache (meno giorni nella cache). Se l'utilizzo della cache è superiore al 100%, la dimensione dei dati da memorizzare nella cache, in base ai criteri di memorizzazione nella cache, è maggiore della dimensione totale della cache nel cluster. | nessuno |
| CPU | Percentuale | Avg, Max, Min | Percentuale di risorse di calcolo allocate attualmente utilizzate dai computer nel cluster. Una CPU media pari o inferiore all'80% è sostenibile per un cluster. Il valore massimo della CPU è 100%, il che significa che non sono presenti risorse di calcolo aggiuntive per elaborare i dati. Quando un cluster non funziona bene, controllare il valore massimo della CPU per determinare se sono presenti CPU specifiche che sono bloccate. | nessuno |
| Utilizzo dell'ingestione | Percentuale | Avg, Max, Min | Percentuale di risorse effettive utilizzate per l'inserimento di dati dalle risorse totali allocate, nei criteri di capacità, per eseguire l'inserimento. Il criterio di capacità predefinito non è più di 512 operazioni di inserimento simultanee o il 75% delle risorse cluster investite nell'inserimento. L'utilizzo medio dell'ingestione pari o inferiore all'80% è uno stato sostenibile per un cluster. Il valore massimo di utilizzo dell'inserimento è 100%, il che significa che viene utilizzata tutta la capacità di inserimento del cluster e può verificarsi una coda di inserimento. | nessuno |
| Mantieni vivo | Conteggio | Avg | Tiene traccia della velocità di risposta del cluster. Un cluster completamente reattivo restituisce il valore 1 e un cluster bloccato o disconnesso restituisce 0.A fully responsive cluster returns value 1 and a blocked or disconnected cluster returns 0. |
| Numero totale di comandi limitati | Conteggio | Media, Max, Min, Somma | Il numero di comandi limitati (rifiutati) nel cluster, poiché è stato raggiunto il numero massimo consentito di comandi simultanei (paralleli). | nessuno |
| Numero totale di estensioni | Conteggio | Media, Max, Min, Somma | Numero totale di estensioni di dati nel cluster. Le modifiche in questa metrica possono implicare enormi modifiche della struttura dei dati e un carico elevato nel cluster, poiché l'unione degli extent di dati è un'attività elevata di CPU. | nessuno |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Esportare le metriche relative all'integrità e alle prestazioni

Le metriche di integrità e prestazioni delle esportazioni tengono traccia dell'integrità generale e delle prestazioni delle operazioni di esportazione, ad esempio ritardo, risultati, numero di record e utilizzo.

**Metrica** | **Unità** | **Aggregazione** | **Descrizione della metrica** | **Dimensioni** |
|---|---|---|---|---|
Numero di esportazione continua di record esportati    | Conteggio | SUM | Numero di record esportati in tutti i processi di esportazione continua. | nessuno |
Esportazione continua ritardo massimo |    Conteggio   | Max   | Il ritardo (in minuti) segnalato dai processi di esportazione continua nel cluster. | nessuno |
Conteggio continuo esportazione in sospeso | Conteggio | Max   | Numero di processi di esportazione continua in sospeso. Questi processi sono pronti per l'esecuzione ma in attesa in una coda, probabilmente a causa di capacità insufficiente). 
Risultato dell'esportazione continua    | Conteggio |   Conteggio   | Risultato di ogni esecuzione dell'esportazione continua. | ContinuousExportName (NomeEsportazione continua) |
Utilizzo delle esportazioni |    Percentuale | Max   | Capacità di esportazione utilizzata, al di fuori della capacità di esportazione totale nel cluster (tra 0 e 100). | nessuno |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Metriche relative all'integrità e alle prestazioni dell'ingestione

Le metriche di integrità e prestazioni dell'inserimento tengono traccia dell'integrità generale e delle prestazioni delle operazioni di inserimento, ad esempio latenza, risultati e volume.

**Metrica** | **Unità** | **Aggregazione** | **Descrizione della metrica** | **Dimensioni** |
|---|---|---|---|---|
| Eventi elaborati (per hub evento/IoT) | Conteggio | Max, Min, Somma | Numero totale di eventi letti dagli hub eventi ed elaborati dal cluster. Gli eventi vengono suddivisi in eventi rifiutati e accettati dal motore del cluster. | EventStatus |
| Latenza di ingestione | Secondi | Avg, Max, Min | Latenza dei dati ingeriti, dal momento in cui i dati sono stati ricevuti nel cluster fino a quando non sono pronti per la query. Il periodo di latenza di inserimento dipende dallo scenario di inserimento. | nessuno |
| Risultato dell'ingestione | Conteggio | Conteggio | Numero totale di operazioni di inserimento non riuscite e riuscite. Utilizzare **applica divisione** per creare bucket di risultati riusciti e non riusciti e analizzare le dimensioni (**Stato****valore** > ).| IngestionResultDetails |
| Volume di ingestione (in MB) | Conteggio | Max, Somma | La dimensione totale dei dati ingeriti nel cluster (in MB) prima della compressione. | Database |
| | | | |  

### <a name="query-performance"></a>Prestazioni delle query

Le metriche delle prestazioni delle query tengono traccia della durata delle query e del numero totale di query simultanee o limitate.

**Metrica** | **Unità** | **Aggregazione** | **Descrizione della metrica** | **Dimensioni** |
|---|---|---|---|---|
| Durata delle query | Millisecondi | Media, Min, Max, Somma | Tempo totale fino alla ricezione dei risultati della query (non include la latenza di rete). | QueryStatus |
| Numero totale di query simultanee | Conteggio | Media, Max, Min, Somma | Il numero di query eseguite in parallelo nel cluster. Questa metrica è un buon modo per stimare il carico sul cluster. | nessuno |
| Numero totale di query limitate | Conteggio | Media, Max, Min, Somma | Numero di query limitate (rifiutate) nel cluster. Il numero massimo di query simultanee (parallele) consentite è definito nei criteri di query simultanei. | nessuno |
| | | | |

### <a name="streaming-ingest-metrics"></a>Metriche di inserimento in streaming

Le metriche di inserimento in streaming tengono traccia dei dati di inserimento in streaming e della frequenza, della durata e dei risultati delle richieste.

**Metrica** | **Unità** | **Aggregazione** | **Descrizione della metrica** | **Dimensioni** |
|---|---|---|---|---|
Velocità dati di inserimento in streamingStreaming Ingest Data Rate |    Conteggio   | RateRequestsPerSecond | Volume totale dei dati ingeriti nel cluster. | nessuno |
Durata dell'inserimento in streaming   | Millisecondi  | Avg, Max, Min | Durata totale di tutte le richieste di inserimento in streaming. | nessuno |
Frequenza richieste inserimento in streaming   | Conteggio | Conteggio, Media, Max, Min, Somma | Numero totale di richieste di inserimento in streaming. | nessuno |
Risultato dell'inserimento in streaming | Conteggio | Avg   | Numero totale di richieste di inserimento in streaming per tipo di risultato. | Risultato |
| | | | |

Ulteriori informazioni sulle [metriche](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)del cluster di Azure Data Explorer supportate.


## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Eseguire l'inserimento dei dati e il monitoraggio delle query in Esplora dati di AzureTutorial: Ingest and query monitoring data in Azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Monitorare le operazioni di inserimento in Esplora dati di Azure usando i log di diagnostica](/azure/data-explorer/using-diagnostic-logs)
* [Avvio rapido: Eseguire query sui dati in Esplora dati di Azure](web-query-data.md)
