---
title: Testare una query usando dati di esempio in Analisi di flusso di Azure
description: Questo articolo descrive come testare una query usando alcuni dati di input di esempio in Analisi di flusso di Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 305b767ee86de6c8b04fed17514a9092afc2d735
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2018
ms.locfileid: "30961690"
---
# <a name="test-a-query-and-sample-input-in-azure-stream-analytics"></a>Testare una query e input di esempio in Analisi di flusso di Azure 

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

Quando si è soddisfatti dei risultati, è possibile salvare la query, avviare il processo e attendere l'elaborazione da parte di Analisi di flusso di Azure.

## <a name="get-help"></a>Ottenere aiuto

Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
