---
title: Introduzione alle funzioni delle finestre di Analisi di flusso di Azure
description: Questo articolo descrive le quattro funzioni delle finestre (a cascata, salto, temporale scorrevole, sessione) usate nei processi di Analisi di flusso di Azure.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: 4c8d2143d2b6e18de2669a6b45961e601cc394bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707558"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Introduzione alle funzioni delle finestre di Analisi di flusso

Negli scenari di flusso tempo eseguire operazioni solo sui dati contenuti in finestre temporali è un modello comune. Analisi di flusso offre supporto nativo per le funzioni delle finestre, consentendo agli sviluppatori di creare processi di elaborazione dei flussi complessi con il minimo sforzo.

Sono disponibili cinque tipi di finestre temporali tra cui scegliere: a [**cascata**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), ad [**salto**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), a [**scorrimento**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics), a [**sessione**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics)e a [**snapshot**](https://docs.microsoft.com/stream-analytics-query/snapshot-window-azure-stream-analytics) .  Usare le funzioni delle finestre nella clausola [**GROUP BY**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) della sintassi di query nei processi di Flusso Analitica. È anche possibile aggregare eventi su più finestre usando la [funzione **Windows ()** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Tutte le operazioni delle [finestre](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) restituiscono i risultati alla **fine** della finestra. Si noti che quando si avvia un processo di analisi di flusso, è possibile specificare l' *ora di inizio dell'output del processo* e il sistema recupererà automaticamente gli eventi precedenti nei flussi in ingresso per restituire la prima finestra all'ora specificata. ad esempio, quando si inizia con l'opzione *Now* , inizierà a emettere immediatamente i dati. L'output della finestra sarà un singolo evento basato sulla funzione di aggregazione usata. All'evento di output sarà associato il timestamp di fine della finestra e tutte le funzioni finestra sono definite con una lunghezza fissa. 

![Concetti delle funzioni finestra di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Finestra a cascata
Le funzioni finestra a cascata vengono usate per segmentare un flusso di dati in segmenti temporali distinti e per eseguire una funzione su tali segmenti, come nell'esempio seguente. I principali elementi distintivi di una finestra a cascata sono la ripetitività e la non sovrapposizione, oltre al fatto che un evento non può appartenere a più di una finestra a cascata.

![Finestra a cascata di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Finestra di salto
Le funzioni finestra di salto consentono di avanzare nel tempo di un periodo fisso. Può essere facile da considerare come finestre a cascata che possono sovrapporsi e essere emesse più spesso rispetto alla dimensione della finestra. Gli eventi possono appartenere a più di un set di risultati della finestra di salto. Per creare una finestra di salto uguale a una finestra a cascata, specificare dimensioni del salto uguali alle dimensioni della finestra. 

![Finestra di salto di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Finestra temporale scorrevole

Le finestre scorrevoli, diversamente dalle finestre a cascata o di salto, restituiscono eventi solo per i punti nel tempo in cui il contenuto della finestra viene effettivamente modificato. In altre parole, quando un evento entra o esce dalla finestra. Ogni finestra ha almeno un evento, come nel caso delle finestre di salto, gli eventi possono appartenere a più di una finestra temporale scorrevole

![Finestra temporale di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Finestra della sessione
Le funzioni finestra della sessione raggruppano gli eventi che arrivano a intervalli di tempo simili, filtrando i periodi di tempo nei quali non sono presenti dati. Dispone di tre parametri principali: timeout, durata massima e chiave di partizionamento (facoltativo).

![Finestra della sessione di Analisi di flusso](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Una finestra della sessione inizia quando si verifica il primo evento. Se si verifica un altro evento entro il timeout specificato dall'ultimo evento acquisito, la finestra si estende per includere il nuovo evento. In caso contrario, se non si verificano eventi entro il timeout, la finestra viene chiusa al timeout.

Se continuano a verificarsi eventi entro il timeout specificato, la finestra della sessione continuerà a estendersi fino al raggiungimento della durata massima. Gli intervalli di controllo della durata massima sono impostati in modo da avere lo stesso valore della durata massima specificata. Ad esempio, se la durata massima è 10, i controlli sulla finestra per verificare il superamento della durata massima verranno eseguiti a intervalli di t = 0, 10, 20, 30 e così via.

Quando viene fornita una chiave di partizione, gli eventi vengono raggruppati tramite la chiave e la finestra della sessione viene applicata indipendentemente a ogni gruppo. Questo partizionamento è utile nei casi in cui sono necessarie finestre sessione diverse per utenti o dispositivi diversi.

## <a name="snapshot-window"></a>Finestra snapshot

Snapshot gruppi di Windows eventi con lo stesso timestamp. A differenza di altri tipi di finestra, che richiedono una funzione finestra specifica (ad esempio [SessionWindow ()](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics), è possibile applicare una finestra snapshot aggiungendo System. timestamp () alla clausola Group by.

![Finestra snapshot di analisi di flusso](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

