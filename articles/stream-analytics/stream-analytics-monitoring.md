---
title: Informazioni sul monitoraggio dei processi di Analisi di flusso | Documentazione Microsoft
description: Informazioni sul monitoraggio dei processi di Analisi di flusso
keywords: monitoraggio di query
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 5f5cc00f-4a7b-491e-89e1-dbafea46d399
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 7474f45494c6190ffcac354e75458b18f5777fb9
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Informazioni sul monitoraggio dei processi di Analisi di flusso e su come monitorare le query

## <a name="introduction-the-monitor-page"></a>Introduzione: Pagina di monitoraggio
Il portale di Azure evidenzia le metriche delle prestazioni chiave che possono essere usate per monitorare e consente di risolvere i problemi di prestazioni delle query e dei processi. Per visualizzare queste metriche, esplorare il processo di analisi di flusso di cui si desidera vedere le metriche e visualizzare la sezione **Monitoraggio** nella pagina della panoramica.  

![Collegamento al monitoraggio](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Verrà visualizzata la finestra mostrata di seguito:

![Monitoraggio del dashboard dei processi](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metriche disponibili per l'analisi di flusso
| Metrica                 | Definizione                               |
| ---------------------- | ---------------------------------------- |
| % utilizzo unità di streaming       | L'utilizzo delle unità di streaming assegnate a un processo dalla scheda Scalabilità del processo. Se questo indicatore raggiunge l'80% o più, esiste una forte probabilità che l'elaborazione di eventi possa essere ritardata o interrotta. |
| Eventi di input           | Quantità di dati ricevuta dal processo di Analisi di flusso, in termini di numero di eventi. Può essere usata per convalidare l'invio degli eventi all'origine di input. |
| Eventi di output          | Quantità di dati inviata dal processo di Analisi di flusso alla destinazione di output, in termini di numero di eventi. |
| Eventi non ordinati    | Numero di eventi non ordinati ricevuti che sono stati eliminati o a cui è stato assegnato un timestamp modificato, in base ai Criteri di ordinamento eventi. Può essere influenzato dalla configurazione dell'impostazione della Finestra di tolleranza elementi non in ordine. |
| Errori di conversione dati | numero di errori di conversione dei dati rilevati da un processo di Analisi di flusso. |
| Errori di runtime         | Numero totale di errori correlati all'elaborazione delle query (esclusi gli errori rilevati durante l'inserimento di eventi o l'output dei risultati) |
| Ultimi eventi di input      | Numero di eventi che arrivano in ritardo dall'origine che sono stati eliminati oppure il cui timestamp è stato modificato, in base alla configurazione dei Criteri di ordinamento eventi dell'impostazione della Finestra di tolleranza elementi non in ordine. |
| Richieste di funzioni      | Numero di chiamate alla funzione di Azure Machine Learning (se presente). |
| Richieste di funzioni non riuscite | Numero di chiamate non riuscite alla funzione di Azure Machine Learning (se presente). |
| Eventi di funzioni        | Numero di chiamate inviate alla funzione di Azure Machine Learning (se presente). |
| Byte evento di input      | Quantità di dati ricevuta dal processo di Analisi di flusso, in termini di byte. Può essere usata per convalidare l'invio degli eventi all'origine di input. |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizzazione del monitoraggio nel portale di Azure
È possibile modificare il tipo di grafico, le metriche visualizzate e l’intervallo di tempo nelle impostazioni di Modifica grafico. Per altre informazioni, vedere [How to Customize Monitoring](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) (Come personalizzare il monitoraggio).

  ![Monitoraggio query, grafico cronologico](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Output più recente
Un altro interessante punto dati per monitorare il processo è l'ora dell'ultimo output, visualizzata nella pagina di panoramica.
L'ora visualizzata corrisponde al tempo di applicazione (ovvero l'ora del timestamp relativo ai dati dell'evento) dell'output più recente del processo.

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

