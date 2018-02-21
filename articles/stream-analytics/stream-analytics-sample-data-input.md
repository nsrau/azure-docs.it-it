---
title: Test delle query in Analisi di flusso di Azure | Microsoft Docs
description: Individuare i problemi durante la risoluzione dei processi di analisi di flusso.
keywords: risoluzione dei problemi relativi all'input, campionamento dell'input
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: e2636b8b89b86bbb2a2991972386462535d5a10f
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="azure-stream-analytics-query-testing-and-input-stream-sampling"></a>Test delle query e campionamento del flusso di input di Analisi di flusso di Azure

La funzione di analisi di flusso di Azure consente di campionare eventi di input che provengono da un file e di testare le query nel portale senza la necessità di avviare o arrestare un processo.

## <a name="testing-your-query"></a>Test di una query

Nel riquadro dei dettagli del processo di analisi di flusso aprire il pannello **Editor query** facendo clic sul nome della query in **Query**. Nello scenario di esempio, poiché non è ancora stata creata alcuna query, fare clic sul segnaposto **< >**.

![Editor query della funzione di analisi di flusso](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

Il pannello dell'editor completo per la creazione di query presenta lo stesso aspetto della versione precedente. Il pannello è stato ora aggiornato di un nuovo riquadro a sinistra, che mostra gli input e gli output usati dalla query e definiti per il processo.

![Elenchi di input e di output nell'editor query di analisi di flusso](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

Sono inoltre visibili altri input e output, che non sono definiti. Questi provengono dal nuovo modello di query iniziale e cambiano o addirittura scompaiono completamente man mano che si modifica la query. È possibile ignorarli senza problemi per il momento.

Per eseguire il test con dati di input di esempio, fare clic con il pulsante destro del mouse su uno degli input e quindi selezionare **Carica dati di esempio da file**.

![Comando Carica dati di esempio da file dell'editor query della funzione di analisi di flusso](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

Dopo avere completato il caricamento, fare clic su **Test** per testare la query usando i dati di esempio appena specificati.

![Pulsante Test dell'editor query della funzione di analisi di flusso](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Per consentire il salvataggio dei risultati del test per un utilizzo successivo, l'output della query viene visualizzato nel browser con un collegamento ai risultati del download. È possibile a questo punto modificare in modo facile e iterativo la query e testarla più volte per vedere come cambia l'output.

![Output di esempio dell'editor query della funzione di analisi di flusso](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

Nell'immagine precedente è stato aggiunto un secondo output, denominato **HighAvgTempOutput**.

Quando si usano più output in una query, è possibile visualizzare i risultati di ogni output separatamente e passare facilmente da un output all'altro.

Quando si è soddisfatti dei risultati, è possibile salvare la query, avviare il processo e attendere l'elaborazione da parte della funzione di analisi di flusso.

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
