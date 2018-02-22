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
ms.openlocfilehash: 71929b449f2a0fa55327fd3f9741208506859e85
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Considerazioni sull'ordine degli eventi con Analisi di flusso di Azure

## <a name="arrival-time-and-application-time"></a>Ora di arrivo e tempo applicazione

In un flusso di dati temporale degli eventi, a ogni evento viene assegnato un timestamp. Analisi di flusso di Azure assegna timestamp a ogni evento usando l'ora di arrivo o il tempo applicazione. La colonna **System.Timestamp** include il timestamp assegnato all'evento. 

L'ora di arrivo viene assegnata nell'origine di input quando l'evento raggiunge l'origine. È possibile accedere all'ora di arrivo usando la proprietà **EventEnqueuedTime** per l'input dell'hub eventi e la proprietà [BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) per l'input del BLOB. 

Il tempo applicazione viene assegnato quando l'evento viene generato e fa parte del payload. Per elaborare gli eventi in base al tempo applicazione, usare la clausola **Timestamp by** nella query di selezione. Se la clausola **Timestamp by** è assente, gli eventi vengono elaborati in base all'ora di arrivo. 

Analisi di flusso di Azure genera l'output in base all'ordine del timestamp e fornisce impostazioni per gestire i dati non in ordine.


## <a name="handling-of-multiple-streams"></a>Gestione di più flussi

Un processo di Analisi di flusso di Azure combina gli eventi di più sequenze temporali in casi come i seguenti:

* Generazione di output da più partizioni. Le query in cui non è presente una clausola esplicita **Partition by PartitionId** devono combinare gli eventi da tutte le partizioni.
* Unione di due o più origini di input differenti.
* Join delle origini di input.

Negli scenari in cui vengono combinate più sequenze temporali, Analisi di flusso di Azure genera un output per il timestamp *t1* solo dopo che tutte le origini combinate sono almeno all'ora *t1*. Ad esempio, si supponga che la query legga da una partizione dell'hub eventi che ha due partizioni. Una delle partizioni, *P1*, ha eventi fino all'ora *t1*. L'altra partizione, *P2*, ha eventi fino all'ora *t1 + x*. L'output viene quindi generato fino all'ora *t1*. Tuttavia, se è presente una clausola esplicita **Partition by PartitionId**, entrambe le partizioni avanzano in modo indipendente.

L'impostazione della tolleranza per arrivo in ritardo viene usata per gestire l'assenza di dati in alcune partizioni.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Configurazione di tolleranza per arrivo in ritardo e tolleranza elementi non in ordine
I flussi di input che non sono in ordine vengono:
* Ordinati (e perciò ritardati)
* Regolati dal sistema, in base al criterio specificato dall'utente

Analisi di flusso di Azure tollera eventi in ritardo e non in ordine durante l'elaborazione in base al tempo applicazione. Le impostazioni seguenti sono disponibili nell'opzione **Ordinamento eventi** nel portale di Azure: 

