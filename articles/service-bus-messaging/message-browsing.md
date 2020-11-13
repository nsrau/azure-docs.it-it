---
title: Bus di servizio di Azure-esplorazione messaggi
description: Esplora e visualizza messaggi del bus di servizio consente a un client del bus di servizio di Azure di enumerare tutti i messaggi in una coda o in una sottoscrizione.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553642"
---
# <a name="message-browsing"></a>Esplorazione dei messaggi

L'esplorazione dei messaggi o la visualizzazione consente a un client del bus di servizio di enumerare tutti i messaggi in una coda o una sottoscrizione, a scopo di diagnostica e di debug.

L'operazione di visualizzazione su una coda restituisce tutti i messaggi nella coda, non solo quelli disponibili per l'acquisizione immediata con `Receive()` o il `OnMessage()` ciclo. La proprietà `State` di ciascun messaggio indica se il messaggio è attivo (può essere ricevuto), [rinviato](message-deferral.md) o [pianificato](message-sequencing.md). L'operazione di visualizzazione su una sottoscrizione restituisce tutti i messaggi ad eccezione dei messaggi pianificati nel log dei messaggi di sottoscrizione. 

I messaggi usati e scaduti vengono eliminati da un'esecuzione asincrona di "Garbage Collection". Questo passaggio potrebbe non essere necessario immediatamente dopo la scadenza dei messaggi. Per questo motivo, `Peek` può restituire messaggi che sono già scaduti. Questi messaggi verranno rimossi o non recapitabili quando viene richiamata un'operazione di ricezione nella coda o nella sottoscrizione la volta successiva. Tenere presente questo comportamento quando si tenta di recuperare i messaggi posticipati dalla coda. Un messaggio scaduto non è più idoneo per il recupero regolare con altri metodi, anche quando viene restituito da Peek. La restituzione di questi messaggi è progettata in quanto Peek è uno strumento di diagnostica che riflette lo stato corrente del log.

Peek restituisce inoltre i messaggi che sono stati bloccati e sono attualmente in fase di elaborazione da altri ricevitori. Tuttavia, poiché Peek restituisce uno snapshot disconnesso, non è possibile osservare lo stato di blocco di un messaggio nei messaggi visualizzati. Le proprietà [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) e [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) generano un' [eccezione InvalidOperationException](/dotnet/api/system.invalidoperationexception) quando l'applicazione tenta di leggerli.

## <a name="peek-apis"></a>API Peek

I metodi [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) e [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) sono disponibili nelle librerie client .NET e Java e negli oggetti Receiver: **MessageReceiver** , **MessageSession**. Peek funziona sulle code, le sottoscrizioni e le rispettive code dei messaggi non recapitabili.

Quando viene chiamato ripetutamente, **Peek** enumera tutti i messaggi nel log della coda o della sottoscrizione, in ordine, dal numero di sequenza disponibile più basso al più alto. Si tratta dell'ordine in cui i messaggi sono stati accodati, non dell'ordine in cui i messaggi potrebbero essere recuperati.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) recupera più messaggi e li restituisce come enumerazione. Se non sono presenti messaggi, l'oggetto enumerazione è vuoto, non Null.

È anche possibile usare un overload del metodo con un [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) da cui iniziare, quindi chiamare l'overload del metodo senza parametri per enumerare ulteriormente. **PeekBatch** funziona in modo analogo, ma recupera contemporaneamente un set di messaggi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
