---
title: Risolvere i problemi relativi a un processo ricorrente anomalo| Microsoft Docs
description: Informazioni su come usare gli Strumenti Azure Data Lake per Visual Studio per eseguire il debug di un processo ricorrente anomalo.
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: 9b60c861810d6577b33aa0cdf14f26dc2cfc0e4d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Risolvere i problemi relativi a un processo ricorrente anomalo

Questo articolo illustra come usare gli [Strumenti Azure Data Lake per Visual Studio](http://aka.ms/adltoolsvs) per risolvere i problemi relativi ai processi ricorrenti. Per altre informazioni sui processi della pipeline e ricorrenti, vedere il [blog su Azure Data Lake e Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

I processi ricorrenti condividono generalmente la stessa logica di query e dati di input simili. Ad esempio si supponga di avere un processo ricorrente che viene eseguito ogni lunedì mattina alle 8:00 per calcolare l'utente più attivo della settimana per la settimana precedente. Gli script per questi processi condividono un solo modello di script che contiene la logica di query. Gli input di questi processi sono i dati di utilizzo della settimana precedente. La condivisione della stessa logica di query e input simili comporta in genere che le prestazioni di questi processi siano simili e stabili. Se uno dei processi ricorrenti improvvisamente viene eseguito in modo anomalo, non viene eseguito o rallenta molto, potrebbe essere utile:

- Vedere i report delle statistiche per le esecuzioni precedenti del processo ricorrente, in modo da capire cosa sia successo.
- Confrontare il processo anomalo con uno normale per scoprire cos'è cambiato.

**Related Job View** (Visualizzazione dei processi correlati) in Strumenti Azure Data Lake per Visual Studio consente di accelerare lo stato di risoluzione dei problemi con entrambi i casi.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Passaggio 1: Trovare i processi ricorrenti e aprire Related Job View (Visualizzazione dei processi correlati)

Per usare Related Job View (Visualizzazione processi correlati) per risolvere il problema relativo al processo ricorrente, è necessario innanzitutto individuare il processo ricorrente in Visual Studio e quindi aprire Related Job View (Visualizzazione processi correlati).

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Caso 1: Si dispone dell'URL per il processo ricorrente

Tramite **Strumenti** > **Data Lake** > **Job View** (Visualizzazione processi), è possibile incollare l'URL di processo per aprire la visualizzazione processi in Visual Studio. Selezionare **View Related Jobs** (Visualizza processi correlati) per aprire la visualizzazione processi correlati.

![Collegamento View Related Jobs (Visualizza processi correlati) in Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Caso 2: Si dispone della pipeline per il processo ricorrente, ma non dell'URL

In Visual Studio è possibile aprire il browser pipeline tramite Esplora server > account di Azure Data Lake Analytics > **Pipeline**. Se non è possibile trovare il nodo in Esplora server, [scaricare la versione più recente del plug-in](http://aka.ms/adltoolsvs). 

![Selezione del nodo Pipeline](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

Nel browser pipeline tutte le pipeline per l'account Data Lake Analytics sono elencate a sinistra. È possibile espandere le pipeline per trovare tutti i processi ricorrenti e quindi selezionare quello che presenta problemi. Viene mostrata la visualizzazione processi correlati a destra.

![Selezione di una pipeline e apertura della visualizzazione processi correlati](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Passaggio 2: Analizzare un report delle statistiche

Un riepilogo e un report delle statistiche sono mostrati nella parte superiore della visualizzazione processi correlati. Qui è possibile trovare la potenziale causa radice del problema. 

1.  Nel report l'asse X indica l'ora di invio del processo. Usarla per trovare il processo anomalo.
2.  Usare il processo nel diagramma seguente per controllare le statistiche e ottenere informazioni dettagliate sul problema e le possibili soluzioni.

![Diagramma del processo per il controllo delle statistiche](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Passaggio 3: Confrontare il processo anomalo con un processo normale

È possibile trovare tutti i processi ricorrenti inviati nell'elenco dei processi in fondo alla visualizzazione processi correlati. Per vedere altre informazioni e possibili soluzioni, fare doppio clic con il pulsante destro del mouse sul processo anomalo. Usare la visualizzazione Differenze processi per confrontare il processo anomalo con un processo normale precedente.

![Menu di scelta rapida per confrontare i processi](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Prestare attenzione alle principali differenze tra questi due processi. Queste differenze potrebbero causare problemi di prestazioni. Per controllare ulteriormente, usare i passaggi nel diagramma seguente:

![Diagramma della procedura di controllo delle differenze tra i processi](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di asimmetria dei dati](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Eseguire il debug del codice C# definito dall'utente per i processi U-SQL non riusciti](data-lake-analytics-debug-u-sql-jobs.md)