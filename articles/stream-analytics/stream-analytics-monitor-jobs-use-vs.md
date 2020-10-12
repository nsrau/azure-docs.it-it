---
title: Monitorare e gestire Analisi di flusso di Azure con Visual Studio
description: Questo articolo descrive come usare Visual Studio per monitorare e gestire i processi di Analisi di flusso di Azure.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 79d677caf0c57fb36d211333b28be0eaa6528948
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86044125"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Monitorare e gestire i processi di Analisi di flusso con Visual Studio

Questo articolo illustra come monitorare un processo di Analisi di flusso in Visual Studio. Gli strumenti di Analisi di flusso di Azure per Visual Studio offrono un'esperienza di monitoraggio simile al portale di Azure senza dover uscire dall'ambiente di sviluppo integrato. È possibile iniziare a monitorare un processo non appena questo viene **inviato ad Azure** da **Script.asaql** oppure è possibile monitorare i processi esistenti indipendentemente dal modo in cui sono stati creati. 

## <a name="job-summary"></a>Riepilogo processo

**Riepilogo processo** e **Metriche del processo** offrono una rapida panoramica di un processo consentendo di determinare in modo immediato le informazioni sugli eventi e lo stato del processo.

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Job Metrics (Metriche di processo)

È possibile comprimere **Riepilogo processo** e fare clic sulla scheda **Metriche del processo** per visualizzare un grafico con le metriche importanti. Selezionare e deselezionare i tipi di metriche per aggiungerle e rimuoverle dal grafico.

![Metrica di Analisi di flusso di Azure in Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Monitoraggio degli errori

È anche possibile monitorare gli errori facendo clic sulla scheda **Errori**.

![Errori di Analisi di flusso di Azure in Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Supporto
Per maggiore supporto, provare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Creare un processo di Analisi di flusso di Azure con Visual Studio](stream-analytics-quick-create-vs.md)
* [Installare gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 


