---
title: Monitora le prestazioni, l'integrità e l'utilizzo di Azure Esplora dati con le metriche
description: Informazioni su come usare le metriche di Esplora dati di Azure per monitorare le prestazioni, l'integrità e l'utilizzo del cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: f5b47a5ae9d13711233d0e4852ec487af7344622
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173781"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitora le prestazioni, l'integrità e l'utilizzo di Azure Esplora dati con le metriche

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. Le metriche di Azure Esplora dati forniscono indicatori chiave per l'integrità e le prestazioni delle risorse del cluster. Usare le metriche descritte in dettaglio in questo articolo per monitorare l'integrità e le prestazioni del cluster Esplora dati di Azure nello scenario specifico come metriche autonome. È anche possibile usare le metriche come base per i Dashboard operativi di [Azure](/azure/azure-portal/azure-portal-dashboards) e gli [avvisi di Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/).

* Creare un [cluster e un database](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="using-metrics"></a>Uso delle metriche

Nel cluster di Azure Esplora dati selezionare **metrica** per aprire il riquadro metriche e iniziare l'analisi nel cluster.

![Selezionare Metriche](media/using-metrics/select-metrics.png)

Nel riquadro metriche:

![Riquadro metriche](media/using-metrics/metrics-pane.png)

1. Per creare un grafico delle metriche, selezionare nome della **metrica** e **aggregazione** pertinente per ogni metrica, come descritto di seguito. I selezionatori **dello spazio dei nomi** della **risorsa** e della metrica sono preselezionati per il cluster Esplora dati di Azure.

    **Metrica** | **Unità** | **Aggregazione** | **Descrizione metrica**
    |---|---|---|---|
    | Utilizzo della cache | Percentuale | AVG, Max, min | Percentuale di risorse della cache allocata attualmente in uso dal cluster. Cache indica le dimensioni dell'unità SSD allocata per l'attività utente in base ai criteri di cache definiti. Un utilizzo medio della cache del 80% o meno è uno stato sostenibile per un cluster. Se l'utilizzo medio della cache è superiore al 80%, il cluster deve essere [scalato fino](manage-cluster-vertical-scaling.md) a un piano tariffario ottimizzato per l'archiviazione o [ridimensionato](manage-cluster-horizontal-scaling.md) a più istanze. In alternativa, adattare i criteri di cache (un minor numero di giorni nella cache). Se l'utilizzo della cache è superiore al 100%, la dimensione dei dati da memorizzare nella cache, in base ai criteri di memorizzazione nella cache, è maggiore delle dimensioni totali della cache nel cluster. |
    | CPU | Percentuale | AVG, Max, min | Percentuale di risorse di calcolo allocate attualmente utilizzate dai computer del cluster. Una CPU media di 80% o meno è sostenibile per un cluster. Il valore massimo della CPU è 100% e indica che non sono presenti risorse di calcolo aggiuntive per elaborare i dati. Quando un cluster non funziona correttamente, controllare il valore massimo della CPU per determinare se sono presenti CPU specifiche bloccate. |
    | Eventi elaborati (per hub eventi) | Numero | Max, min, Sum | Numero totale di eventi letti dall'hub eventi ed elaborati dal cluster. Gli eventi vengono suddivisi in eventi rifiutati ed eventi accettati dal motore del cluster. |
    | Latenza inserimento | Secondi | AVG, Max, min | Latenza dei dati inseriti, dal momento in cui i dati sono stati ricevuti nel cluster fino a quando non sono pronti per la query. Il periodo di latenza di inserimento dipende dallo scenario di inserimento. |
    | Risultato inserimento | Numero | Numero | Numero totale di operazioni di inserimento non riuscite e riuscite. Utilizzare **applica suddivisione** per creare bucket di esito positivo e risultati non riusciti e analizzare le dimensioni (**valore** > **stato**).|
    | Utilizzo dell'inserimento | Percentuale | AVG, Max, min | Percentuale di risorse effettive usate per inserire i dati dalle risorse totali allocate, nei criteri di capacità, per eseguire l'inserimento. I criteri di capacità predefiniti non sono più di 512 operazioni di inserimento simultanee o il 75% delle risorse del cluster investito nell'inserimento. L'utilizzo medio dell'inserimento del 80% o meno è uno stato sostenibile per un cluster. Il valore massimo di utilizzo dell'inserimento è pari al 100%, il che significa che viene utilizzata tutta la capacità di inserimento del cluster e che può verificarsi una coda di inserimento. |
    | Volume di inserimento (in MB) | Numero | Max, min, Sum | Dimensioni totali dei dati inseriti nel cluster (in MB) prima della compressione. |
    | Keep-Alive | Numero | AVG | Rileva la velocità di risposta del cluster. Un cluster completamente reattivo restituisce il valore 1 e un cluster bloccato o disconnesso restituisce 0. |
    | Durata delle query | Secondi | Count, AVG, min, Max, Sum | Tempo totale fino alla ricezione dei risultati della query (non include la latenza di rete). |
    | | | |

    Informazioni aggiuntive relative alle [metriche del cluster di Azure Esplora dati supportate](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Selezionare il pulsante **Aggiungi metrica** per visualizzare più metriche tracciate nello stesso grafico.
3. Selezionare il pulsante **+ nuovo grafico** per visualizzare più grafici in un'unica visualizzazione.
4. Usare selezione ora per modificare l'intervallo di tempo (impostazione predefinita: ultime 24 ore).
5. Usare [ **Aggiungi filtro** e **applicare la suddivisione** per le](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) metriche con dimensioni.
6. Selezionare Aggiungi **al dashboard** per aggiungere la configurazione del grafico ai dashboard in modo che sia possibile visualizzarla di nuovo.
7. Impostare **nuova regola di avviso** per visualizzare le metriche usando i criteri di impostazione. La nuova regola di avviso includerà la risorsa di destinazione, la metrica, la suddivisione e le dimensioni di filtro del grafico. Modificare queste impostazioni nel [riquadro di creazione della regola di avviso](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Informazioni aggiuntive sull'utilizzo del [Esplora metriche](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: inserire ed eseguire query sui dati di monitoraggio in Azure Esplora dati](/azure/data-explorer/ingest-data-no-code)
* [Monitorare le operazioni di inserimento Esplora dati di Azure usando i log di diagnostica](/azure/data-explorer/using-diagnostic-logs)
* [Guida introduttiva: Eseguire query sui dati in Esplora dati di Azure](web-query-data.md)
