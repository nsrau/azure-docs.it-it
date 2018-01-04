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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Considerazioni sull'ordine di Azure Analitica flusso eventi

## <a name="arrival-time-and-application-time"></a>Ora di arrivo e tempo applicazione

In un flusso di dati temporali degli eventi, ogni evento viene assegnato un timestamp. Analitica flusso Azure assegna un timestamp a ogni evento ora di arrivo oppure tempo applicazione. Il **timestamp** della colonna è il timestamp assegnato all'evento. 

L'ora di arrivo viene assegnata nell'origine di input quando l'evento raggiunge l'origine. Ora di arrivo è possibile accedere utilizzando il **EventEnqueuedTime** proprietà per l'input dell'hub eventi e l'utilizzo di [BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) proprietà per l'input di blob. 

Tempo applicazione viene assegnato quando viene generato l'evento e fa parte del payload. Per elaborare gli eventi da tempo applicazione, utilizzare il **Timestamp da** clausola nella query select. Se il **Timestamp da** clausola è assente, gli eventi vengono elaborati dall'ora di arrivo. 

Azure Analitica di flusso di output nell'ordine di data e ora e fornisce le impostazioni per gestire i dati in ordine.


## <a name="handling-of-multiple-streams"></a>Gestione di più flussi

Un processo di Azure flusso Analitica combina gli eventi da più sequenze temporali in casi simile al seguente:

* Generazione di output da più partizioni. Le query che non dispongono di un oggetto esplicito **partizione PartitionId** clausola deve combinare gli eventi da tutte le partizioni.
* Unione di due o più origini di input differenti.
* Join delle origini di input.

In scenari in cui vengono combinate più sequenze temporali, Azure flusso Analitica produce l'output per timestamp *t1* solo dopo che tutte le origini che vengono combinate sono almeno in fase di *t1*. Ad esempio, si supponga che la query legge da una partizione di hub di eventi che dispone di due partizioni. Una delle partizioni, *P1*, dispone di eventi fino al momento *t1*. La partizione, *P2*, dispone di eventi fino al momento *t1 + x*. Viene quindi generato un output fino a ora *t1*. Ma se è presente un'esplicita **partizione PartitionId** clausola, entrambe le partizioni sullo stato di avanzamento in modo indipendente.

Per gestire l'assenza di dati in alcune partizioni viene utilizzata l'impostazione per la tolleranza di arrivo in ritardo.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Configurazione a tolleranza di arrivo in ritardo e la tolleranza in ordine
I flussi di input che non sono in ordine vengono:
* Ordinati (e pertanto ritardato)
* Regolato dal sistema, in base al criterio specificato dall'utente

Flusso Analitica tollerata eventi ad associazione tardi e in ordine quando esegue l'elaborazione da tempo applicazione. Le impostazioni seguenti sono disponibili nel **ordine degli eventi** opzione nel portale di Azure: 

