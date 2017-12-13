---
title: Gestione dell'ordine degli eventi e del ritardo con Analisi di flusso di Azure | Documentazione Microsoft
description: Informazioni su come funziona Analisi di flusso con gli eventi non in ordine o in ritardo nei flussi di dati.
keywords: non in ordine, in ritardo, eventi
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
ms.openlocfilehash: 208dfa14d5d18e106d654539cd80bafdeb90cdf8
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2017
---
# <a name="azure-stream-analytics-event-order-consideration"></a>Considerazioni sull'ordine degli eventi con Analisi di flusso di Azure

## <a name="understand-arrival-time-and-application-time"></a>Comprendere l'ora di arrivo e il tempo applicazione

In un flusso di dati temporale degli eventi, a ogni evento viene assegnato un timestamp. Analisi di flusso di Azure assegna timestamp a ogni evento usando l'ora di arrivo o il tempo applicazione. La colonna System.Timestamp include il timestamp assegnato all'evento. L'ora di arrivo viene assegnata nell'origine di input quando l'evento raggiunge l'origine. L'ora di arrivo corrisponde a EventEnqueuedTime per l'input di Hub eventi e all'[ora dell'ultima modifica del BLOB](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) per l'input del BLOB. Il tempo applicazione viene assegnato quando l'evento viene generato e fa parte del payload. Per elaborare gli eventi in base al tempo applicazione, usare la clausola "Timestamp by" nella query di selezione. Se la clausola "Timestamp by" è assente, gli eventi vengono elaborati in base all'ora di arrivo. È possibile accedere all'ora di arrivo usando la proprietà EventEnqueuedTime per l'hub eventi e la proprietà BlobLastModified per l'input del BLOB. Analisi di flusso di Azure genera l'output in base all'ordine del timestamp e fornisce alcune impostazioni per gestire i dati non in ordine.


## <a name="azure-stream-analytics-handling-of-multiple-streams"></a>Gestione di più flussi con Analisi di flusso di Azure

Il processo di Analisi di flusso di Azure combina gli eventi di più sequenze temporali in alcuni casi, inclusi i seguenti:

* Generazione di output da più partizioni. Le query in cui non è presente una clausola esplicita "Partition by PartitionId" dovranno combinare gli eventi da tutte le partizioni.
* Unione di due o più origini di input differenti.
* Join delle origini di input.

Negli scenari in cui vengono combinate più sequenze temporali Analisi di flusso di Azure genererà un output per un timestamp *t1* solo dopo che tutte le origini combinate sono almeno all'ora *t1*.
Ad esempio, se la query legge da una partizione *Hub eventi* che dispone di due partizioni e una delle partizioni *P1* contiene eventi fino all'ora *t1* e l'altra partizione *P2* contiene eventi fino all'ora *t1 + x*, verrà generato un output fino all'ora *t1*.
Tuttavia, se è presente una clausola esplicita *"Partition by PartitionId"*, entrambe le partizioni avanzano in modo indipendente.
L'impostazione Tolleranza per arrivo in ritardo viene usata per gestire l'assenza di dati in alcune partizioni.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Configurazione di Tolleranza per arrivo in ritardo e Tolleranza elementi non in ordine
I flussi di input che non sono in ordine vengono:
* Ordinati (e perciò **ritardati**).
* Regolati dal sistema, in base al criterio specificato dall'utente.

Analisi di flusso di Azure tollera eventi in ritardo e non in ordine durante l'elaborazione in base al **tempo applicazione**. Le impostazioni seguenti sono disponibili nell'opzione **Ordinamento eventi** nel portale di Azure: 

