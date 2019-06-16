---
title: Monitorare le prestazioni, integrità e sull'utilizzo con le metriche di Esplora dati di Azure
description: Informazioni su come usare le metriche di Esplora dati di Azure per monitorare le prestazioni, integrità e sull'utilizzo del cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: a9c9f4d827d21c374bebba9d39e33b0bcad8a83e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60826799"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitorare le prestazioni, integrità e sull'utilizzo con le metriche di Esplora dati di Azure

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. Metriche di Esplora dati di Azure forniscono gli indicatori chiave per quanto riguarda l'integrità e prestazioni delle risorse del cluster. Usare le metriche che sono descritte in dettaglio in questo articolo per monitorare l'integrità del cluster Esplora dati di Azure e le prestazioni in uno scenario specifico come metriche autonomo. È anche possibile usare metriche come base per operational [i dashboard di Azure](/azure/azure-portal/azure-portal-dashboards) e [avvisi di Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Prerequisiti

* Se non hai una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/).

* Creare un [cluster e database](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="using-metrics"></a>L'uso delle metriche

Nel cluster di Esplora dati di Azure, selezionare **metriche** per aprire il riquadro metriche e di avviare analisi sul cluster.

![Selezionare Metriche](media/using-metrics/select-metrics.png)

Nel riquadro delle metriche:

![Riquadro metriche](media/using-metrics/metrics-pane.png)

1. Per creare un grafico delle metriche, selezionare **Metric** nome e rilevanti **aggregazione** per ogni metrica come descritto in dettaglio di seguito. Il **Resource** e **Namespace metrica** selezioni sono già selezionati per il cluster di Esplora dati di Azure.

    **Metrica** | **Unità** | **Aggregazione** | **Descrizione della metrica**
    |---|---|---|---|
    | Uso della cache | Percent | Avg, Max, Min | Percentuale delle risorse di cache allocate attualmente in uso da parte del cluster. Cache si riferisce alle dimensioni dell'unità SSD allocata per l'attività degli utenti in base ai criteri di cache definito. Un utilizzo della cache medio dell'80% o meno è uno stato sostenibile per un cluster. Se l'utilizzo medio della cache è superiore all'80%, il cluster deve essere [aumentato](manage-cluster-scale-up.md) in un archivio ottimizzato piano tariffario o [aumentato](manage-cluster-scale-out.md) alle altre istanze. In alternativa, adattare i criteri di cache (un minor numero di giorni nella cache). Se l'uso della cache è superiore a 100%, la dimensione dei dati da memorizzare nella cache, in base ai criteri di memorizzazione nella cache, è più grande che le dimensioni totali della cache nel cluster. |
    | CPU | Percent | Avg, Max, Min | Percentuale delle risorse di calcolo allocate attualmente in uso dal computer del cluster. Un utilizzo medio della CPU dell'80% o meno è sostenibile per un cluster. Il valore massimo della CPU è 100%, ovvero non sono presenti risorse di calcolo aggiuntive per elaborare i dati. Quando un cluster non è soddisfacenti, controllare il valore massimo della CPU per determinare se sono presenti CPU specifiche che vengono bloccate. |
    | Eventi elaborati (per gli hub eventi) | Conteggio | Max, Min, Sum | Numero totale di eventi di lettura da hub eventi ed elaborati dal cluster. Gli eventi vengono suddivisi in eventi rifiutati e accettato dal motore di cluster. |
    | Latenza di inserimento | Secondi | Avg, Max, Min | Latenza di inserimento, dal momento in cui che sono stati ricevuti i dati nel cluster fino a quando non è pronto per le query di dati. Il periodo di latenza di inserimento varia a seconda dello scenario di inserimento. |
    | Risultato di inserimento | Conteggio | Conteggio | Numero totale di operazioni di inserimento che non è riuscita e ha avuto esito positivo. Uso **applica la suddivisione** creare bucket di successo e avere esito negativo dei risultati e analizzare le dimensioni (**valore** > **stato**).|
    | Utilizzo di inserimento | Percent | Avg, Max, Min | Percentuale delle risorse effettive usato per inserire dati nelle risorse totale allocata, nei criteri di capacità, per eseguire l'inserimento. I criteri di capacità predefinita non sono più di 512 operazioni di inserimento simultanee o 75% delle risorse del cluster investite nel processo di inserimento. L'utilizzo medio inserimento dell'80% o meno è uno stato sostenibile per un cluster. Valore massimo dell'utilizzo di inserimento è 100%. questo significa che tutte le capacità di inserimento dei cluster viene usato e potrebbe causare una coda di inserimento. |
    | Volume di inserimento (in MB) | Conteggio | Max, Min, Sum | Le dimensioni totali dei dati inseriti nel cluster (in MB) prima della compressione. |
    | Keep-alive | Conteggio | AVG | Registra la velocità di risposta del cluster. Un cluster completamente reattivo restituisce il valore 1 e un cluster disconnesso o bloccato restituisce 0. |
    | Durata delle query | Secondi | Count, Avg, Min, Max, Sum | Totale tempo fino a quando non vengono ricevuti i risultati della query (non include la latenza di rete). |
    | | | |

    Informazioni aggiuntive relative a [supportate le metriche dei cluster Esplora dati di Azure](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Selezionare il **Aggiungi metrica** pulsante per visualizzare più metriche tracciate indicano valori nello stesso grafico.
3. Selezionare il **+ nuovo grafico** pulsante per visualizzare grafici più in un'unica visualizzazione.
4. Usare la selezione di tempo per modificare l'intervallo di tempo (impostazione predefinita: ultime 24 ore).
5. Uso [ **aggiungere i filtri** e **applicare suddivisione** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) per metriche con dimensioni.
6. Selezionare **Aggiungi al dashboard** per aggiungere la configurazione del grafico per i dashboard in modo che è possibile visualizzarlo nuovamente.
7. Impostare **nuova regola di avviso** per visualizzare le metriche usando i criteri di gruppo. La nuova regola di avviso includerà la risorsa di destinazione, la metrica, la suddivisione e le dimensioni di filtro del grafico. Modificare queste impostazioni nella finestra di [riquadro di creazione della regola di avviso](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Altre informazioni sull'uso di [Esplora metriche](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: Eseguire query sui dati in Esplora dati di Azure](web-query-data.md)
