---
title: Stati del processo di Analisi di flusso di Azure
description: Questo articolo descrive i quattro stati diversi di un processo di analisi di flusso. in esecuzione, arrestato, danneggiato e non riuscito.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: c533463ff544dc315142f7fb95c34c67933f9614
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444257"
---
# <a name="azure-stream-analytics-job-states"></a>Stati del processo di Analisi di flusso di Azure

Un processo di analisi di flusso potrebbe trovarsi in uno dei quattro stati in un determinato momento: in esecuzione, arrestato, danneggiato o non riuscito. È possibile visualizzare lo stato del processo alla pagina della panoramica del processo di Analisi di flusso del portale di Azure. 

| Stato | DESCRIZIONE | Azioni consigliate |
| --- | --- | --- |
| **Running** | Il processo in esecuzione in Azure analizza gli eventi provenienti da origini di input definite, li elabora e scrive i risultati nei sink degli output configurati. | La procedura consigliata consiste nel tenere traccia delle prestazioni dei processi monitorando le [metriche chiave](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Stopped** | Il processo viene arrestato e non elabora gli eventi. | ND | 
| **Danneggiato** | Potrebbero verificarsi problemi intermittenti con le connessioni di input e di output. Questi errori sono detti errori temporanei che possono far entrare il processo in uno stato danneggiato. Analisi di flusso di Azure proverà a porre rimedio agli errori immediatamente e a ripristinare lo stato di esecuzione (in pochi minuti). Questi errori potrebbero verificarsi a causa di problemi di rete, disponibilità di altre risorse di Azure, errori di deserializzazione e così via. Quando il processo è in stato Danneggiato, le prestazioni potrebbero essere alterate.| È possibile esaminare i [log di diagnostica o attività](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) per disporre di altre informazioni sulla causa di questi errori temporanei. In casi come errori di deserializzazione, è consigliabile adottare misure correttive per assicurarsi che gli eventi non siano danneggiati. Se il processo continua a raggiungere il limite di utilizzo della risorsa, provare ad aumentare il numero dell'unità di archiviazione o a [eseguire il processo in parallelo](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). In altri casi in cui non è possibile eseguire alcuna azione, Analisi di flusso di Azure proverà a ripristinare uno stato di *Esecuzione*. <br> È possibile usare la metrica [ritardo filigrana](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) per comprendere se questi errori temporanei influisce sulle prestazioni del processo.|
| **Non riuscito** | Lo stato è Non riuscito perché il processo in corso ha rilevato un errore critico. La lettura e l'elaborazione degli eventi sono interrotte. Gli errori di runtime sono spesso alla base dei processi in stato Non riuscito. | È possibile [configurare gli avvisi](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) in modo che si riceva una notifica quando un processo passa allo stato Non riuscito. <br> <br>È possibile eseguire il debug usando i [log di diagnostica e attività](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) per identificare la causa radice e risolvere il problema.|

## <a name="next-steps"></a>Passaggi successivi
* [Impostare gli avvisi per i processi di Analisi di flusso di Azure](stream-analytics-set-up-alerts.md)
* [Metriche disponibili in Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Risoluzione dei problemi usando i log di diagnostica](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