![Gestione degli eventi con Analisi di flusso](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>Tolleranza per arrivo in ritardo
La tolleranza per arrivo in ritardo è applicabile solo quando l'elaborazione si basa sul tempo applicazione. In caso contrario, l'impostazione viene ignorata.

La tolleranza per arrivo in ritardo è la differenza massima tra l'ora di arrivo e il tempo applicazione. Se un evento arriva più tardi rispetto alla tolleranza per arrivo in ritardo (ad esempio, tempo applicazione *app_t* < ora di arrivo *arr_t* -tolleranza per arrivo in ritardo *late_t*), l'evento viene allineato al valore massimo della tolleranza per arrivo in ritardo (*arr_t* - *late_t*). La finestra di arrivo in ritardo è il ritardo massimo tra la generazione dell'evento e la ricezione dello stesso nell'origine di input. 

Quando più partizioni dello stesso flusso di input o più flussi di input vengono combinate, la tolleranza per arrivo in ritardo è la quantità massima di tempo che ogni partizione resta in attesa di nuovi dati. 

La regolazione in base alla tolleranza per arrivo in ritardo viene eseguita per prima, seguita da quella degli elementi non in ordine. La colonna **System.Timestamp** contiene il timestamp finale assegnato all'evento.

### <a name="out-of-order-tolerance"></a>Tolleranza elementi non in ordine
Gli eventi che non arrivano in ordine, ma entro la finestra di tolleranza degli elementi non in ordine stabilita, vengono riordinati in base al timestamp. Gli eventi che arrivano con un ritardo maggiore rispetto alla finestra di tolleranza vengono:
* **Regolati**: regolati in modo che sembrino arrivati nel momento di massimo ritardo accettabile. 
* **Eliminati**: rimossi.

Quando Analisi di flusso di Azure riordina gli eventi ricevuti entro la finestra di tolleranza degli elementi non in ordine, l'output della query è ritardato in base alla finestra di tolleranza elementi non in ordine.

### <a name="example"></a>Esempio

* Tolleranza per arrivo in ritardo = 10 minuti<br/>
* Tolleranza elementi non in ordine = 3 minuti<br/>
* Elaborazione in base al tempo applicazione<br/>
* Eventi:
   1. **Tempo applicazione** = 00:00:00, **Ora di arrivo** = 00:10:01, **System.Timestamp** = 00:00:01, regolato perché (**Ora di arrivo - Tempo applicazione**) è maggiore rispetto alla tolleranza per arrivo in ritardo.
   2. **Tempo applicazione** = 00:00:01, **Ora di arrivo** = 00:10:01, **System.Timestamp** = 00:00:01, non regolato perché il tempo applicazione rientra nella finestra di arrivo in ritardo.
   3. **Tempo applicazione** = 00:10:00, **Ora di arrivo** = 00:10:02, **System.Timestamp** = 00:10:00, non regolato perché il tempo applicazione rientra nella finestra di arrivo in ritardo.
   4. **Tempo applicazione** = 00:09:00, **Ora di arrivo** = 00:10:03, **System.Timestamp** = 00:09:00, accettato con il timestamp originale perché il tempo applicazione rientra nella tolleranza elementi non ordine.
   5. **Tempo applicazione** = 00:06:00, **Ora di arrivo** = 00:10:04, **System.Timestamp** = 00:07:00, regolato perché il tempo applicazione è precedente alla tolleranza elementi non in ordine.

### <a name="practical-considerations"></a>Considerazioni pratiche
Come già detto, la tolleranza per arrivo in ritardo è la differenza massima tra il tempo applicazione e l'ora di arrivo. Durante l'elaborazione in base al tempo applicazione, gli eventi successivi rispetto alla tolleranza per arrivo in ritardo configurata vengono regolato prima che sia applicata l'impostazione tolleranza elementi non in ordine. Pertanto, gli elementi non in ordine effettivi sono la differenza minima tra la tolleranza per arrivo in ritardo e la tolleranza elementi non in ordine.

Gli eventi non in ordine all'interno di un flusso si verificano a causa di vari motivi, tra cui:
* Sfasamento di orario tra i mittenti.
* Latenza variabile tra il mittente e l'origine evento di input.

I motivi di arrivo in ritardo includono:
* I mittenti elaborano gli eventi in batch e li inviano per un intervallo successivo, dopo l'intervallo.
* La latenza tra l'invio dell'evento da parte del mittente e la sua ricezione in corrispondenza dell'origine di input.

Quando si configurano la tolleranza per arrivo in ritardo e la tolleranza elementi non in ordine per un processo specifico, occorre considerare la correttezza, i requisiti di latenza e i fattori elencati in precedenza.

Di seguito sono riportati alcuni esempi.

#### <a name="example-1"></a>Esempio 1 
La query ha una clausola **Partition by PartitionId**. All'interno di una singola partizione, gli eventi vengono inviati con metodi di invio sincrono. I metodi di invio sincrono si bloccano finché non vengono inviati gli eventi.

In questo caso, gli elementi non in ordine sono pari a zero poiché gli eventi vengono inviati in ordine con la conferma esplicita prima di inviare l'evento successivo. L'arrivo in ritardo è il ritardo massimo tra la generazione dell'evento e l'invio dell'evento, più la latenza massima tra il mittente e l'origine di input.

#### <a name="example-2"></a>Esempio 2
La query ha una clausola **Partition by PartitionId**. All'interno di una singola partizione, gli eventi vengono inviati con metodi di invio asincrono. I metodi di invio asincrono possono avviare più invii contemporaneamente, che possono causare eventi non in ordine.

In questo caso, sia gli elementi non in ordine che l'arrivo in ritardo sono almeno il ritardo massimo tra la generazione dell'evento e l'invio dell'evento, più la latenza massima tra il mittente e l'origine di input.

#### <a name="example-3"></a>Esempio 3
La query non include la clausola **Partition by PartitionId** e sono presenti almeno due partizioni.

La configurazione è la stessa dell'esempio 2. Tuttavia, l'assenza di dati in una delle partizioni può ritardare l'output di un'ulteriore finestra di tolleranza per arrivo in ritardo.

## <a name="handling-event-producers-with-differing-timelines"></a>Gestione di producer di eventi con sequenze temporali diverse
Spesso un flusso di eventi di input contiene eventi provenienti da più producer di eventi, ad esempio singoli dispositivi. Questi eventi possono arrivare non in ordine per i motivi descritti in precedenza. In questi scenari, anche se il disordine tra producer di eventi potrebbe essere consistente, il disordine tra gli eventi di un singolo producer è minimo o inesistente.

Analisi di flusso di Azure offre meccanismi generali per la gestione degli eventi non in ordine. Questi meccanismi producono ritardi di elaborazione (nell'attesa che gli eventi non in ordine raggiungano il sistema), eliminazione o modifica degli eventi o entrambi.

Tuttavia, in molti scenari, la query desiderata sta elaborando gli eventi di diversi producer in modo indipendente. Ad esempio, potrebbe star eseguendo l'aggregazione degli eventi per finestra, per dispositivo. In questi casi non è necessario ritardare l'output che corrisponde a un producer di eventi nell'attesa del recupero da parte degli altri producer. In altre parole, non è necessario gestire lo sfasamento dell'ora tra i producer. È possibile ignorarla.

Naturalmente, questo significa che gli eventi di output stessi non sono in ordine rispetto ai relativi timestamp. Il consumer a valle deve essere in grado di gestire tale comportamento. Tuttavia, ogni evento nell'output è corretto.

Analisi di flusso di Azure implementa questa funzionalità usando la clausola [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx).

## <a name="summary"></a>Summary
* Configurare la tolleranza per arrivo in ritardo e la finestra di tolleranza elementi non in ordine in base ai requisiti di latenza e correttezza. Considerare inoltre la modalità di invio degli eventi.
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