![Gestione degli eventi con Analisi di flusso](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>Tolleranza di arrivo in ritardo
Tolleranza di arrivo in ritardo è applicabile solo quando esegue l'elaborazione da tempo applicazione. In caso contrario, l'impostazione viene ignorata.

La tolleranza di arrivo in ritardo è la differenza massima tra l'ora di arrivo e tempo applicazione. Se un evento arriva entro la tolleranza di arrivo in ritardo (ad esempio, il tempo applicazione *app_t* < ora di arrivo *arr_t* -tolleranza di criteri di arrivo tardiva *late_t*), evento viene regolato per il valore massimo della tolleranza di arrivo in ritardo (*arr_t* - *late_t*). La finestra di arrivo in ritardo è il ritardo massimo tra la generazione di eventi e la ricezione dell'evento all'origine di input. 

Quando si combinano più partizioni dello stesso flusso di input o più flussi di input, la tolleranza di arrivo ad associazione tardiva è la quantità massima di tempo in cui è in attesa di nuovi dati ogni partizione. 

Si verifica per prima in base a tolleranza di arrivo in ritardo di regolazione. Si verifica in seguito in base a tolleranza di errore in ordine di regolazione. Il **timestamp** della colonna è il timestamp finale assegnato all'evento.

### <a name="out-of-order-tolerance"></a>Tolleranza di errore in ordine
Eventi che arrivano senza ordine ma all'interno della finestra di tolleranza di errore in ordine set vengono riordinati dal timestamp. Gli eventi arrivati entro la finestra di tolleranza di errore sono:
* **Regolati**: regolati in modo che sembrino arrivati nel momento di massimo ritardo accettabile. 
* **Eliminati**: rimossi.

Quando il flusso Analitica Riordina eventi ricevuti all'interno della finestra di tolleranza di errore in ordine, l'output della query viene posticipata dalla finestra di tolleranza di errore in ordine.

### <a name="example"></a>Esempio

* Tolleranza per arrivo in ritardo = 10 minuti<br/>
* Tolleranza di errore in ordine = 3 minuti<br/>
* Elaborazione in base al tempo applicazione<br/>
* Eventi:
   1. **Tempo applicazione** = 00:00:00, **ora di arrivo** = 10:00:01, **timestamp** = 00:00:01, rettificare perché (**ora di arrivo - tempo applicazione**) è più della tolleranza di arrivo in ritardo.
   2. **Tempo applicazione** = 00:00:01, **ora di arrivo** = 10:00:01, **timestamp** = 00:00:01, non modificato perché l'applicazione ora è all'interno della finestra di arrivo in ritardo.
   3. **Tempo applicazione** = 00:10:00, **ora di arrivo** = 00:10:02 **timestamp** = 00:10:00, non modificato perché l'applicazione ora è all'interno della finestra di arrivo in ritardo.
   4. **Tempo applicazione** = 00:09:00, **ora di arrivo** = 10:00:03, **timestamp** = 00:09:00, accettato con il timestamp originale perché tempo applicazione è all'interno dell'ordine tolleranza di errore.
   5. **Tempo applicazione** = 06:00:00, **ora di arrivo** = 04:00:10, **timestamp** = 00:07:00, regolato perché tempo applicazione è precedente rispetto alla tolleranza in ordine.

### <a name="practical-considerations"></a>Considerazioni pratiche
Come accennato in precedenza, tolleranza di arrivo in ritardo è la differenza massima tra tempo applicazione e l'ora di arrivo. Quando esegue l'elaborazione da tempo applicazione, vengono modificati gli eventi entro la tolleranza configurata di arrivo in ritardo prima che venga applicata l'impostazione della tolleranza di errore in ordine. In tal caso, non in ordine di validità è il requisito minimo di tolleranza di arrivo in ritardo e tolleranza di errore in ordine.

I motivi per gli eventi in ordine all'interno di un flusso includono:
* Dello sfasamento di orario tra i mittenti.
* Variabile latenza tra il mittente e l'origine evento di input.

I motivi per arrivo in ritardo includono:
* Mittenti di invio in batch e l'invio di eventi per un intervallo in un secondo momento, dopo l'intervallo.
* Latenza tra l'invio dell'evento dal mittente e la ricezione dell'evento in corrispondenza l'origine di input.

Quando si configurano tolleranza arrivo in ritardo e la tolleranza in ordine per un processo specifico, prendere in considerazione i fattori precedenti, requisiti di latenza e correttezza.

Di seguito sono riportati alcuni esempi.

#### <a name="example-1"></a>Esempio 1 
La query ha un **partizione PartitionId** clausola. All'interno di una singola partizione, gli eventi vengono inviati tramite i metodi di invio sincrona. I metodi di invio sincrono si bloccano finché non vengono inviati gli eventi.

In questo caso, non in ordine è zero poiché gli eventi vengono inviati in base all'ordine esplicito conferma prima dell'invio dell'evento successivo. All'arrivo in ritardo è il ritardo massimo tra la generazione dell'evento e l'invio dell'evento, nonché la latenza massima tra il mittente e l'origine di input.

#### <a name="example-2"></a>Esempio 2
La query ha un **partizione PartitionId** clausola. All'interno di una singola partizione, gli eventi vengono inviati tramite i metodi di invio asincrono. Metodi di invio asincrono possono avviare più inviati allo stesso tempo, che possono causare gli eventi in ordine.

In questo caso, ad associazione tardiva e non in ordine di arrivo costituiscono almeno il ritardo massimo tra la generazione dell'evento e l'invio dell'evento, nonché la latenza massima tra il mittente e l'origine di input.

#### <a name="example-3"></a>Esempio 3
La query non dispone di un **partizione PartitionId** clausola, e sono presenti almeno due partizioni.

Configurazione è lo stesso esempio 2. Tuttavia, assenza di dati in una delle partizioni può ritardare l'output da una finestra di tolleranza arrivo in ritardo aggiuntiva.

## <a name="handling-event-producers-with-differing-timelines"></a>La gestione di producer di eventi con le sequenze temporali diversi
Un flusso singolo evento di input contiene spesso gli eventi provenienti da più producer di eventi, ad esempio i singoli dispositivi. Questi eventi possono arrivare senza ordine dovuto ai motivi descritti in precedenza. In questi scenari, anche se il disordine tra producer di eventi potrebbe essere di grandi dimensioni, il disordine in eventi da un produttore singolo è piccolo (o addirittura inesistenti).

Azure Analitica di flusso fornisce meccanismi generale per la gestione di eventi in ordine. Tali risultati meccanismi di ritardi di elaborazione (in attesa per gli eventi straggling raggiungere il sistema), eliminati o modificati gli eventi o entrambi.

Ancora in molti scenari, la query desiderata è l'elaborazione degli eventi dal producer di eventi diversi in modo indipendente. Ad esempio, potrebbe essere l'aggregazione degli eventi per ogni finestra, per ogni dispositivo. In questi casi, non è necessario ritardare l'output che corrisponde al produttore di un evento durante l'attesa di altri produttori di eventi per l'intercettazione. In altre parole, non è necessario affrontare di sfasamento dell'ora tra i produttori. È possibile ignorarla.

Naturalmente, ciò significa che gli stessi eventi di output non sono ordinati per quanto riguarda i relativi timestamp. Il consumer a valle deve essere in grado di gestire tale comportamento. Tuttavia, ogni evento nell'output sia corretto.

Azure flusso Analitica implementa questa funzionalità utilizzando il [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx) clausola.

## <a name="summary"></a>Summary
* Configurare tolleranza arrivo in ritardo e la finestra in ordine in base a requisiti di latenza e correttezza. Considerare inoltre la modalità di invio di eventi.
* È consigliabile che la tolleranza in ordine è inferiore a tolleranza di arrivo in ritardo.
* È inoltre più sequenze temporali, mancanza di dati in una delle origini o le partizioni può ritardare l'output da una finestra di tolleranza arrivo in ritardo aggiuntiva.

## <a name="get-help"></a>Ottenere aiuto
Per ulteriori informazioni, provare il [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi di flusso](stream-analytics-real-time-fraud-detection.md)
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sull'API REST di gestione di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx)
