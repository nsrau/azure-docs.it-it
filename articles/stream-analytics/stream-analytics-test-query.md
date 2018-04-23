---
title: Testare le query in Analisi di flusso di Azure
description: Questo articolo descrive come testare le query usando file di dati di esempio all'interno di processi di Analisi di flusso.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: fb7d936dfdc543c208ce98c588f5ad83704ff5dc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Testare le query della funzione di analisi di flusso nel portale di Azure

La funzione analisi di flusso di Azure consente di testare le query nel portale di Azure senza la necessità di avviare o arrestare un processo.

## <a name="test-the-input"></a>Testare l'input

1. Per eseguire il test con dati di input di esempio, fare clic con il pulsante destro del mouse su uno degli input e quindi selezionare **Carica dati di esempio da file**. Attualmente è possibile caricare solo dati in formato JSON. Se i dati sono in un formato diverso, ad esempio CSV, è necessario convertirli in formato JSON prima del caricamento. Per convertire i dati in formato JSON, è possibile usare qualsiasi strumento di conversione open source, ad esempio il [convertitore da CSV a JSON](http://www.convertcsv.com/csv-to-json.htm).

    ![Pulsante per il testo delle query nell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. Dopo avere completato il caricamento, fare clic su **Test** per testare la query usando i dati di esempio appena specificati.

    ![dati di esempio per il test nell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

L'output della query viene visualizzato nel browser con il collegamento dei risultati del download, qualora si volesse salvare i risultati del test per usarli in un secondo momento. È possibile a questo punto modificare in modo facile e iterativo la query e testarla più volte per vedere come cambia l'output.

![Output di esempio dell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Quando si usano più output in una query, è possibile visualizzare i risultati di entrambi gli output separatamente e passare facilmente da un output all'altro.

Se si è soddisfatti dei risultati mostrati nel browser, è possibile salvare la query, avviare il processo e lasciarlo elaborare gli eventi senza errori.

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
