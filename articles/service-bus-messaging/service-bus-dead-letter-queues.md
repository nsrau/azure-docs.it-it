---
title: Code dei messaggi non recapitabili del bus di servizio | Documentazione Microsoft
description: Descrive le code dei messaggi non recapitabili nel bus di servizio di Azure. Le code del bus di servizio e le sottoscrizioni dell'argomento includono una coda secondaria chiamata coda di messaggi non recapitabili.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: 6630d96c90a221a6b0374f2e4758748a77ad0610
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647833"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Panoramica delle code dei messaggi non recapitabili del bus di servizio

Le code del bus di servizio di Azure e le sottoscrizioni dell'argomento includono una coda secondaria chiamata *coda di messaggi non recapitabili* (DLQ, Dead-Letter Queue). Non è necessario creare in modo esplicito la coda dei messaggi non recapitabili, che, tra l'altro, non può essere eliminata né altrimenti gestita indipendentemente dall'entità principale.

Questo articolo descrive le code dei messaggi non recapitabili nel bus di servizio. Questo argomento viene in gran parte illustrato nell'[esempio relativo alle code di messaggi non recapitabili](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) su GitHub.
 
## <a name="the-dead-letter-queue"></a>Coda di messaggi non recapitabili

Lo scopo della coda dei messaggi non recapitabili è conservare i messaggi che non possono essere recapitati ai ricevitori o che non possono essere elaborati. I messaggi possono essere rimossi dalla coda e verificati. Con l'aiuto di un operatore, un'applicazione potrebbe correggere i problemi e inviare nuovamente il messaggio, registrare la notizia che si è verificato un errore e intraprendere azioni correttive. 

Dal punto di vista di API e protocolli, la coda DLQ è molto simile a qualsiasi altra coda, ad eccezione del fatto che i messaggi possono essere inviati ad essa solo tramite l'operazione messaggi non recapitabili dell'entità padre. Inoltre, il parametro time-to-live non viene rispettato e non è possibile impostare come non recapitabile un messaggio di una coda DLQ. La coda dei messaggi non recapitabili supporta completamente il recapito con blocco di visualizzazione e le operazioni transazionali.

Non è prevista alcuna pulizia automatica della coda. I messaggi rimangono nella coda fino a quando non vengono esplicitamente recuperati e non si chiama il metodo [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) sul messaggio non recapitabile.

## <a name="dlq-message-count"></a>Numero di messaggi in coda DQL
Non è possibile ottenere il numero di messaggi nella coda dei messaggi non recapitabili a livello di argomento. Ciò è dovuto al fatto che i messaggi non si trovano a livello di argomento a meno che il bus di servizio non generi un errore interno. Al contrario, quando un mittente invia un messaggio a un argomento, il messaggio viene inoltrato alle sottoscrizioni per l'argomento nell'arco di millisecondi e quindi non risiede più a livello di argomento. È quindi possibile visualizzare i messaggi in coda DQL associati alla sottoscrizione per l'argomento. Nell'esempio seguente **Service Bus Explorer** indica che sono presenti 62 messaggi in coda DQL per la sottoscrizione "test1". 

