---
title: Monitora le prestazioni, l'integrità e l'utilizzo di Azure Esplora dati con le metriche
description: Informazioni su come usare le metriche di Esplora dati di Azure per monitorare le prestazioni, l'integrità e l'utilizzo del cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: f8078d8bae00ac4789a679be4d7a1944c749cce6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423845"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitora le prestazioni, l'integrità e l'utilizzo di Azure Esplora dati con le metriche

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. Le metriche di Azure Esplora dati forniscono indicatori chiave per l'integrità e le prestazioni delle risorse del cluster. Usare le metriche descritte in dettaglio in questo articolo per monitorare l'integrità e le prestazioni del cluster Esplora dati di Azure nello scenario specifico come metriche autonome. È anche possibile usare le metriche come base per i Dashboard operativi di [Azure](/azure/azure-portal/azure-portal-dashboards) e gli [avvisi di Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha un [account Azure](https://azure.microsoft.com/free/), è possibile crearne uno gratuito.
* Un [cluster e un database](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Uso delle metriche

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Nel cluster di Azure Esplora dati selezionare **metrica** per aprire il riquadro metriche e iniziare l'analisi nel cluster.
    ![selezionare](media/using-metrics/select-metrics.png)metrica.
1. Nel riquadro metriche: ![riquadro metriche](media/using-metrics/metrics-pane.png)
    1. Per creare un grafico delle metriche, selezionare nome della **metrica** e **aggregazione** pertinente per ogni metrica. I selezionatori **dello spazio dei nomi** della **risorsa** e della metrica sono pre-selezionati per il cluster Azure Esplora dati. Per altre informazioni sulle diverse metriche, vedere la pagina relativa alle [metriche di Azure Esplora dati supportate](#supported-azure-data-explorer-metrics).
    1. Selezionare **Aggiungi metrica** per visualizzare più metriche tracciate nello stesso grafico.
    1. Selezionare **+ nuovo grafico** per visualizzare più grafici in un'unica visualizzazione.
    1. Usare selezione ora per modificare l'intervallo di tempo (impostazione predefinita: ultime 24 ore).
    1. Usare [ **Aggiungi filtro** e **applicare la suddivisione** per le](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) metriche con dimensioni.
    1. Selezionare Aggiungi **al dashboard** per aggiungere la configurazione del grafico ai dashboard in modo che sia possibile visualizzarla di nuovo.
    1. Impostare **nuova regola di avviso** per visualizzare le metriche usando i criteri di impostazione. La nuova regola di avviso includerà la risorsa di destinazione, la metrica, la suddivisione e le dimensioni di filtro del grafico. Modificare queste impostazioni nel [riquadro di creazione della regola di avviso](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Informazioni aggiuntive sull'utilizzo del [Esplora metriche](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Metriche di Azure Esplora dati supportate

Le metriche di Azure Esplora dati supportate sono suddivise in diverse categorie in base all'utilizzo. 

### <a name="cluster-health-metrics"></a>Metriche di integrità del cluster

Le metriche di integrità del cluster tengono traccia dell'integrità generale del cluster. Sono inclusi l'utilizzo delle risorse e dell'inserimento e la velocità di risposta.

**Metrica** | **Unità** | **Aggregazione** | **Descrizione metrica** | **Dimensioni** |
|---|---|---|---|---|
| Utilizzo della cache | Percentuale | AVG, Max, min | Percentuale di risorse della cache allocata attualmente in uso dal cluster. Cache è la dimensione dell'unità SSD allocata per l'attività utente in base ai criteri di cache definiti. Un utilizzo medio della cache del 80% o meno è uno stato sostenibile per un cluster. Se l'utilizzo medio della cache è superiore al 80%, il cluster deve essere [scalato fino](manage-cluster-vertical-scaling.md) a un piano tariffario ottimizzato per l'archiviazione o [ridimensionato](manage-cluster-horizontal-scaling.md) a più istanze. In alternativa, adattare i criteri di cache (un minor numero di giorni nella cache). Se l'utilizzo della cache è superiore al 100%, la dimensione dei dati da memorizzare nella cache, in base ai criteri di memorizzazione nella cache, è maggiore delle dimensioni totali della cache nel cluster. | None |
| CPU | Percentuale | AVG, Max, min | Percentuale di risorse di calcolo allocate attualmente utilizzate dai computer del cluster. Una CPU media di 80% o meno è sostenibile per un cluster. Il valore massimo della CPU è pari al 100%, ovvero non sono disponibili risorse di calcolo aggiuntive per elaborare i dati. Quando un cluster non funziona correttamente, controllare il valore massimo della CPU per determinare se sono presenti CPU specifiche bloccate. | None |
| Utilizzo dell'inserimento | Percentuale | AVG, Max, min | Percentuale di risorse effettive usate per inserire i dati dalle risorse totali allocate, nei criteri di capacità, per eseguire l'inserimento. I criteri di capacità predefiniti non sono più di 512 operazioni di inserimento simultanee o il 75% delle risorse del cluster investito nell'inserimento. L'utilizzo medio dell'inserimento del 80% o meno è uno stato sostenibile per un cluster. Il valore massimo di utilizzo dell'inserimento è pari al 100%, il che significa che viene utilizzata tutta la capacità di inserimento del cluster e che può verificarsi una coda di inserimento. | None |
| Keep-Alive | Conteggio | Medio | Rileva la velocità di risposta del cluster. Un cluster completamente reattivo restituisce il valore 1 e un cluster bloccato o disconnesso restituisce 0. |
| Numero totale di comandi limitati | Conteggio | AVG, Max, min, Sum | Numero di comandi limitati (rifiutati) nel cluster, poiché è stato raggiunto il numero massimo consentito di comandi simultanei (paralleli). | None |
| Numero totale di extent | Conteggio | AVG, Max, min, Sum | Numero totale di extent dei dati nel cluster. Le modifiche apportate a questa metrica possono implicare modifiche massive della struttura dei dati e un carico elevato sul cluster, poiché l'Unione degli extent dei dati è un'attività con utilizzo intensivo della CPU | None |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Esportare le metriche di integrità e prestazioni

Le metriche delle prestazioni e dello stato di esportazione tengono traccia dell'integrità e delle prestazioni generali delle operazioni di esportazione, ad esempio la latenza, i risultati, il numero di record e l'utilizzo.

**Metrica** | **Unità** | **Aggregazione** | **Descrizione metrica** | **Dimensioni** |
|---|---|---|---|---|
Numero di esportazioni continue dei record esportati    | Conteggio | Sum | Numero di record esportati in tutti i processi di esportazione continua. | None |
Latenza massima di esportazione continua |    Conteggio   | Max   | Ritardo (in minuti) segnalato dai processi di esportazione continua nel cluster. | None |
Conteggio in sospeso esportazione continua | Conteggio | Max   | Numero di processi di esportazione continua in sospeso. Questi processi sono pronti per l'esecuzione, ma in attesa in una coda, probabilmente a causa di una capacità insufficiente. 
Risultato dell'esportazione continua    | Conteggio |   Conteggio   | Esito positivo o negativo di ogni esecuzione di esportazione continua. | ContinuousExportName |
Esporta utilizzo |    Percentuale | Max   | Capacità di esportazione utilizzata, al di fuori della capacità di esportazione totale nel cluster (compreso tra 0 e 100). | None |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Misurazione dello stato e delle prestazioni di inserimento

Le metriche relative a integrità e prestazioni di inserimento tengono traccia dell'integrità generale e delle prestazioni delle operazioni di inserimento, ad esempio latenza, risultati e volume.

**Metrica** | **Unità** | **Aggregazione** | **Descrizione metrica** | **Dimensioni** |
|---|---|---|---|---|
| Eventi elaborati (per gli hub eventi/tutto) | Conteggio | Max, min, Sum | Numero totale di eventi letti dall'hub eventi ed elaborati dal cluster. Gli eventi vengono suddivisi in eventi rifiutati ed eventi accettati dal motore del cluster. | EventStatus |
| Latenza inserimento | Secondi | AVG, Max, min | Latenza dei dati inseriti, dal momento in cui i dati sono stati ricevuti nel cluster fino a quando non sono pronti per la query. Il periodo di latenza di inserimento dipende dallo scenario di inserimento. | None |
| Risultato inserimento | Conteggio | Conteggio | Numero totale di operazioni di inserimento non riuscite e riuscite. Utilizzare **applica suddivisione** per creare bucket di esito positivo e risultati non riusciti e analizzare le dimensioni (**valore** > **stato**).| IngestionResultDetails |
| Volume di inserimento (in MB) | Conteggio | Max, Sum | Dimensioni totali dei dati inseriti nel cluster (in MB) prima della compressione. | Database |
| | | | |  

### <a name="query-performance"></a>Prestazioni delle query

Le metriche delle prestazioni delle query tengono traccia della durata delle query e del numero totale di query simultanee o limitate.

**Metrica** | **Unità** | **Aggregazione** | **Descrizione metrica** | **Dimensioni** |
|---|---|---|---|---|
| Durata delle query | Millisecondi | AVG, min, Max, Sum | Tempo totale fino alla ricezione dei risultati della query (non include la latenza di rete). | QueryStatus |
| Numero totale di query simultanee | Conteggio | AVG, Max, min, Sum | Il numero di query eseguite in parallelo nel cluster. Questa metrica è un modo efficace per stimare il carico sul cluster. | None |
| Numero totale di query limitate | Conteggio | AVG, Max, min, Sum | Numero di query limitate (rifiutate) nel cluster. Il numero massimo di query simultanee (parallele) consentite è definito nei criteri di query simultanei. | None |
| | | | |

### <a name="streaming-ingest-metrics"></a>Metriche di inserimento di flussi

Le metriche di inserimento del flusso tengono traccia dei dati di inserimento del flusso e della frequenza delle richieste, della durata e dei risultati.

**Metrica** | **Unità** | **Aggregazione** | **Descrizione metrica** | **Dimensioni** |
|---|---|---|---|---|
Velocità dati di inserimento dei flussi |    Conteggio   | RateRequestsPerSecond | Volume totale dei dati inseriti nel cluster. | None |
Durata inserimento flusso   | Millisecondi  | AVG, Max, min | Durata totale di tutte le richieste di inserimento di flussi. | None |
Frequenza delle richieste di inserimento di flussi   | Conteggio | Count, AVG, Max, min, Sum | Numero totale di richieste di inserimento di flussi. | None |
Risultato inserimento flusso | Conteggio | Medio   | Numero totale di richieste di inserimento di flussi in base al tipo di risultato. | Risultato |
| | | | |

Altre informazioni sulle [metriche del cluster di Azure Esplora dati supportate](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: inserire ed eseguire query sui dati di monitoraggio in Azure Esplora dati](/azure/data-explorer/ingest-data-no-code)
* [Monitorare le operazioni di inserimento in Esplora dati di Azure usando i log di diagnostica](/azure/data-explorer/using-diagnostic-logs)
* [Guida introduttiva: Eseguire query sui dati in Esplora dati di Azure](web-query-data.md)
