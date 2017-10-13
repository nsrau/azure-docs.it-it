---
title: Debug guidato dai dati di Analisi di flusso di Azure mediante il diagramma del processo di debug | Documentazione Microsoft
description: Risoluzione dei problemi del processo di Analisi di flusso mediante il diagramma di processo e le metriche.
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/01/2017
ms.author: samacha
ms.openlocfilehash: 5b689c07bf8baa531c7a50ca50ed5140c1787e7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Debug guidato dai dati mediante il diagramma di processo

Il diagramma di processo nel pannello **Monitoraggio** del portale di Azure consente di visualizzare la pipeline di processo. Mostra gli input, gli output e i passaggi delle query. È possibile usare il diagramma di processo per esaminare le metriche per ogni passaggio, al fine di isolare più rapidamente l'origine di un problema durante la sua risoluzione.

## <a name="using-the-job-diagram"></a>Uso del diagramma di processo

Nel portale di Azure, durante un processo di Analisi di flusso, sotto **SUPPORTO E RISOLUZIONE DEI PROBLEMI** selezionare **Diagramma del processo**:

![Diagramma di processo con metriche - posizione](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Selezionare ciascun passaggio della query per vedere la sezione corrispondente nel riquadro di modifica query. Viene visualizzato il grafico della metrica relativo al passaggio in un riquadro inferiore della pagina.

![Diagramma di processo con metriche - processo di base](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Per vedere le partizioni dell'input di Hub eventi di Azure, selezionare **...** Si apre il menu di scelta rapida. È possibile vedere anche la fusione dell'input.

![Diagramma di processo con metriche - espandere la partizione](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Per vedere il grafico delle metrica per una singola partizione, selezionare il nodo della partizione. Le metriche sono visualizzate nella parte inferiore della pagina.

![Diagramma di processo con metriche - altre metriche](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Per vedere il grafico delle metriche per una fusione, selezionare il nodo della fusione. Il diagramma seguente mostra che nessun evento è stato eliminato o regolato.

![Diagramma di processo con metriche - griglia](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Per vedere i dettagli del valore e ora delle metriche, puntare sul grafico.

![Diagramma di processo con metriche - puntare con il mouse](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Risolvere i problemi mediante le metriche

La metrica **QueryLastProcessedTime** indica quando un determinato passaggio ha ricevuto dati. Osservando la topologia, è possibile procedere a ritroso dal processore di output per vedere quale passaggio non riceve dati. Se un passaggio non riceve dati, andare al passaggio di query subito prima. Controllare se il passaggio di query precedente ha un intervallo di tempo e se è trascorso un tempo sufficiente perché possa emettere dati. (Notare che gli intervalli di tempo vengono bloccati all'ora).
 
Se il passaggio di query precedente è un processore di input, usare le metriche di input per rispondere alle seguenti domande mirate. Esse consentono di determinare se un processo sta ricevendo dati dalle sue origini di input. Se viene eseguito il partizionamento della query, esaminare ogni partizione.
 
### <a name="how-much-data-is-being-read"></a>Quanti dati vengono letti?

*   **InputEventsSourcesTotal** è il numero di unità di dati lette. Ad esempio il numero di BLOB.
*   **InputEventsTotal** è il numero di eventi letti. Questa metrica è disponibile per ogni partizione.
*   **InputEventsInBytesTotal** è il numero di byte letti.
*   **InputEventsLastArrivalTime** viene aggiornato con l'ora di accodamento di ogni evento ricevuto.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>L'ora viene spostata in avanti? Se vengono letti eventi reali, potrebbe non essere generata la punteggiatura.

*   **InputEventsLastPunctuationTime** specifica quando è stata generata la punteggiatura per indicare lo spostamento in avanti dell'ora. Se la punteggiatura non viene generata, il flusso di dati può bloccarsi.
 
### <a name="are-there-any-errors-in-the-input"></a>Sono presenti errori nell'input?

*   **InputEventsEventDataNullTotal** è un conteggio degli eventi con dati Null.
*   **InputEventsSerializerErrorsTotal** è un conteggio degli eventi che non è stato possibile deserializzare correttamente.
*   **InputEventsDegradedTotal** è un conteggio degli eventi che sono stati interessati da un problema diverso dalla deserializzazione.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Vengono eliminati o modificati eventi?

*   **InputEventsEarlyTotal** è il numero degli eventi con un timestamp applicazione precedente il limite massimo.
*   **InputEventsLateTotal** è il numero degli eventi con un timestamp applicazione successivo al limite massimo.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** è il numero degli eventi eliminati prima dell'ora di inizio del processo.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Si sta verificando un ritardo nella lettura dei dati?

*   **InputEventsSourcesBackloggedTotal** indica quanti altri messaggi devono essere letti per gli input di Hub eventi e dell'hub IoT di Azure.


## <a name="get-help"></a>Ottenere aiuto
Per ottenere assistenza aggiuntiva, provare ad accedere al [forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi di flusso](stream-analytics-real-time-fraud-detection.md)
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sull'API REST di gestione di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx)
