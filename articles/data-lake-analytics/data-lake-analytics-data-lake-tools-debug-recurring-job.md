---
title: Come risolvere i problemi relativi a un processo ricorrente anomalo| Microsoft Docs
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
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>Come risolvere i problemi relativi a un processo ricorrente anomalo

Questo documento illustra come usare gli [Strumenti Azure Data Lake per Visual Studio](http://aka.ms/adltoolsvs) per risolvere i problemi relativi a un processo ricorrente. Per altre informazioni sulla pipeline e i processi ricorrenti vedere [qui](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).
I processi ricorrenti condividono generalmente la stessa logica di query e dati di input simili. Ad esempio, si dispone di un processo ricorrente in esecuzione ogni lunedì mattina alle 8:00 per contare l'utente attivo settimanale dell'ultima settimana, gli script per questi processi condividono un modello di script che contiene la logica di query e gli input per questi processi sono i dati di utilizzo dell'ultima settimana. La condivisione di una stessa logica di query e di un input simile di solito sta a significare che le prestazioni di questi processi sono simili e stabili. Se improvvisamente il funzionamento di uno dei processi ricorrenti risulta anomalo, fallisce o è molto rallentato, è possibile:

1.  Vedere i report delle statistiche per le anteprime delle esecuzioni del processo ricorrente, in modo da capire cosa è successo.
2.  Confrontare il processo anomalo con uno normale per scoprire cos'è cambiato.

**Related Job View** (Visualizzazione dei processi correlati) in Strumenti Azure Data Lake per Visual Studio consente di accelerare lo stato di risoluzione dei problemi con entrambi i casi.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Passaggio 1: Trovare i processi ricorrenti e aprire Related Job View (Visualizzazione dei processi correlati)

Per usare Related Job View (Visualizzazione dei processi correlati) per risolvere il problema relativo al processo ricorrente, è necessario innanzitutto individuare il processo ricorrente in Visual Studio e quindi aprire Related Job View (Visualizzazione dei processi correlati).

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Caso 1: Si dispone dell'URL per il processo ricorrente

Selezionando **Tools > Data Lake > Job View** (Strumenti > Data Lake > Vista processi), è possibile incollare l'URL del processo per aprire Job View (Vista processi) in Visual Studio e selezionare View Related Jobs (Visualizza processi correlati) per aprire la Related Job View (Visualizzazione dei processi correlati).

![Data Lake Analytics Tools View Related Jobs](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Caso 2: Si dispone della pipeline per il processo ricorrente, ma non dell'URL

In Visual Studio, è possibile aprire il Browser della pipeline tramite **Esplora server > l'account Data Lake Analytics > Pipelines (Pipeline)** (se non si trova il nodo in Esplora server, scaricare lo strumento [qui](http://aka.ms/adltoolsvs)). Nel Browser della pipeline sono elencate a sinistra tutte le pipeline per l'account ADLA, è possibile espandere le pipeline per trovare tutti i processi ricorrenti. Fare clic su quello che presenta dei problemi e a destra si apre la Related Job View (Visualizzazione dei processi correlati).

![Data Lake Analytics Tools View Related Jobs](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![Data Lake Analytics Tools View Related Jobs](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>Passaggio 2: Analizzare i report delle statistiche

Nella parte superiore della Related Job View (Visualizzazione dei processi correlati) sono visualizzati un riepilogo e un report delle statistiche e si può ottenere la possibile causa radice dell'anomalia. 

1.  È necessario innanzitutto individuare il processo anomalo nel report. L'asse X mostra il tempo di invio del processo tramite cui è possibile individuare il processo anomalo.
2.  Seguire il processo riportato in basso per verificare le statistiche e ottenere informazioni dettagliate sull'anomalia e le possibili soluzioni.

![Data Lake Analytics Tools View Related Jobs](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>Passaggio 3: Confrontare il processo ricorrente anomalo con un processo normale

È possibile trovare tutti i processi ricorrenti inviati nell'elenco dei processi in fondo alla Related Job View (Visualizzazione dei processi correlati). Facendo clic con il pulsante destro del mouse è possibile confrontare il processo anomalo con uno normale precedente per trovare altre informazioni dettagliate e possibili soluzioni nella visualizzazione Differenze processi.

![Data Lake Analytics Tools View Related Jobs](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

In genere è necessario prestare attenzione alle principali differenze tra questi 2 processi, poiché sono probabilmente i motivi che causano problemi di prestazioni. È inoltre possibile osservare i passaggi seguenti per effettuare altre verifiche.

![Data Lake Analytics Tools View Related Jobs](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Passaggi successivi

* [Come eseguire il debug e risolvere i problemi di asimmetria dei dati](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Come eseguire il debug di un errore di processo U-SQL per l'errore del codice definito dall'utente](data-lake-analytics-debug-u-sql-jobs.md)