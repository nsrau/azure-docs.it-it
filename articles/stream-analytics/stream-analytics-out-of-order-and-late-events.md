---
title: Gestione dell'ordine degli eventi e del ritardo con Analisi di flusso di Azure | Documentazione Microsoft
description: Informazioni su come funziona Analisi di flusso con gli eventi non in ordine o in ritardo nei flussi di dati.
keywords: non in ordine, in ritardo, eventi
documentationcenter: 
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
ms.openlocfilehash: d7a5620aef1f28ea065ec10ce095f7f3904a8060
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017


---
# <a name="azure-stream-analytics-event-order-handling"></a>Gestione dell'ordine degli eventi con Analisi di flusso di Azure

In un flusso di eventi di dati temporali ogni evento viene registrato con l'ora in cui viene ricevuto. In presenza di determinate condizioni, i flussi di eventi potrebbero ricevere occasionalmente alcuni eventi in un ordine diverso da quello in cui sono stati inviati. Il problema potrebbe essere causato da una semplice ritrasmissione TCP o anche da uno sfasamento di orario tra il dispositivo di invio e l'hub eventi di ricezione. Inoltre vengono aggiunti eventi di "pausa" ai flussi di eventi ricevuti per far avanzare il tempo in assenza di arrivi di eventi. Questo è necessario negli scenari come "Inviare una notifica quando non si verificano accessi per 3 minuti".

I flussi di input che non sono in ordine vengono:
* Ordinati (e perciò **ritardati**).
* Regolati dal sistema, in base a un criterio specificato dall'utente.


## <a name="lateness-tolerance"></a>Tolleranza ai ritardi
Analisi di flusso tollera questi tipi di scenari. Analisi di flusso è in grado di gestire gli eventi "non in ordine" e "in ritardo". Gestisce questi eventi nei modi seguenti:

* Gli eventi che arrivano non in ordine ma entro la tolleranza stabilita vengono **riordinati per timestamp**.
* Gli eventi che arrivano con un ritardo maggiore della tolleranza vengono **eliminati o regolati**.
    * **Regolati**: regolati in modo che sembrino arrivati nel momento di massimo ritardo accettabile.
    * **Eliminati**: rimossi.

![Gestione degli eventi con Analisi di flusso](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

## <a name="reduce-the-number-of-out-of-order-events"></a>Ridurre il numero degli eventi non in ordine

Poiché Analisi di flusso applica una trasformazione temporale quando elabora gli eventi in ingresso (ad esempio per le aggregazioni finestra o i join temporali), ordina gli eventi in ingresso per timestamp.

Quando la parola chiave "timestamp by" è **not**, viene utilizzata per impostazione predefinita l'ora di accodamento dell'evento in Hub eventi di Azure. Hub eventi garantisce la monotonicità del timestamp in ogni partizione dell'hub eventi. Garantisce anche che gli eventi di tutte le partizioni saranno uniti in ordine di timestamp. Queste due garanzie di Hub eventi assicurano l'assenza di eventi non in ordine.

In alcuni casi è importante usare il timestamp del mittente. In tal caso viene scelto un timestamp dal payload dell'evento usando "timestamp by". Questi scenari potrebbero introdurre una o più cause che determinano eventi non ordinati:

* I producer di eventi presentano sfasamenti di orario. Questo è comune quando i producer provengono da computer diversi che inevitabilmente hanno orologi diversi.
* Si verifica un ritardo di rete dall'origine degli eventi all'hub eventi di destinazione.
* Esistono sfasamenti di orario tra le partizioni dell'hub eventi. Analisi di flusso innanzitutto ordina gli eventi di tutte le partizioni dell'hub eventi in base all'ora di accodamento dell'evento. Quindi esamina il flusso di dati per gli eventi non in ordine.

Nella scheda di configurazione si noteranno i seguenti valori predefiniti:

![Gestione degli eventi non in ordine di Analisi di flusso](media/stream-analytics-event-handling/stream-analytics-out-of-order-handling.png)

Se si imposta il valore 0 secondi come finestra di tolleranza per gli eventi non in ordine, è come stabilire che tutti gli eventi sono sempre in ordine. Considerate le tre cause che determinano eventi non ordinati, è improbabile che questo si verifichi. 

Per consentire ad Analisi di flusso di correggere un evento non in ordine è possibile specificare una finestra di tolleranza per gli eventi non in ordine diversa da zero. Analisi di flusso memorizza gli eventi nel buffer fino a tale finestra e quindi li riordina usando il timestamp scelto. Applica quindi la trasformazione temporale. È possibile iniziare con una finestra di 3 secondi e ottimizzare il valore per ridurre il numero di eventi di cui viene regolata l'ora. 

Un effetto collaterale della memorizzazione nel buffer è che l'output viene **ritardato della stessa quantità di tempo**. È possibile ottimizzare il valore per ridurre il numero di eventi non in ordine e mantenere bassa la latenza del processo.

## <a name="get-help"></a>Ottenere aiuto
Per ottenere assistenza aggiuntiva, provare ad accedere al [forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi di flusso](stream-analytics-real-time-fraud-detection.md)
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sull'API REST di gestione di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx)
