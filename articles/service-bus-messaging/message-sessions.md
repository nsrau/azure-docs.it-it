---
title: Sessioni di messaggi del bus di servizio di Azure | Microsoft Docs
description: In questo articolo viene illustrato come utilizzare le sessioni per abilitare la gestione congiunta e ordinata di sequenze illimitata di messaggi correlati.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 1e22641e9d4f9959c26cd2043ea2acd7e260e0f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314052"
---
# <a name="message-sessions"></a>Sessioni di messaggistica
Le sessioni del bus di servizio di Microsoft Azure consentono la gestione congiunta e ordinata di sequenze non vincolate di messaggi correlati. Le sessioni possono essere usate nei modelli FIFO (first in, first out) e richiesta-risposta. In questo articolo viene illustrato come utilizzare le sessioni per implementare questi modelli quando si usa il bus di servizio. 

## <a name="first-in-first-out-fifo-pattern"></a>Modello FIFO (First-In, First Out)
Per ottenere una garanzia FIFO nel bus di servizio, utilizzare le sessioni. Il bus di servizio non è prescrittivo sulla natura della relazione tra i messaggi e inoltre non definisce un modello particolare per determinare dove inizia o finisce una sequenza di messaggi.

> [!NOTE]
> Il livello di base del bus di servizio non supporta le sessioni. I livelli Standard e Premium supportano le sessioni. Per le differenze tra questi livelli, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

Qualsiasi mittente può creare una sessione quando invia i messaggi in un argomento o una coda impostando la proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) su un identificatore definito dall'applicazione univoco per la sessione. A livello di protocollo AMQP 1.0, questo valore è mappato alla proprietà *group-id*.

Nelle code o nelle sottoscrizioni in grado di riconoscere la sessione, le sessioni vengono create quando è presente almeno un messaggio con [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)della sessione. Una volta che una sessione esiste, non c'è un tempo definito o API per quando la sessione scade o scompare. In teoria, è possibile ricevere un messaggio per una sessione oggi e il messaggio successivo tra un anno e, se la proprietà **SessionId** corrisponde, la sessione dal punto di vista del bus di servizio è la stessa.

In genere, tuttavia, un'applicazione riconosce chiaramente dove inizia e dove finisce un set di messaggi correlati, Il bus di servizio non imposta regole specifiche.

Per delineare una sequenza per il trasferimento di un file è ad esempio possibile impostare la proprietà **Label** per il primo messaggio su **start**, per i messaggi intermedi su **content** e per l'ultimo messaggio su **end**. La posizione relativa dei messaggi di contenuto può essere calcolata come differenza tra il valore *SequenceNumber* del messaggio corrente e il valore **SequenceNumber** del messaggio contrassegnato come *start*.

La funzionalità di sessione nel bus di servizio consente un'operazione di ricezione specifica, sotto forma di [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession), nelle API Java e C#. Per abilitare la funzionalità, impostare la proprietà [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) nella coda o nella sottoscrizione tramite Azure Resource Manager oppure impostare il flag nel portale. È necessario prima di tentare di utilizzare le operazioni API correlate.

Nel portale impostare il flag con la casella di controllo seguente:

![][2]

> [!NOTE]
> Quando le sessioni sono abilitate in una coda o in una sottoscrizione, le applicazioni client non possono ***più*** inviare/ricevere messaggi regolari. Tutti i messaggi devono essere inviati come parte di una sessione (impostando l'ID di sessione) e ricevuti ricevendo la sessione.

Le API per le sessioni sono presenti nei client di accodamento e di sottoscrizione. Esiste un modello imperativo che controlla quando vengono ricevuti sessioni e messaggi e un modello basato su gestore, simile a *OnMessage*, che nasconde la complessità della gestione del ciclo di ricezione.

### <a name="session-features"></a>Funzionalità delle sessioni

Le sessioni forniscono il demultiplexing simultaneo dei flussi di messaggi con interfoliazione, conservando e garantendo il recapito ordinato.

![][1]

Viene creato un ricevitore [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) dal client che accetta una sessione. Il client chiama [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) o [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) in C#. Nel modello di callback reattivo viene registrato un gestore di sessione.

Quando l'oggetto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) viene accettato e mentre è mantenuto da un client, tale client mantiene un blocco esclusivo su tutti i messaggi con [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) di tale sessione presenti nella coda o nella sottoscrizione e anche su tutti i messaggi con tale **SessionId** che arrivano ancora mentre la sessione viene mantenuta.

Il blocco viene rilasciato quando **Close** o **CloseAsync** vengono chiamati o quando il blocco scade nei casi in cui l'applicazione non è in grado di eseguire l'operazione di chiusura. Il blocco della sessione deve essere considerato come un blocco esclusivo su un file, il che significa che l'applicazione deve chiudere la sessione non appena non ne ha più bisogno e/o non prevede ulteriori messaggi.

Quando più ricevitori simultanei eseguono il pull dalla coda, i messaggi che appartengono a una sessione specifica vengono inviati al ricevitore specifico che attualmente ha bloccato la sessione. Con tale operazione, un flusso di messaggi con interfoliazione in una coda o sottoscrizione viene de-multiplexed in modo pulito a ricevitori diversi e tali ricevitori possono anche vivere su computer client diversi, poiché la gestione dei blocchi avviene sul lato del servizio, all'interno del bus di servizio.

L'illustrazione precedente mostra tre ricevitori di sessioni simultanee. Una sessione con `SessionId` = 4 non ha nessun client proprietario attivo, il che significa che i messaggi non vengono recapitati da questa sessione specifica. Una sessione opera in molti modi, ad esempio come una coda secondaria.

Il blocco di sessione mantenuto dal ricevitore di sessione comprende i blocchi dei messaggi usati dalla modalità di finalizzazione *blocco di visualizzazione*. Un ricevitore non può avere due messaggi contemporaneamente "in volo", ma i messaggi devono essere elaborati in ordine. È possibile ottenere un nuovo messaggio solo quando il messaggio precedente è stato completato o inserito nella coda di messaggi non recapitabili. Se un messaggio viene abbandonato, verrà presentato di nuovo con la successiva operazione di ricezione.

### <a name="message-session-state"></a>Stato della sessione di messaggi

Quando i flussi di lavoro vengono elaborati in sistemi cloud ad alta scalabilità e disponibilità elevata, il gestore del flusso di lavoro associato a una determinata sessione deve essere in grado di eseguire il ripristino da errori imprevisti e può riprendere il lavoro parzialmente completato in un processo o in un computer diverso da cui è iniziato il lavoro.

La funzionalità di stato della sessione consente un'annotazione definita dall'applicazione di una sessione di messaggi all'interno del broker, in modo che lo stato di elaborazione registrato rispetto alla sessione diventi immediatamente disponibile quando la sessione viene acquisita da un nuovo elaboratore.

Dal punto di vista del bus di servizio, lo stato della sessione di messaggi è un oggetto binario opaco che può contenere i dati delle dimensioni di un messaggio, ovvero 256 KB per il livello Standard del bus di servizio e 1 MB per il livello Premium. Lo stato di elaborazione relativo a una sessione può essere conservato all'interno dello stato della sessione oppure lo stato della sessione può puntare a una posizione di archiviazione o a un record di database che contiene tali informazioni.

Le API per la gestione dello stato della sessione, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), sono disponibili nell'oggetto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nelle API Java e C#. Una sessione per la quale in precedenza non era impostato alcuno stato restituisce un riferimento **Null** per **GetState**. [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) consente di cancellare lo stato della sessione impostato in precedenza.

