---
title: Test delle query nella funzione analisi di flusso di Azure | Microsoft Docs
description: Informazioni su come testare le query nei processi di analisi di flusso.
keywords: testare query, risolvere i problemi relativi alle query
documentation center: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 9ca96196d2d4b898c79556867b5ff8d8be0f66dc
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017


---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Testare le query della funzione di analisi di flusso nel portale di Azure

La funzione analisi di flusso di Azure consente di testare le query nel portale di Azure senza la necessità di avviare o arrestare un processo.

## <a name="test-the-input"></a>Testare l'input

1. Per eseguire il test con dati di input di esempio, fare clic con il pulsante destro del mouse su uno degli input e quindi selezionare **Carica dati di esempio da file**.

    ![Pulsante per il testo delle query nell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. Dopo avere completato il caricamento, fare clic su **Test** per testare la query usando i dati di esempio appena specificati.

    ![dati di esempio per il test nell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

L'output della query viene visualizzato nel browser con il collegamento dei risultati del download, qualora si volesse salvare i risultati del test per usarli in un secondo momento. È possibile a questo punto modificare in modo facile e iterativo la query e testarla più volte per vedere come cambia l'output.

![Output di esempio dell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Quando si usano più output in una query, è possibile visualizzare i risultati di entrambi gli output separatamente e passare facilmente da un output all'altro.

Se si è soddisfatti dei risultati mostrati nel browser, è possibile salvare la query, avviare il processo e lasciarlo elaborare gli eventi senza errori.

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

