---
title: Introduzione alle funzioni finestra di Analisi di flusso | Microsoft Docs
description: Informazioni sulle tre funzioni finestra in Analisi di flusso (finestra a cascata, finestra di salto, finestra temporale scorrevole).
keywords: finestra a cascata, finestra temporale scorrevole, finestra di salto
documentationcenter: ''
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok

---
# <a name="introduction-to-stream-analytics-window-functions"></a>Introduzione alle funzioni finestra di Analisi di flusso
In molti scenari di flusso in tempo reale è necessario eseguire operazioni solo sui dati contenuti in finestre temporali. Il supporto nativo delle funzioni finestra è una funzionalità cruciale di Analisi di flusso di Azure che pone l'accento sulla produttività degli sviluppatori per la creazione di processi di elaborazione di flussi complessi. Analisi di flusso consente agli sviluppatori di usare finestre [**a cascata**](https://msdn.microsoft.com/library/dn835055.aspx), [**di salto**](https://msdn.microsoft.com/library/dn835041.aspx) e [**temporali scorrevoli**](https://msdn.microsoft.com/library/dn835051.aspx) per eseguire operazioni temporali sui dati di flusso. Vale la pena di sottolineare che tutte le operazioni [finestra](https://msdn.microsoft.com/library/dn835019.aspx) restituiscono i risultati alla **fine** della finestra. L'output della finestra sarà un singolo evento basato sulla funzione di aggregazione usata. All'evento sarà associato il timestamp di fine della finestra e tutte le funzioni finestra sono definite con una lunghezza fissa. Infine, è importante segnalare che tutte le funzioni finestra devono essere usate in una clausola [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx).

![Concetti delle funzioni finestra di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Finestra a cascata
Le funzioni finestra a cascata vengono usate per segmentare un flusso di dati in segmenti temporali distinti e per eseguire una funzione su tali segmenti, come nell'esempio seguente. I principali elementi distintivi di una finestra a cascata sono la ripetitività e la non sovrapposizione, oltre al fatto che un evento non può appartenere a più di una finestra a cascata.

![Introduzione alle funzioni finestra a cascata di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Finestra di salto
Le funzioni finestra di salto consentono di avanzare nel tempo di un periodo fisso. Può essere utile pensare a queste finestre come finestre a cascata che possono essere sovrapposte, quindi gli eventi possono appartenere a più di un set di risultati della finestra di salto. Per creare una finestra di salto uguale a una finestra a cascata, basterebbe specificare dimensioni del salto uguali alle dimensioni della finestra. 

![Introduzione alle funzioni finestra di salto di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Finestra temporale scorrevole
Le funzioni finestra temporale scorrevole, diversamente dalle finestre a cascata o di salto, generano un output **solo** quando si verifica un evento. Ogni finestra avrà almeno un evento e la finestra si sposta continuamente in avanti di un € (epsilon). Come le finestre di salto, gli eventi possono appartenere a più di una finestra temporale scorrevole.

![Introduzione alle funzioni finestra temporale scorrevole di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Risorse della Guida per le funzioni finestra
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=Oct16_HO2-->


