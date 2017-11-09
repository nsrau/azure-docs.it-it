---
title: Sessioni di messaggi del bus di servizio di Azure | Microsoft Docs
description: Gestire le sequenze di messaggi del bus di servizio di Azure con le sessioni.
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
ms.openlocfilehash: c6441d2119518e89a869ee52e5f0b80450ae2bbe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="message-sessions--first-in-first-out-fifo"></a>Sessioni di messaggi/First In, First Out (FIFO) 

Le sessioni del bus di servizio consentono la gestione congiunta e ordinata di sequenze non vincolate di messaggi correlati. Per realizzare una garanzia FIFO nel bus di servizio, è necessario usare le sessioni. Il bus di servizio non prescrive la natura della relazione tra i messaggi e non definisce nemmeno un modello specifico per determinare dove inizia o finisce una sequenza di messaggi.

Qualsiasi mittente può creare una sessione quando invia i messaggi in un argomento o una coda impostando la proprietà del broker [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) su un identificatore definito dall'applicazione univoco per la sessione. A livello di protocollo AMQP 1.0, questo valore è mappato alla proprietà *group-id*.

Nelle code o nelle sottoscrizioni in grado di riconoscere le sessioni, le sessioni vengono create quando c'è almeno un messaggio con la proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) della sessione. Una volta creata una sessione, non c'è un'API o un tempo definito per la scadenza o la rimozione della sessione. In teoria, è possibile ricevere un messaggio per una sessione oggi e il messaggio successivo tra un anno e, se la proprietà **SessionId** corrisponde, la sessione dal punto di vista del bus di servizio è la stessa.

In genere, tuttavia, un'applicazione riconosce chiaramente dove inizia e dove finisce un set di messaggi correlati, ma il bus di servizio non imposta alcuna regola specifica.

Per delineare una sequenza per il trasferimento di un file è ad esempio possibile impostare la proprietà **Label** per il primo messaggio su **start**, per i messaggi intermedi su **content** e per l'ultimo messaggio su **end**. La posizione relativa dei messaggi di contenuto può essere calcolata come differenza tra il valore *SequenceNumber* del messaggio corrente e il valore **SequenceNumber** del messaggio contrassegnato come *start*.

La funzionalità di sessione nel bus di servizio consente un'operazione di ricezione specifica, sotto forma di [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession), nelle API Java e C#. Per abilitare la funzionalità, impostare la proprietà [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) nella coda o nella sottoscrizione tramite Azure Resource Manager oppure impostare il flag nel portale. Questa operazione è necessaria prima di cercare di usare le operazioni API correlate.

Nel portale il flag viene impostato con la casella di controllo seguente:

![][2]

Le API per le sessioni sono presenti nei client di accodamento e di sottoscrizione. Ci sono un modello imperativo in cui è possibile controllare quando vengono ricevuti i messaggi e le sessioni e un modello basato su gestore, simile a *OnMessage*, che nasconde la complessità di gestione del ciclo di ricezione.

## <a name="session-features"></a>Funzionalità delle sessioni

Le sessioni forniscono il demultiplexing simultaneo dei flussi di messaggi con interfoliazione, conservando e garantendo il recapito ordinato.

![][1]

Viene creato un ricevitore [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) dal client che accetta una sessione. Il client chiama in modo imperativo [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) o [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) in C#. Nel modello di callback reattivo, viene registrato un gestore di sessione, come illustrato più avanti.

Quando l'oggetto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) viene accettato e mentre viene gestito da un client, il client mantiene un blocco esclusivo su tutti i messaggi con tale [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) presenti nella coda o nella sottoscrizione, nonché su tutti i messaggi con tale **SessionId** che arrivano mentre la sessione è ancora attiva.

Il blocco viene rilasciato quando viene chiamato **Close** o **CloseAsync** oppure quando scade, nei casi in cui l'applicazione non è in grado di eseguire questa operazione. Il blocco della sessione deve essere considerato come un blocco esclusivo su un file, vale a dire che l'applicazione deve chiudere la sessione non appena non è più necessaria e/o quando non sono previsti altri messaggi.

Quando più ricevitori simultanei eseguono il pull dalla coda, i messaggi che appartengono a una sessione specifica vengono inviati al ricevitore specifico che attualmente ha bloccato la sessione. Con questa operazione, viene eseguito il demultiplexing di un flusso di messaggi con interfoliazione che si trova in una coda o una sottoscrizione in ricevitori diversi, che possono trovarsi anche in computer client diversi, perché la gestione del blocco avviene sul lato del servizio, all'interno del bus di servizio.

