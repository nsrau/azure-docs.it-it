---
title: Prelettura di messaggi del bus di servizio di Azure | Microsoft Docs
description: Migliorare le prestazioni tramite la prelettura del bus di servizio di Azure.
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 0a61918108a48f4a9fa3d1c07cc8d41525f1f2a0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="prefetch-azure-service-bus-messages"></a>Prelettura dei messaggi del bus di servizio di Azure

Quando la *Prelettura* è abilitata in uno dei client ufficiali del bus di servizio, il ricevitore acquisisce in modalità non interattiva più messaggi, fino al limite [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount), oltre al valore iniziale richiesto dall'applicazione.

Una singola chiamata iniziale di tipo [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) o [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) acquisisce quindi un messaggio per l'utilizzo immediato, che viene restituito non appena disponibile. Il client acquisisce quindi altri messaggi in background, per riempire il buffer di prelettura.

## <a name="enable-prefetch"></a>Abilitare la prelettura

Con .NET è possibile abilitare la funzionalità Prelettura impostando la proprietà [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) di **MessageReceiver**, **QueueClient** o **SubscriptionClient** su un numero maggiore di zero. La configurazione del valore su zero consente di disattivare la prelettura.

È possibile aggiungere con facilità questa impostazione sul lato di ricezione delle impostazioni degli esempi [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) o [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) per verificare l'effetto in questi contesti.

Mentre i messaggi sono disponibili nel buffer di prelettura, eventuali chiamate successive di tipo **Receive**/**ReceiveAsync** vengono soddisfatte immediatamente dal buffer e il buffer viene rifornito in background, non appena risulta disponibile spazio. Se non sono presenti messaggi disponibili per il recapito, l'operazione di ricezione svuota il buffer e quindi attende o si blocca, in base a quanto previsto.

La prelettura funziona allo stesso modo anche con le API [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) e [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync).

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Motivi per cui la Prelettura non è l'opzione predefinita anche se è più veloce

La Prelettura accelera il flusso di messaggi facendo in modo che un messaggio sia immediatamente disponibile per il recupero locale quando e prima che un'applicazione ne richieda uno. Questo vantaggio a livello di velocità effettiva è il risultato di un compromesso che l'autore dell'applicazione deve effettuare in modo esplicito:

Con la modalità di ricezione [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode.receiveanddelete), tutti i messaggi acquisiti nel buffer di prelettura non sono più disponibili nella coda e si trovano nel buffer di prelettura in memoria solo fino a quando non vengono ricevuti nell'applicazione tramite le API **Receive**/**ReceiveAsync** o **OnMessage**/**OnMessageAsync**. Se l'applicazione viene terminata prima della ricezione dei messaggi nell'applicazione, tali messaggi vengono irrimediabilmente persi.

Nella modalità di ricezione [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode.peeklock) i messaggi recuperati nel buffer di Prelettura vengono acquisiti nel buffer in uno stato di blocco e il clock di timeout per il blocco è attivato. Se il buffer di prelettura è di grandi dimensioni e l'elaborazione richiede una quantità di tempo tale che i blocchi del messaggio scadono durante la permanenza nel buffer di prelettura o addirittura mentre l'applicazione elabora il messaggio, è possibile che l'applicazione debba gestire alcuni eventi confusi.

È possibile che l'applicazione acquisisca un messaggio con un blocco scaduto o con scadenza imminente. In questo caso è possibile che l'applicazione elabori il messaggio ma non riesca a completarlo a causa della scadenza di un blocco. L'applicazione può verificare la proprietà [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc), che è soggetta allo sfasamento di orario del clock del broker e del clock del computer locale. Se il blocco del messaggio è scaduto, l'applicazione deve ignorare il messaggio. Non deve essere effettuata alcuna chiamata API sul messaggio o con il messaggio. Se il messaggio non è scaduto ma la scadenza è imminente, il blocco può essere rinnovato ed esteso di un altro periodo di blocco predefinito chiamando [message.RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Se il blocco scade automaticamente nel buffer di prelettura, il messaggio viene considerato come abbandonato e viene reso di nuovo disponibile per il recupero dalla coda. È quindi possibile che venga recuperato nel buffer di prelettura e posizionato al termine del buffer. Se il buffer di prelettura non può essere in genere usato completamente durante la scadenza del messaggio, è possibile che i messaggi vengano preletti ripetutamente ma non vengano mai effettivamente recapitati in uno stato usabile (con blocco valido) e che vengano infine spostati nella coda di messaggi non recapitabili quando viene raggiunto il numero massimo di recapiti.

Se è necessario un livello elevato di affidabilità per l'elaborazione di messaggi e se l'elaborazione richiede molto impegno e tempo, è consigliabile usare con cautela la funzionalità di prelettura o non usarla del tutto.

Se è necessaria una velocità effettiva elevata e l'elaborazione dei messaggi è in genere poco costosa, la prelettura offre vantaggi significativi a livello di velocità effettiva.

Il numero massimo di prelettura e la durata del blocco configurati nella coda o nella sottoscrizione devono essere bilanciati in modo che il timeout del blocco sia almeno superiore alla durata cumulativa prevista dell'elaborazione di messaggi per le dimensioni massime del buffer di prelettura, più un messaggio. Allo stesso tempo, è necessario che il timeout di blocco non sia superiore al valore [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) massimo dei messaggi quando vengono rilasciati accidentalmente, richiedendo quindi la scadenza del blocco prima del nuovo tentativo di recapito.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