![Numero di messaggi in coda DQL](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

È anche possibile ottenere il numero di messaggi in coda DQL usando il comando dell'interfaccia della riga di comando di Azure: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription?view=azure-cli-latest#az-servicebus-topic-subscription-show). 

## <a name="moving-messages-to-the-dlq"></a>Spostare messaggi nella coda DLQ

Nel bus di servizio sono presenti diverse attività che comportano l'inserimento di messaggi nella coda DLQ dall'interno del motore di messaggistica stesso. Un'applicazione può anche spostare in modo esplicito i messaggi nella coda dei messaggi non recapitabili. 

Quando un messaggio viene spostato dal broker, vengono aggiunte al messaggio due proprietà, `DeadLetterReason` e `DeadLetterErrorDescription`, nel momento in cui il broker chiama la sua versione interna del metodo [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) sul messaggio.

Le applicazioni possono definire i propri codici per la proprietà `DeadLetterReason`, ma il sistema imposta i valori seguenti.

| DeadLetterReason | DeadLetterErrorDescription |
| --- | --- |
|HeaderSizeExceeded |È stata superata la dimensione del flusso. |
|TTLExpiredException |Il messaggio è scaduto ed è stato configurato come non recapitabile. Per informazioni dettagliate, vedere la sezione [Superamento di TimeToLive](#exceeding-timetolive). |
|L'ID sessione ha valore null. |L'entità attivata dalla sessione non consente il recapito di un messaggio il cui identificatore di sessione è null. |
|MaxTransferHopCountExceeded | Numero massimo di hop consentiti durante l'inoltro tra le code. Il valore è impostato su 4. |
| MaxDeliveryCountExceededExceptionMessage | Il messaggio non può essere utilizzato dopo il numero massimo di tentativi di recapito. Per informazioni dettagliate, vedere la sezione [Superamento di MaxDeliveryCount](#exceeding-maxdeliverycount). |

## <a name="exceeding-maxdeliverycount"></a>Superamento di MaxDeliveryCount

Le code e le sottoscrizioni hanno ognuna una proprietà [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount). Il valore predefinito è 10. Ogni volta che un messaggio viene recapitato in un blocco ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)) ma viene abbandonato in modo esplicito oppure il blocco è scaduto, il valore [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) del messaggio viene incrementato. Quando il valore [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) supera [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), il messaggio viene spostato nella coda DLQ con il codice motivo `MaxDeliveryCountExceeded`.

Non è possibile disattivare questo comportamento, ma è possibile impostare [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) su un numero elevato.

## <a name="exceeding-timetolive"></a>Superamento di TimeToLive

Quando la proprietà [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) o [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) viene impostata su **true** (il valore predefinito è **false**), tutti i messaggi in scadenza vengono spostati nella coda DLQ con il codice motivo `TTLExpiredException`.

I messaggi scaduti vengono ripuliti e spostati nella coda di messaggi non recapitabili solo quando esiste almeno un ricevitore attivo che effettua il pull dalla sottoscrizione o dalla coda principale. Tale comportamento è predefinito.

## <a name="errors-while-processing-subscription-rules"></a>Errori durante l'elaborazione di regole di sottoscrizione

Quando la proprietà [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) di una sottoscrizione è abilitata, qualsiasi errore si verifichi durante l'esecuzione di una regola di filtro SQL di una sottoscrizione viene acquisito nella coda DLQ con il messaggio che indica l'errore.

## <a name="application-level-dead-lettering"></a>Definizione di messaggi non recapitabili a livello di applicazione

Oltre alle funzionalità di definizione dei messaggi non recapitabili del sistema, le applicazioni possono usare la coda DLQ per rifiutare esplicitamente i messaggi inaccettabili. Questi possono riguardare i messaggi che non possono essere elaborati correttamente a causa diversi problemi del sistema, i messaggi contenenti payload in formato non valido o che non superino il processo di autenticazione quando viene utilizzato un schema di sicurezza a livello di messaggio.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Messaggi non recapitabili negli scenari ForwardTo o SendVia

I messaggi verranno inviati nella coda dei messaggi non recapitabili di trasferimento nelle condizioni seguenti:

- Un messaggio passa attraverso più di quattro code o argomenti che sono [concatenati](service-bus-auto-forwarding.md).
- L'argomento o la coda di destinazione è disattivato o eliminato.
- L'argomento o la coda di destinazione supera le dimensioni massime dell'entità.

Per recuperare questi messaggi non recapitabili, è possibile creare un destinatario usando il metodo di utilità [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath).

## <a name="example"></a>Esempio

Il frammento di codice seguente crea un ricevitore del messaggio. Nel ciclo di ricezione della coda principale, il codice recupera il messaggio con [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), che richiede al broker di restituire immediatamente i messaggi disponibili o di restituire nessun risultato. Se riceve un messaggio, il codice lo abbandona immediatamente, incrementando `DeliveryCount`. Dopo che il sistema ha spostato il messaggio alla coda DLQ, la coda principale rimane vuota e il ciclo viene interrotto, perché [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) restituisce **null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>Percorso della coda di messaggi non recapitabili
È possibile accedere alla coda dei messaggi non recapitabili utilizzando la sintassi seguente:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Se si usa .NET SDK, è possibile ottenere il percorso della coda dei messaggi non recapitabili usando il metodo SubscriptionClient.FormatDeadLetterPath(). Questo metodo accetta il nome dell'argomento e il nome della sottoscrizione e i suffissi con **/$DeadLetterQueue**.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle code del bus di servizio, vedere gli articoli seguenti:

* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Analogie e differenze tra le code di Azure e le code del bus di servizio](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