Una coda rimane tuttavia una coda, ovvero non c'è un accesso casuale. Se più ricevitori simultanei sono in attesa di accettare sessioni specifiche oppure in attesa di messaggi da sessioni specifiche e c'è un messaggio nella parte superiore di una coda che appartiene a una sessione non ancora reclamata da alcun ricevitore, le consegne rimangono in attesa fino a quando un ricevitore di sessione non reclama tale sessione.

La figura precedente mostra tre ricevitori di sessioni simultanei, che devono tutti accettare attivamente i messaggi dalla coda affinché ogni ricevitore possa avanzare. La sessione precedente, con *SessionId*=4, non ha alcun client proprietario attivo e quindi non verrà recapitato alcun messaggio in alcuna posizione fino a quando tale messaggio non viene acquisito da un ricevitore di sessione proprietario appena creato.

Sebbene possa sembrare un comportamento vincolante, un singolo processo del ricevitore può gestire facilmente molte sessioni simultanee, in particolare quando sono scritte in codice rigorosamente asincrono. La gestione di alcune dozzine di sessioni simultanee è completamente automatica con il modello di callback.

La strategia per la gestione di numerose sessioni simultanee, per cui ogni sessione riceve i messaggi solo sporadicamente, prevede che il gestore elimini la sessione dopo un determinato tempo di inattività e riprenda l'elaborazione quando la sessione viene accettata con l'arrivo della sessione successiva.

Il blocco di sessione mantenuto dal ricevitore di sessione comprende i blocchi dei messaggi usati dalla modalità di finalizzazione *blocco di visualizzazione*. Un ricevitore non può avere due messaggi simultaneamente "in corso", ma i messaggi devono essere elaborati in ordine. È possibile ottenere un nuovo messaggio solo quando il messaggio precedente è stato completato o inserito nella coda di messaggi non recapitabili. Se un messaggio viene abbandonato, verrà presentato di nuovo con la successiva operazione di ricezione.

## <a name="message-session-state"></a>Stato della sessione di messaggi

Quando i flussi di lavoro vengono elaborati in sistemi cloud a scalabilità elevata e a disponibilità elevata, il gestore del flusso di lavoro associato a una sessione specifica deve essere in grado di eseguire il ripristino in caso di errori imprevisti, oltre che di riprendere un lavoro completato parzialmente su un processo o un computer diverso da quello dove il lavoro è stato iniziato.

La funzionalità di stato della sessione consente un'annotazione definita dall'applicazione di una sessione di messaggi all'interno del broker, in modo che lo stato di elaborazione registrato rispetto alla sessione diventi immediatamente disponibile quando la sessione viene acquisita da un nuovo elaboratore.

Dal punto di vista del bus di servizio, lo stato della sessione di messaggi è un oggetto binario opaco che può contenere i dati delle dimensioni di un messaggio, ovvero 256 KB per il livello Standard del bus di servizio e 1 MB per il livello Premium. Lo stato di elaborazione relativo a una sessione può essere conservato all'interno dello stato della sessione oppure lo stato della sessione può puntare a una posizione di archiviazione o a un record di database che contiene tali informazioni.

Le API per la gestione dello stato della sessione, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), sono disponibili nell'oggetto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nelle API Java e C#. Una sessione per la quale in precedenza non era impostato alcuno stato restituisce un riferimento **Null** per **GetState**. [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) consente di cancellare lo stato della sessione impostato in precedenza.

Tutte le sessioni esistenti in una coda o una sottoscrizione possono essere enumerate con il metodo **SessionBrowser** nell'API Java e con [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) in [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) e [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) nel client .NET.

Lo stato della sessione conservato in una coda o in una sottoscrizione viene conteggiato per il raggiungimento della quota di archiviazione dell'entità. Quando un'applicazione finisce di usare una sessione, è quindi consigliabile che esegua la pulizia dello stato conservato, per evitare costi di gestione esterni.

## <a name="next-steps"></a>Passaggi successivi

- [Esempio completo](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/BasicSessionSendReceiveUsingQueueClient) di invio e ricezione di messaggi basati su sessione dalle code del bus di servizio con la libreria .NET Standard.
- [Esempio](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) che usa il client .NET Framework per gestire i messaggi in grado di riconoscere le sessioni. 

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png