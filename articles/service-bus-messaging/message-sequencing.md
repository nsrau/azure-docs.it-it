---
title: Timestamp e sequenze di messaggi del bus di servizio di Azure | Microsoft Docs
description: Mantenere l'ordine e la sequenza dei messaggi del bus di servizio con i timestamp
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
ms.date: 09/28/2017
ms.author: sethm
ms.openlocfilehash: e97bdd645ef2a3efba83e3f87114c998f9a9e344
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="message-sequencing-and-timestamps"></a>Timestamp e sequenze di messaggi

I timestamp e le sequenze sono due funzionalità sempre abilitate in tutte le entità del bus di servizio e disponibili tramite le proprietà [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) e [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) dei messaggi ricevuti o esplorati.

Per i casi in cui l'ordine assoluto dei messaggi è significativo e/o in cui un consumer necessita di un identificatore univoco attendibile per i messaggi, il broker contrassegna i messaggi con un numero di sequenza crescente senza interruzioni in relazione alla coda o all'argomento. Per le entità partizionate, il numero di sequenza viene emesso in relazione alla partizione.

Il valore **SequenceNumber** è un numero intero univoco a 64 bit assegnato a un messaggio quando viene accettato e archiviato dal broker, usato come identificatore interno. Per le entità partizionate, i primi 16 bit rispecchiano l'identificatore della partizione. I numeri di sequenza ripartono da zero quando l'intervallo di 48/64 bit viene esaurito.

Il numero di sequenza può essere considerato attendibile come identificatore univoco perché viene assegnato da un'autorità centrale e neutra e non dai client. Rappresenta inoltre l'effettivo ordine di arrivo ed è più preciso rispetto a un timestamp come criterio di ordinamento, perché i timestamp potrebbero non avere una risoluzione sufficientemente alta in caso di frequenze di messaggi estreme e potrebbero essere soggetti a sfasamento di orario (anche se minimo) in situazioni in cui la proprietà del broker passa tra i nodi.

L'ordine di arrivo assoluto è importante, ad esempio, in scenari aziendali in cui un numero limitato di prodotti in offerta viene fornito a chi prima li richiede, fino all'esaurimento delle scorte, come nel caso di una vendita di biglietti per un concerto.

La funzionalità di timestamp funge da autorità neutra e affidabile che acquisisce in modo accurato l'ora UTC di arrivo di un messaggio, che si riflette nella proprietà **EnqueuedTimeUtc**. Il valore è utile se uno scenario aziendale dipende da scadenze, ad esempio se un elemento di lavoro è stato inviato in una certa data prima di mezzanotte, ma l'elaborazione è nel backlog della coda.

## <a name="scheduled-messages"></a>Messaggi pianificati

È possibile inviare messaggi a una coda o un argomento per l'elaborazione ritardata, ad esempio pianificando un processo in modo che diventi disponibile per l'elaborazione da parte di un sistema a una determinata ora. Questa funzionalità rende disponibile un'affidabile utilità di pianificazione distribuita basata sul tempo.

I messaggi pianificati non si materializzano nella coda fino all'ora di accodamento definita. Prima di tale ora, i messaggi pianificati possono essere annullati. L'annullamento comporta l'eliminazione del messaggio.

È possibile pianificare i messaggi impostando la proprietà [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) quando si invia un messaggio attraverso il normale percorso di invio oppure in modo esplicito con l'API [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_). Nel secondo caso viene immediatamente restituito il valore **SequenceNumber** del messaggio pianificato, che è possibile usare in un secondo momento per annullare il messaggio pianificato, se necessario. I messaggi pianificati e i relativi numeri di sequenza possono anche essere individuati tramite l'[esplorazione dei messaggi](message-browsing.md).

Il valore **SequenceNumber** per un messaggio pianificato è valido quando il messaggio si trova in questo stato. Quando il messaggio passa allo stato attivo, viene aggiunto alla coda come se fosse stato accodato nell'istante corrente e gli viene quindi assegnato un nuovo valore **SequenceNumber**.

Poiché la funzionalità è associata ai singoli messaggi e i messaggi possono essere accodati una sola volta, il bus di servizio non supporta le pianificazioni ricorrenti per i messaggi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)