---
title: Testare il processo di Analisi di flusso di Azure con dati di esempio | Microsoft Docs
description: Informazioni su come testare le query nei processi di analisi di flusso.
keywords: testare un processo, campionamento dell'input, caricare data di esempio
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>Testare la query di Analisi di flusso con dati di esempio

La funzionalità Analisi di flusso di Azure consente di caricare i dati di esempio e testare le query nel portale senza dover avviare o arrestare un processo.

## <a name="upload-sample-data-and-test-the-query"></a>Caricare i dati di esempio e testare la query

1. Passare a uno dei processi di Analisi di flusso di Azure esistenti > fare clic su **Query** per aprire la finestra Editor di query. 

2. Per eseguire il test della query con dati di input di esempio, fare clic con il pulsante destro del mouse su uno degli input e quindi selezionare **Carica dati di esempio da file**. Attualmente è possibile caricare solo dati in formato JSON. Se i dati sono in un formato diverso, ad esempio CSV, è necessario convertirli in formato JSON prima del caricamento. Per convertire i dati in formato JSON, è possibile usare qualsiasi strumento di conversione open source, ad esempio il [convertitore da CSV a JSON](http://www.convertcsv.com/csv-to-json.htm).

    ![Pulsante per il testo delle query nell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. Dopo avere completato il caricamento, fare clic su **Test** per testare la query usando i dati di esempio appena specificati.

    ![dati di esempio per il test nell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. Per consentire il salvataggio dei risultati del test per un utilizzo successivo, l'output della query viene visualizzato nel browser con un collegamento ai risultati del download. È possibile a questo punto modificare in modo facile e iterativo la query e testarla più volte per vedere come cambia l'output.

   ![Output di esempio dell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Quando si usano più output in una query, è possibile visualizzare i risultati di ogni output separatamente e passare facilmente da un output all'altro. Dopo aver verificato i risultati mostrati nel browser, è possibile salvare la query, avviare il processo e lasciarlo elaborare gli eventi senza errori.
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