Lo stato della sessione rimane finché non viene cancellato (restituendo **null**), anche se vengono utilizzati tutti i messaggi in una sessione.

Tutte le sessioni esistenti in una coda o sottoscrizione possono essere enumerate con il **metodo SessionBrowser** nell'API Java e con [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) nel [client QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) e [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) nel client .NET Framework.

Lo stato della sessione conservato in una coda o in una sottoscrizione viene conteggiato per il raggiungimento della quota di archiviazione dell'entità. Quando un'applicazione finisce di usare una sessione, è quindi consigliabile che esegua la pulizia dello stato conservato, per evitare costi di gestione esterni.

### <a name="impact-of-delivery-count"></a>Impatto del conteggio delle consegne

La definizione di conteggio recapito per messaggio nel contesto delle sessioni varia leggermente dalla definizione in assenza di sessioni. Ecco una tabella che riassume quando il conteggio delle consegne viene incrementato.

| Scenario | Il conteggio recapiti del messaggio è incrementato |
|----------|---------------------------------------------|
| La sessione viene accettata, ma il blocco della sessione scade (a causa del timeout) | Sì |
| La sessione viene accettata, i messaggi all'interno della sessione non vengono completati (anche se sono bloccati) e la sessione viene chiusa | No |
| La sessione viene accettata, i messaggi vengono completati e quindi la sessione viene chiusa in modo esplicito | N/D (È il flusso standard. Qui i messaggi vengono rimossi dalla sessione) |

## <a name="request-response-pattern"></a>Modello di richiesta-rispostaRequest-response pattern
Il [modello di richiesta-risposta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) è un modello di integrazione consolidato che consente all'applicazione mittente di inviare una richiesta e fornisce al destinatario un modo per inviare correttamente una risposta all'applicazione mittente. Questo modello richiede in genere una coda o un argomento di breve durata a cui l'applicazione può inviare risposte. In questo scenario, le sessioni forniscono una soluzione alternativa semplice con semantica comparabile. 

Più applicazioni possono inviare le richieste a una singola coda di richieste, con un parametro di intestazione specifico impostato per identificare in modo univoco l'applicazione mittente. L'applicazione ricevente può elaborare le richieste in arrivo nella coda e inviare risposte su una coda abilitata per le sessioni, impostando l'ID di sessione sull'identificatore univoco che il mittente aveva inviato nel messaggio di richiesta. L'applicazione che ha inviato la richiesta può quindi ricevere messaggi su un ID di sessione specifico ed elaborare correttamente le risposte.

> [!NOTE]
> L'applicazione che invia le richieste iniziali deve `SessionClient.AcceptMessageSession(SessionID)` conoscere l'ID di sessione e utilizzarla per bloccare la sessione in cui si prevede la risposta. È consigliabile utilizzare un GUID che identifichi in modo univoco l'istanza dell'applicazione come ID di sessione. Non deve essere presente `AcceptMessageSession(timeout)` alcun gestore di sessione o nella coda per garantire che le risposte siano disponibili per essere bloccate ed elaborate da ricevitori specifici.

## <a name="next-steps"></a>Passaggi successivi

- Per un esempio in cui viene utilizzato il client .NET Framework per gestire i messaggi in grado di riconoscere la sessione, vedere [gli esempi Microsoft.Azure.ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) o [Microsoft.ServiceBus.Messaging.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) 

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
