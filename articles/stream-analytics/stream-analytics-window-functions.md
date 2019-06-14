---
title: Introduzione alle funzioni delle finestre di Analisi di flusso di Azure
description: Questo articolo descrive le quattro funzioni delle finestre (a cascata, salto, temporale scorrevole, sessione) usate nei processi di Analisi di flusso di Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 56b6f11d226f25e3094a90d8646fa13860ee306e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066753"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introduzione alle funzioni delle finestre di Analisi di flusso

Negli scenari di flusso tempo eseguire operazioni solo sui dati contenuti in finestre temporali è un modello comune. Analisi di flusso offre supporto nativo per le funzioni delle finestre, consentendo agli sviluppatori di creare processi di elaborazione dei flussi complessi con il minimo sforzo.

Esistono quattro tipi di finestre temporali tra cui scegliere: [**a cascata**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [**di salto**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [**scorrevoli**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) e [**della sessione**](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics).  Usare le funzioni delle finestre nella clausola [ **GROUP BY** ](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) della sintassi di query nei processi di Flusso Analitica. È anche possibile aggregare eventi su più finestre utilizzando il [ **Windows()** funzione](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Tutte le operazioni delle [finestre](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) restituiscono i risultati alla **fine** della finestra. L'output della finestra sarà un singolo evento basato sulla funzione di aggregazione usata. All'evento di output sarà associato il timestamp di fine della finestra e tutte le funzioni finestra sono definite con una lunghezza fissa. 

![Concetti delle funzioni finestra di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Finestra a cascata
Le funzioni finestra a cascata vengono usate per segmentare un flusso di dati in segmenti temporali distinti e per eseguire una funzione su tali segmenti, come nell'esempio seguente. I principali elementi distintivi di una finestra a cascata sono la ripetitività e la non sovrapposizione, oltre al fatto che un evento non può appartenere a più di una finestra a cascata.

![Finestra a cascata di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Finestra di salto
Le funzioni finestra di salto consentono di avanzare nel tempo di un periodo fisso. Può essere utile pensare a queste finestre come finestre a cascata che possono essere sovrapposte, quindi gli eventi possono appartenere a più di un set di risultati della finestra di salto. Per creare una finestra di salto uguale a una finestra a cascata, specificare dimensioni del salto uguali alle dimensioni della finestra. 

![Finestra di salto di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Finestra temporale scorrevole
Le funzioni finestra temporale scorrevole, diversamente dalle finestre a cascata o di salto, generano un output **solo** quando si verifica un evento. Ogni finestra avrà almeno un evento e la finestra si sposta continuamente in avanti di un € (epsilon). Come le finestre di salto, gli eventi possono appartenere a più di una finestra temporale scorrevole.

![Finestra temporale di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Finestra della sessione
Le funzioni finestra della sessione raggruppano gli eventi che arrivano a intervalli di tempo simili, filtrando i periodi di tempo nei quali non sono presenti dati. Dispone di tre parametri principali: timeout, durata massima e chiave di partizionamento (facoltativo).

![Finestra della sessione di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Una finestra della sessione inizia quando si verifica il primo evento. Se si verifica un altro evento entro il timeout specificato dall'ultimo evento acquisito, la finestra si estende per includere il nuovo evento. In caso contrario, se non si verificano eventi entro il timeout, la finestra viene chiusa al timeout.

Se continuano a verificarsi eventi entro il timeout specificato, la finestra della sessione continuerà a estendersi fino al raggiungimento della durata massima. Gli intervalli di controllo della durata massima sono impostati in modo da avere lo stesso valore della durata massima specificata. Ad esempio, se la durata massima è 10, i controlli sulla finestra per verificare il superamento della durata massima verranno eseguiti a intervalli di t = 0, 10, 20, 30 e così via.

Quando viene fornita una chiave di partizione, gli eventi vengono raggruppati tramite la chiave e la finestra della sessione viene applicata indipendentemente a ogni gruppo. Questo partizionamento è utile nei casi in cui sono necessarie finestre sessione diverse per utenti o dispositivi diversi.


## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

