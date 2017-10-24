---
title: Esplorazione dei messaggi del bus di servizio di Azure | Microsoft Docs
description: Esplorare e visualizzare i messaggi del bus di servizio
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
ms.date: 09/27/2017
ms.author: sethm
ms.openlocfilehash: b0bc1ef7570ccac07975e2560a1d0501d3cde2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="message-browsing"></a>Esplorazione dei messaggi

L'esplorazione dei messaggi ("visualizzazione") consente a un client di enumerare tutti i messaggi presenti in una coda o in una sottoscrizione, in genere a scopo diagnostico o di debug.

Le operazioni di visualizzazione restituiscono tutti i messaggi presenti nel log dei messaggi della coda o della sottoscrizione, non solo quelli disponibili per l'acquisizione immediata con *Receive()* o il ciclo *OnMessage()*. La proprietà *State* di ciascun messaggio indica se il messaggio è attivo (può essere ricevuto), rinviato (vedere Differimento [collegamento da definire]) o pianificato (vedere Messaggi pianificati [collegamento da definire]).

I messaggi usati e scaduti vengono eliminati da un'esecuzione asincrona di "Garbage Collection" e non necessariamente nel momento in cui i messaggi scadono. Il metodo Peek potrebbe quindi restituire dei messaggi già scaduti che verranno rimossi o impostati come non recapitabili al prossimo richiamo di un'operazione di ricezione nella coda o nella sottoscrizione.

Questo aspetto è particolarmente importante da considerare quando si tenta di recuperare i messaggi rinviati dalla coda. Un messaggio per cui l'istante [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) è passato non è più idoneo per il recupero normale con qualsiasi modalità, anche se viene restituito dal metodo Peek. La restituzione di questi messaggi è intenzionale dato che il metodo Peek è uno strumento di diagnostica che visualizza lo stato attuale del log.

Inoltre il metodo Peek restituisce i messaggi che erano bloccati e vengono attualmente elaborati da altri ricevitori, ma non sono ancora stati completati. Tuttavia, dato che il metodo Peek restituisce uno snapshot disconnesso, lo stato di blocco di un messaggio non può essere osservato sui messaggi in anteprima e le proprietà [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) e [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) restituiranno [InvalidOperationException](/dotnet/api/system.invalidoperationexception) quando l'applicazione tenta di leggerli.

## <a name="peek-apis"></a>API Peek

I metodi [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) e [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) esistono in tutte le librerie client .NET e Java e in tutti gli oggetti ricevitori: **MessageReceiver**, **MessageSession**, **QueueClient** e **SubscriptionClient**. Il metodo Peek funziona in tutte le code e le sottoscrizioni e nelle rispettive code di messaggi non recapitabili.

Se viene chiamato ripetutamente, il metodo Peek enumera tutti i messaggi presenti nel log della coda o della sottoscrizione in ordine di numero di sequenza, dal numero più basso a quello più alto. Questo è l'ordine in cui i messaggi sono stati accodati. Non è l'ordine in cui i messaggi potrebbero essere recuperati.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) recupera più messaggi e li restituisce come enumerazione. Se non sono presenti messaggi, l'oggetto enumerazione è vuoto, non Null.

È inoltre possibile eseguire il seeding di un overload del metodo con un valore [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) da cui iniziare e poi chiamare l'overload del metodo senza parametri per continuare l'enumerazione. **PeekBatch** funziona in modo analogo, ma recupera contemporaneamente un set di messaggi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)