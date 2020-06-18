---
title: Sessioni di messaggi del bus di servizio di Azure | Microsoft Docs
description: In questo articolo viene illustrato come usare le sessioni per abilitare la gestione congiunta e ordinata di sequenze non vincolate di messaggi correlati.
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
ms.date: 05/20/2020
ms.author: aschhab
ms.openlocfilehash: 9cedf3678fc73b004c142380b4ba69c10ca72ebf
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726996"
---
# <a name="message-sessions"></a>Sessioni di messaggistica
Le sessioni del bus di servizio di Microsoft Azure consentono la gestione congiunta e ordinata di sequenze non vincolate di messaggi correlati. Le sessioni possono essere usate in criteri **First in, First out (FIFO)** e di **richiesta-risposta**. Questo articolo illustra come usare le sessioni per implementare questi criteri quando si usa il bus di servizio. 

> [!NOTE]
> Il livello Basic del bus di servizio non supporta le sessioni. I livelli Standard e Premium supportano le sessioni. Per le differenze tra questi livelli, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Criterio FIFO (First in, First out)
Per realizzare una garanzia FIFO nel bus di servizio, usare le sessioni. Il bus di servizio non prescrive la natura della relazione tra i messaggi e non definisce nemmeno un modello specifico per determinare dove inizia o finisce una sequenza di messaggi.

Qualsiasi mittente può creare una sessione quando invia i messaggi in un argomento o una coda impostando la proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) su un identificatore definito dall'applicazione univoco per la sessione. A livello di protocollo AMQP 1.0, questo valore è mappato alla proprietà *group-id*.

Nelle code o nelle sottoscrizioni in grado di riconoscere le sessioni, queste vengono create quando c'è almeno un messaggio con la proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) della sessione. Una volta creata una sessione, non c'è un'API o un tempo definito per la scadenza o la rimozione della sessione. In teoria, è possibile ricevere un messaggio per una sessione oggi e il messaggio successivo tra un anno e, se la proprietà **SessionId** corrisponde, la sessione dal punto di vista del bus di servizio è la stessa.

In genere, tuttavia, un'applicazione riconosce chiaramente dove inizia e dove finisce un set di messaggi correlati, il bus di servizio non imposta alcuna regola specifica.

Per delineare una sequenza per il trasferimento di un file è ad esempio possibile impostare la proprietà **Label** per il primo messaggio su **start**, per i messaggi intermedi su **content** e per l'ultimo messaggio su **end**. La posizione relativa dei messaggi di contenuto può essere calcolata come differenza tra il valore *SequenceNumber* del messaggio corrente e il valore **SequenceNumber** del messaggio contrassegnato come *start*.

La funzionalità di sessione nel bus di servizio consente un'operazione di ricezione specifica, sotto forma di [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession), nelle API Java e C#. Per abilitare la funzionalità, impostare la proprietà [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) nella coda o nella sottoscrizione tramite Azure Resource Manager oppure impostare il flag nel portale. Questa operazione è necessaria prima di cercare di usare le operazioni API correlate.

Nel portale impostare il flag con la casella di controllo seguente:

![][2]

> [!NOTE]
> Quando le sessioni sono abilitate in una coda o in una sottoscrizione, le applicazioni client ***non possono più*** inviare/ricevere messaggi regolari. Tutti i messaggi devono essere inviati come parte di una sessione (impostando l'ID sessione) e ricevuti ricevendo la sessione.

Le API per le sessioni sono presenti nei client di accodamento e di sottoscrizione. Esiste un modello imperativo che controlla quando vengono ricevuti i messaggi e le sessioni e un modello basato su gestore, simile a *OnMessage*, che nasconde la complessità di gestione del ciclo di ricezione.

### <a name="session-features"></a>Funzionalità delle sessioni

Le sessioni forniscono il demultiplexing simultaneo dei flussi di messaggi con interfoliazione, conservando e garantendo il recapito ordinato.

![][1]

Viene creato un ricevitore [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) dal client che accetta una sessione. Il client chiama [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) o [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) in C#. Nel modello di callback reattivo viene registrato un gestore di sessione.

Quando l'oggetto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) viene accettato e mentre viene gestito da un client, il client mantiene un blocco esclusivo su tutti i messaggi con il [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) della sessione presenti nella coda o nella sottoscrizione, nonché su tutti i messaggi con tale **SessionId** che arrivano mentre la sessione è ancora attiva.

Il blocco viene rilasciato quando viene chiamato **Close** o **CloseAsync** oppure quando scade, nei casi in cui l'applicazione non è in grado di eseguire l'operazione di chiusura. Il blocco della sessione deve essere considerato come un blocco esclusivo su un file, vale a dire che l'applicazione deve chiudere la sessione non appena non è più necessaria e/o quando non sono previsti altri messaggi.

Quando più ricevitori simultanei eseguono il pull dalla coda, i messaggi che appartengono a una sessione specifica vengono inviati al ricevitore specifico che attualmente ha bloccato la sessione. Con questa operazione, viene eseguito il demultiplexing di un flusso di messaggi con interfoliazione in una coda o una sottoscrizione in ricevitori diversi, che possono trovarsi anche in computer client diversi, perché la gestione del blocco avviene sul lato del servizio, all'interno del bus di servizio.

L'illustrazione precedente mostra tre ricevitori di sessioni simultanee. Una sessione con `SessionId` = 4 non ha nessun client proprietario attivo, il che significa che i messaggi non vengono recapitati da questa sessione specifica. Una sessione opera in molti modi, ad esempio come una coda secondaria.

Il blocco di sessione mantenuto dal ricevitore di sessione comprende i blocchi dei messaggi usati dalla modalità di finalizzazione *blocco di visualizzazione*. Un blocco su una sessione è possibile su un solo ricevitore. Anche se un ricevitore può avere molti messaggi in transito, i messaggi verranno ricevuti in ordine. Se un messaggio viene abbandonato, verrà presentato di nuovo con la successiva operazione di ricezione.

### <a name="message-session-state"></a>Stato della sessione di messaggi

Quando i flussi di lavoro vengono elaborati in sistemi cloud a scalabilità elevata e a disponibilità elevata, il gestore del flusso di lavoro associato a una sessione specifica deve essere in grado di eseguire il ripristino in caso di errori imprevisti e riprendere un lavoro completato parzialmente su un processo o un computer diverso da quello dove il lavoro è stato iniziato.

La funzionalità di stato della sessione consente un'annotazione definita dall'applicazione di una sessione di messaggi all'interno del broker, in modo che lo stato di elaborazione registrato rispetto alla sessione diventi immediatamente disponibile quando la sessione viene acquisita da un nuovo elaboratore.

Dal punto di vista del bus di servizio, lo stato della sessione di messaggi è un oggetto binario opaco che può contenere i dati delle dimensioni di un messaggio, ovvero 256 KB per il livello Standard del bus di servizio e 1 MB per il livello Premium. Lo stato di elaborazione relativo a una sessione può essere conservato all'interno dello stato della sessione oppure lo stato della sessione può puntare a una posizione di archiviazione o a un record di database che contiene tali informazioni.

Le API per la gestione dello stato della sessione, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), sono disponibili nell'oggetto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nelle API Java e C#. Una sessione per la quale in precedenza non era impostato alcuno stato restituisce un riferimento **Null** per **GetState**. [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) consente di cancellare lo stato della sessione impostato in precedenza.

