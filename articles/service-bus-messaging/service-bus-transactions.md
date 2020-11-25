---
title: Panoramica dell'elaborazione delle transazioni nel bus di servizio di Azure
description: Questo articolo offre una panoramica dell'elaborazione delle transazioni e della funzionalità di invio tramite il bus di servizio di Azure.
ms.topic: article
ms.date: 10/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9162b8578fe4f48cc3740b38d9d84ffaa2f260de
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023602"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Panoramica dell'elaborazione delle transazioni del bus di servizio

Questo articolo descrive le funzionalità delle transazioni del bus di servizio di Microsoft Azure. Gran parte della discussione è illustrata nell' [esempio relativo alle transazioni AMQP con il bus di servizio](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Questo articolo si limita a una panoramica dell'elaborazione delle transazioni e della funzionalità *invia tramite* nel bus di servizio, invece l'esempio delle transazioni atomiche ha un ambito di riferimento più ampio e complesso.

## <a name="transactions-in-service-bus"></a>Transazioni nel bus di servizio

Una *transazione* raggruppa due o più operazioni in un *ambito di esecuzione*. Per natura, questo tipo di transazione deve garantire che tutte le operazioni appartenenti a un determinato gruppo di operazioni abbiano esito positivo o negativo. In questo contesto, le transazioni agiscono come unità, spesso definita *atomicità*.

Il bus di servizio è un gestore di messaggi transazionali e assicura l'integrità transazionale per tutte le operazioni interne eseguite in relazione agli archivi del messaggio. Tutti i trasferimenti di messaggi all'interno del bus di servizio, ad esempio lo spostamento di messaggi a una [coda dei messaggi non recapitabili](service-bus-dead-letter-queues.md) o all'[inoltro automatico](service-bus-auto-forwarding.md) dei messaggi tra entità, sono transazionali. Di conseguenza, se il bus di servizio accetta un messaggio, questo è già stato archiviato e contrassegnato con un numero di sequenza. Da questo momento, i trasferimenti di messaggi all'interno del bus di servizio sono operazioni coordinate tra le entità e non causeranno una perdita (l'origine ha esito positivo, mentre la destinazione ha esito negativo) o una duplicazione (l'origine ha esito negativo, mentre la destinazione ha esito positivo) del messaggio.

Il bus di servizio supporta le operazioni di raggruppamento in una singola entità di messaggistica (coda, argomento, sottoscrizione) nell'ambito di una transazione. Ad esempio, è possibile inviare più messaggi a una coda da un ambito di transazione e verrà eseguito il commit dei messaggi al log della coda solo quando la transazione viene completata correttamente.

## <a name="operations-within-a-transaction-scope"></a>Operazioni nell'ambito di una transazione

È possibile eseguire le operazioni nell'ambito di una transazione come indicato di seguito:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: `Send` , `SendAsync` , `SendBatch` ,`SendBatchAsync`
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: `Complete` , `CompleteAsync` , `Abandon` , `AbandonAsync` , `Deadletter` , `DeadletterAsync` , `Defer` , `DeferAsync` , `RenewLock` , `RenewLockAsync` 

Le operazioni di ricezione non vengono incluse perché si presuppone che l'applicazione acquisisca i messaggi usando la modalità [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode), all'interno di alcuni cicli di ricezione o di un callback [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage), e solo successivamente apra un ambito di transazione per l'elaborazione del messaggio.

La ricezione del messaggio (completamento, abbandono, non recapitabilità, rinvio) si verifica all'interno dell'ambito e dipende dal risultato complessivo della transazione.

## <a name="transfers-and-send-via"></a>Trasferimenti e "invia tramite"

Per consentire la consegna transazionale di dati da una coda o un argomento a un processore e quindi a un'altra coda o argomento, il bus di servizio supporta i *trasferimenti*. In un'operazione di trasferimento, un mittente invia prima di tutto un messaggio a una *coda o* a un argomento di trasferimento e la coda o l'argomento di trasferimento sposta immediatamente il messaggio alla coda o all'argomento di destinazione desiderato usando la stessa implementazione di trasferimento affidabile su cui si basa la funzionalità di autoinoltri. Non viene mai eseguito il commit del messaggio nel log della coda di trasferimento o dell'argomento in modo che diventi visibile per i consumer della coda di trasferimento o dell'argomento.

La potenza di questa funzionalità transazionale diventa evidente quando la coda di trasferimento o l'argomento stesso è l'origine dei messaggi di input del mittente. In altre parole, il bus di servizio può trasferire il messaggio alla coda o all'argomento di destinazione "tramite" la coda o l'argomento di trasferimento, durante l'esecuzione di un'operazione completa (o rinviata o non recapitabile) sul messaggio di input, tutto in un'unica operazione atomica. 

### <a name="see-it-in-code"></a>Codice di esempio

Per impostare tali trasferimenti, si crea un mittente del messaggio che fa riferimento alla coda di destinazione tramite la coda di trasferimento. È inoltre disponibile un destinatario che estrae i messaggi dalla stessa coda. Ad esempio:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Una transazione semplice usa quindi questi elementi, come nell'esempio seguente. Per fare riferimento all'esempio completo, vedere il [codice sorgente in GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="timeout"></a>Timeout
Si verifica il timeout di una transazione dopo 2 minuti. Il timer di transazione viene avviato all'avvio della prima operazione nella transazione. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle code del bus di servizio, vedere gli articoli seguenti:

* [Come usare le code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Concatenamento di entità del bus di servizio con l'inoltro automatico](service-bus-auto-forwarding.md)
* [Esempio di autoinoltr](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Esempio di transazioni atomiche con il bus di servizio](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Analogie e differenze tra le code di Azure e le code del bus di servizio](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


