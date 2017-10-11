---
title: Gestione dell'ordine degli eventi e del ritardo con Analisi di flusso di Azure | Documentazione Microsoft
description: Informazioni su come funziona Analisi di flusso di Azure con gli eventi non in ordine o in ritardo nei flussi di dati
keywords: non in ordine, in ritardo, eventi
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
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: cf9a43fbb82a32c92d66f25809916d3ccde1a20d
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="azure-stream-analytics-event-order-handling"></a>Gestione dell'ordine degli eventi con Analisi di flusso di Azure

In un flusso di dati temporale degli eventi, a ogni evento viene assegnato un timestamp. In Analisi di flusso di Azure, i timestamp vengono assegnati a ogni evento in base all'ora di arrivo o al tempo applicazione. 

La colonna **System.Timestamp** presenta il timestamp assegnato all'evento. L'ora di arrivo viene assegnata all'origine di input quando l'evento la raggiunge. L'ora di arrivo corrisponde a **EventEnqueuedTime** per l'input dell'hub eventi e all'[ora dell'ultima modifica del blob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) per l'input del blob. L'ora di applicazione viene assegnata quando viene generato l'evento e fa parte del payload. 

Per elaborare gli eventi in base al tempo applicazione, usare la clausola TIMESTAMP BY nella query di selezione. Se la clausola TIMESTAMP BY è assente, gli eventi vengono elaborati in base all'ora di arrivo. È possibile accedere all'ora di arrivo usando la proprietà **EventEnqueuedTime** per l'hub eventi e la proprietà **BlobLastModified** per l'input del blob. 

Analisi di flusso di Azure genera l'output in base all'ordine del timestamp e fornisce alcune impostazioni per operare con gli elementi non in ordine.

![Gestione degli eventi con Analisi di flusso](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

I flussi di input che non sono in ordine vengono:
* Ordinati (e perciò *ritardati*).
* Regolati dal sistema, in base al criterio specificato dall'utente.

Analisi di flusso di Azure tollera eventi in ritardo e non in ordine durante l'elaborazione in base al tempo applicazione.

**Tolleranza per arrivo in ritardo**

* L'impostazione della tolleranza per arrivo in ritardo è applicabile solo quando l'elaborazione si basa sul tempo applicazione. In caso contrario, viene ignorata.
* La tolleranza di arrivo in ritardo è la differenza massima tra l'ora di arrivo e il tempo applicazione. Se il tempo applicazione è precedente a *(Ora di arrivo - Finestra di arrivo in ritardo)*, viene impostato su *(Ora di arrivo - Finestra di arrivo in ritardo)*.
* Quando più partizioni dello stesso flusso di input o più flussi di input vengono combinati insieme, la tolleranza di arrivo in ritardo è la quantità massima di tempo di attesa dei nuovi dati di ogni partizione. 

La finestra di arrivo in ritardo è il ritardo massimo tra la generazione dell'evento e la ricezione dello stesso nell'origine di input.
La regolazione in base alla tolleranza di arrivo in ritardo viene eseguita per prima e quella degli elementi non in ordine viene eseguita per seconda. La colonna **System.Timestamp** contiene il timestamp finale assegnato all'evento.

**Tolleranza per elementi non in ordine**

* Gli eventi che non arrivano in ordine ma che rientrano nella finestra di tolleranza stabilita vengono *riordinati in base al timestamp*. 
* Gli eventi che arrivano con un ritardo maggiore della tolleranza vengono *eliminati* o *regolati*.
    * **Regolati**: regolati in modo che sembrino arrivati nel momento di massimo ritardo accettabile. 
    * **Eliminati**: rimossi.

Per riordinare gli eventi ricevuti entro la "finestra di tolleranza degli elementi non in ordine", l'output della query è *ritardato in base alla finestra di tolleranza per elementi non in ordine*.

**Esempio**

Tolleranza per arrivo in ritardo = 10 minuti<br/>
Tolleranza per elementi non in ordine = 3 minuti<br/>
Elaborazione in base al tempo applicazione<br/>

Eventi:

Evento 1 _Tempo applicazione_ = 00:00:00, _Ora di arrivo_ = 00:10:01, _System.Timestamp_ = 00:00:01, regolato perché (_Ora di arrivo_ - _Tempo applicazione_) è maggiore rispetto alla tolleranza per arrivo in ritardo.

Evento 2 _Tempo applicazione_ = 00:00:01, _Ora di arrivo_ = 00:10:01, _System.Timestamp_ = 00:00:01, non regolato perché il tempo applicazione rientra nella finestra di arrivo in ritardo.

Evento 3 _Tempo applicazione_ = 00:10:00, _Ora di arrivo_ = 00:10:02, _System.Timestamp_ = 00:10:00, non regolato perché il tempo applicazione rientra nella finestra di arrivo in ritardo.

Evento 4 _Tempo applicazione_ = 00:09:00, _Ora di arrivo_ = 00:10:03, _System.Timestamp_ = 00:09:00, accettato con il timestamp originale perché il tempo applicazione rientra nella tolleranza per elementi non ordine.

Evento 5 _Tempo applicazione_ = 00:06:00, _Ora di arrivo_ = 00:10:04, _System.Timestamp_ = 00:07:00, regolato perché il tempo applicazione è precedente alla tolleranza per elementi non ordine.



## <a name="get-help"></a>Ottenere aiuto
Per ottenere assistenza aggiuntiva, provare ad accedere al [forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