Lo stato della sessione rimane invariato fino a quando non viene cancellata (restituendo **null**), anche se sono stati usati tutti i messaggi presenti nella sessione.

Tutte le sessioni esistenti in una coda o una sottoscrizione possono essere enumerate con il metodo **SessionBrowser** nell'API Java e con [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) in [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) e [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) nel client .NET Framework.

Lo stato della sessione conservato in una coda o in una sottoscrizione viene conteggiato per il raggiungimento della quota di archiviazione dell'entità. Quando un'applicazione finisce di usare una sessione, è quindi consigliabile che esegua la pulizia dello stato conservato, per evitare costi di gestione esterni.

### <a name="impact-of-delivery-count"></a>Effetti del numero di recapiti

La definizione di numero di recapiti per messaggio nel contesto delle sessioni varia leggermente rispetto alla definizione in assenza di sessioni. Di seguito è riportata una tabella che riepiloga quando viene incrementato il numero di recapiti.

| Scenario | Numero di recapiti del messaggio incrementato |
|----------|---------------------------------------------|
| La sessione viene accettata, ma il blocco della sessione scade (a causa del timeout) | Sì |
| La sessione viene accettata, i messaggi all'interno della sessione non vengono completati (anche se sono bloccati) e la sessione viene chiusa | No |
| La sessione viene accettata, i messaggi vengono completati e la sessione viene chiusa in modo esplicito | N/A (flusso standard. Qui i messaggi vengono rimossi dalla sessione) |

## <a name="request-response-pattern"></a>Criterio richiesta-risposta
Il [criterio richiesta-risposta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) è un criterio di integrazione ben definito che consente all'applicazione mittente di inviare una richiesta e offre al ricevitore un modo per inviare correttamente una risposta all'applicazione mittente. Questo criterio richiede in genere un argomento o una coda di breve durata per l'invio di risposte da parte dell'applicazione. In questo scenario, le sessioni offrono una semplice soluzione alternativa con semantica paragonabile. 

Più applicazioni possono inviare richieste a una singola coda, con un parametro di intestazione specifico impostato per identificare in modo univoco l'applicazione mittente. L'applicazione ricevente è in grado di elaborare le richieste in arrivo nella coda e di inviare risposte nella coda abilitata per la sessione, impostando l'ID sessione sull'identificatore univoco inviato dal mittente sul messaggio della richiesta. L'applicazione che ha inviato la richiesta può quindi ricevere messaggi sull'ID di sessione specifico ed elaborare correttamente le risposte.

> [!NOTE]
> L'applicazione che invia le richieste iniziali deve conoscere l'ID della sessione e usare `SessionClient.AcceptMessageSession(SessionID)` per bloccare la sessione in cui è prevista la risposta. È consigliabile usare un GUID che identifichi in modo univoco l'istanza dell'applicazione come ID di sessione. Nella coda non deve essere presente alcun gestore di sessione o `AcceptMessageSession(timeout)`, per garantire che le risposte siano disponibili per essere bloccate ed elaborate da ricevitori specifici.

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi [Microsoft.Azure.ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) o [Microsoft.ServiceBus.Messaging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) per un esempio che usa il client .NET Framework per gestire messaggi in grado di riconoscere la sessione. 

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
