---
title: Gestione dell'ordine degli eventi e del ritardo con Analisi di flusso di Azure | Documentazione Microsoft
description: Informazioni su come funziona Analisi di flusso con gli eventi non in ordine o in ritardo nei flussi di dati.
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
ms.openlocfilehash: cf57bd12a62b3de8ac49b26ce7cdc40aec0b6738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stream-analytics-event-order-handling"></a>Gestione dell'ordine degli eventi con Analisi di flusso di Azure

In un flusso di dati temporale degli eventi, a ogni evento viene assegnato un timestamp. Analisi di flusso di Azure assegna timestamp a ogni evento usando l'ora di arrivo o il tempo applicazione. La colonna System.Timestamp include il timestamp assegnato all'evento. L'ora di arrivo viene assegnata nell'origine di input quando l'evento raggiunge l'origine. L'ora di arrivo corrisponde a EventEnqueuedTime per l'input di Hub eventi e all'[ora dell'ultima modifica del BLOB](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) per l'input del BLOB. Il tempo applicazione viene assegnato quando l'evento viene generato e fa parte del payload. Per elaborare gli eventi in base al tempo applicazione, usare la clausola "Timestamp by" nella query di selezione. Se la clausola "Timestamp by" è assente, gli eventi vengono elaborati in base all'ora di arrivo. È possibile accedere all'ora di arrivo usando la proprietà EventEnqueuedTime per l'hub eventi e la proprietà BlobLastModified per l'input del BLOB. Analisi di flusso di Azure genera l'output in base all'ordine del timestamp e fornisce alcune impostazioni per gestire i dati non in ordine.

![Gestione degli eventi con Analisi di flusso](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

I flussi di input che non sono in ordine vengono:
* Ordinati (e perciò **ritardati**).
* Regolati dal sistema, in base al criterio specificato dall'utente.

Analisi di flusso di Azure tollera eventi in ritardo e non in ordine durante l'elaborazione in base al tempo applicazione.

**Tolleranza per arrivo in ritardo**

* Questa impostazione è applicabile solo quando l'elaborazione si basa sul tempo applicazione. In caso contrario, viene ignorata.
* Si tratta della differenza massima tra l'ora di arrivo e il tempo applicazione. Se il tempo applicazione è precedente a (Ora di arrivo - Finestra di arrivo in ritardo), viene impostato su (Ora di arrivo - Finestra di arrivo in ritardo)
* Quando più partizioni dello stesso flusso di input o più flussi di input vengono combinati insieme, la tolleranza di arrivo in ritardo è la quantità massima di tempo di attesa dei nuovi dati di ogni partizione. 

In breve, la finestra di arrivo in ritardo è il ritardo massimo tra la generazione dell'evento e la ricezione dell'evento nell'origine di input.
La modifica in base alla tolleranza per arrivo in ritardo viene eseguita per prima, seguita da quella degli elementi non in ordine. La colonna **System.Timestamp** include il timestamp finale assegnato all'evento.

**Tolleranza per elementi non in ordine**

* Gli eventi che non arrivano in ordine ma che rientrano nella finestra di tolleranza degli elementi non in ordine stabilita vengono **riordinati in base al timestamp**. 
* Gli eventi che arrivano con un ritardo maggiore della tolleranza vengono **eliminati o modificati**.
    * **Regolati**: regolati in modo che sembrino arrivati nel momento di massimo ritardo accettabile. 
    * **Eliminati**: rimossi.

Per riordinare gli eventi ricevuti entro la "finestra di tolleranza per elementi non in ordine", l'output della query viene **ritardato in base alla finestra di tolleranza per elementi non in ordine**.

**Esempio**

Tolleranza per arrivo in ritardo = 10 minuti<br/>
Tolleranza per elementi non in ordine = 3 minuti<br/>
Elaborazione in base al tempo applicazione<br/>

Eventi:

Evento 1 _Tempo applicazione_ = 00:00:00, _Ora di arrivo_ = 00:10:01, _System.Timestamp_ = 00:00:01, modificato perché (_Ora di arrivo_ - _Tempo applicazione_) è maggiore della tolleranza per arrivo in ritardo.

Evento 2 _Tempo applicazione_ = 00:00:01, _Ora di arrivo_ = 00:10:01, _System.Timestamp_ = 00:00:01, non modificato perché il tempo applicazione rientra nella finestra di arrivo in ritardo.

Evento 3 _Tempo applicazione_ = 00:10:00, _Ora di arrivo_ = 00:10:02, _System.Timestamp_ = 00:10:00, non modificato perché il tempo applicazione rientra nella finestra di arrivo in ritardo.

Evento 4 _Tempo applicazione_ = 00:09:00, _Ora di arrivo_ = 00:10:03, _System.Timestamp_ = 00:09:00, accettato con il timestamp originale perché il tempo applicazione rientra nella tolleranza per elementi non ordine.

Evento 5 _Tempo applicazione_ = 00:06:00, _Ora di arrivo_ = 00:10:04, _System.Timestamp_ = 00:07:00, modificato perché il tempo applicazione è precedente alla tolleranza per elementi non ordine.



## <a name="get-help"></a>Ottenere aiuto
Per ottenere assistenza aggiuntiva, provare ad accedere al [forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi di flusso](stream-analytics-real-time-fraud-detection.md)
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sull'API REST di gestione di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx)