![Gestione degli eventi con Analisi di flusso](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

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

* Tolleranza per arrivo in ritardo = 10 minuti<br/>
* Tolleranza per elementi non in ordine = 3 minuti<br/>
* Elaborazione in base al tempo applicazione<br/>
* Eventi:
   * Evento 1 _Tempo applicazione_ = 00:00:00, _Ora di arrivo_ = 00:10:01, _System.Timestamp_ = 00:00:01, modificato perché (_Ora di arrivo_ - _Tempo applicazione_) è maggiore della tolleranza per arrivo in ritardo.
   * Evento 2 _Tempo applicazione_ = 00:00:01, _Ora di arrivo_ = 00:10:01, _System.Timestamp_ = 00:00:01, non modificato perché il tempo applicazione rientra nella finestra di arrivo in ritardo.
   * Evento 3 _Tempo applicazione_ = 00:10:00, _Ora di arrivo_ = 00:10:02, _System.Timestamp_ = 00:10:00, non modificato perché il tempo applicazione rientra nella finestra di arrivo in ritardo.
   * Evento 4 _Tempo applicazione_ = 00:09:00, _Ora di arrivo_ = 00:10:03, _System.Timestamp_ = 00:09:00, accettato con il timestamp originale perché il tempo applicazione rientra nella tolleranza per elementi non ordine.
   * Evento 5 _Tempo applicazione_ = 00:06:00, _Ora di arrivo_ = 00:10:04, _System.Timestamp_ = 00:07:00, modificato perché il tempo applicazione è precedente alla tolleranza per elementi non ordine.

## <a name="practical-considerations"></a>Considerazioni pratiche
Come già detto, la *tolleranza per arrivo in ritardo* è la differenza massima tra il tempo applicazione e l'ora di arrivo.
Anche durante l'elaborazione in base al tempo applicazione, gli eventi successivi rispetto alla *tolleranza per arrivo in ritardo* configurata vengono modificati prima che sia applicata l'impostazione *tolleranza elementi non in ordine*. Pertanto, gli elementi non in ordine effettivi sono la differenza minima tra la tolleranza per arrivo in ritardo e la tolleranza elementi non in ordine.

Gli eventi non in ordine all'interno di un flusso si verificano a causa di specifici motivi, tra cui:
* Sfasamento di orario tra i mittenti.
* Latenza variabile tra il mittente e l'origine evento di input.

L'arrivo in ritardo si verifica a causa di specifici motivi, tra cui:
* I mittenti elaborano gli eventi in batch e li inviano per un intervallo successivo, dopo l'intervallo.
* La latenza tra l'invio dell'evento da parte del mittente e la sua ricezione in corrispondenza dell'origine di input.

Quando si configurano la *tolleranza per arrivo in ritardo* e la *tolleranza elementi non in ordine* per un processo specifico, dovrebbero essere tenuti in considerazione la correttezza, i requisiti di latenza e i fattori elencati in precedenza.

Di seguito sono riportati alcuni esempi.

### <a name="example-1"></a>Esempio 1: 
La query include la clausola "Partition by PartitionId" e all'interno di una singola partizione gli eventi vengono inviati tramite metodi di invio sincrono. I metodi di invio sincrono si bloccano finché non vengono inviati gli eventi.
In questo caso, gli elementi non in ordine sono pari a zero poiché gli eventi vengono inviati in ordine con la conferma esplicita prima di inviare l'evento successivo. L'arrivo in ritardo è il ritardo massimo tra la generazione dell'evento e l'invio dell'evento più la latenza massima tra il mittente e l'origine di input.

### <a name="example-2"></a>Esempio 2
La query include la clausola "Partition by PartitionId" e all'interno di una singola partizione gli eventi vengono inviati tramite metodi di invio asincrono. I metodi di invio asincrono possono avviare più invii contemporaneamente, che possono causare eventi non in ordine.
In questo caso, sia gli elementi non in ordine che l'arrivo in ritardo sono almeno il ritardo massimo tra la generazione dell'evento e l'invio dell'evento più la latenza massima tra il mittente e l'origine di input.

### <a name="example-3"></a>Esempio 3:
La query non include la clausola "Partition by PartitionId" e sono presenti almeno due partizioni.
La configurazione è la stessa dell'esempio 2. Tuttavia, l'assenza di dati in una delle partizioni può ritardare l'output di un'ulteriore finestra di "tolleranza per arrivo in ritardo".

## <a name="to-summarize"></a>Per riepilogare
* La finestra di tolleranza per arrivo in ritardo e per elementi non in ordine dovrebbe essere configurata in base alla correttezza e ai requisiti di latenza e dovrebbe anche tenere in considerazione la modalità di invio degli eventi.
* È consigliabile che la tolleranza elementi non in ordine sia inferiore alla tolleranza per arrivo in ritardo.
* Quando si combinano più sequenze temporali, la mancanza di dati in una delle origini o delle partizioni può ritardare l'output di un'ulteriore finestra di tolleranza per arrivo in ritardo.

## <a name="get-help"></a>Ottenere aiuto
Per ottenere assistenza aggiuntiva, provare ad accedere al [forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi di flusso](stream-analytics-real-time-fraud-detection.md)
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sull'API REST di gestione di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx)
