<properties 
    pageTitle="Transazioni del bus di servizio | Microsoft Azure" 
    description="Panoramica delle transazioni atomiche del bus di servizio di Azure e invia tramite" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="05/16/2016"
    ms.author="clemensv;sethm"/>

# Panoramica dell'elaborazione delle transazioni del bus di servizio

Questo articolo descrive le funzionalità delle transazioni del bus di servizio. Gran parte della descrizione si basa sull'esempio relativo alle [transazioni atomiche con il bus di servizio](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions). Questo articolo si limita a una panoramica dell'elaborazione delle transazioni e della funzionalità *invia tramite* nel bus di servizio, mentre l'esempio relativo alle transazioni atomiche ha un ambito di riferimento più ampio e complesso.

## Transazioni nel bus di servizio

Una [transazione](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions) raggruppa due o più operazioni in un *ambito di esecuzione*. Per natura, questo tipo di transazione deve garantire che tutte le operazioni appartenenti a un determinato gruppo di operazioni abbiano esito positivo o negativo. In questo senso, le transazioni agiscono come un'unità, spesso definita anche *atomicità*.

Il bus di servizio è un gestore di messaggi transazionali e assicura l'integrità transazionale per tutte le operazioni interne eseguite in relazione agli archivi del messaggio. Tutti i trasferimenti di messaggi all'interno del bus di servizio, ad esempio lo spostamento di messaggi a una [coda dei messaggi non recapitabili](service-bus-dead-letter-queues.md) o l'[inoltro automatico](service-bus-auto-forwarding.md) dei messaggi tra entità, sono transazionali. Di conseguenza, se il bus di servizio accetta un messaggio, questo è già stato archiviato e contrassegnato con un numero di sequenza. Da questo momento, i trasferimenti di messaggi all'interno del bus di servizio sono operazioni coordinate tra le entità e non causeranno una perdita (l'origine ha esito positivo, mentre la destinazione ha esito negativo) o una duplicazione (l'origine ha esito negativo, mentre la destinazione ha esito positivo) del messaggio.

Il bus di servizio supporta le operazioni di raggruppamento in una singola entità di messaggistica (coda, argomento, sottoscrizione) all'interno dell'ambito di una transazione. Ad esempio, è possibile inviare più messaggi a una coda da un ambito di transazione e verrà eseguito il commit dei messaggi al log della coda solo quando la transazione viene completata correttamente.

## Operazioni nell'ambito di una transazione 

È possibile eseguire le operazioni nell'ambito di una transazione come indicato di seguito:

- **[QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx), [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)**: Send, SendAsync, SendBatch, SendBatchAsync 

- **[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)**: Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync

Le operazioni di ricezione non vengono incluse perché si presuppone che l'applicazione acquisisca i messaggi usando la modalità [ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), all'interno di alcuni cicli di ricezione o di un callback [OnMessage](https://msdn.microsoft.com/library/azure/dn369601.aspx), e solo in un secondo momento apra un ambito di transazione per l'elaborazione del messaggio.

La ricezione del messaggio (completamento, abbandono, non recapitabilità, rinvio) si verifica all'interno dell'ambito e dipende dal risultato complessivo della transazione.

## Trasferimenti e "invia tramite"

Per abilitare il passaggio transazionale dei dati da una coda a un processore e quindi a un'altra coda, il bus di servizio supporta i *trasferimenti*. In un'operazione di trasferimento, un mittente invia un messaggio a una "coda di trasferimento" e quest'ultima sposta immediatamente il messaggio alla coda di destinazione prestabilita usando la stessa implementazione di trasferimento affidabile su cui si basa la funzionalità di inoltro automatico. Non viene mai eseguito il commit del messaggio al log della coda di trasferimento in modo che diventi visibile agli utenti della coda di trasferimento.

L'efficacia di questa funzionalità transazionale diventa evidente quando la coda di trasferimento stessa è l'origine dei messaggi di input del mittente. In altri termini, il bus di servizio può trasferire il messaggio alla coda di destinazione "tramite" la coda di trasferimento, durante l'esecuzione di un'operazione di completamento (o rinvio o non recapitabilità) nel messaggio di input, il tutto in una singola operazione atomica.

### Codice di esempio

Per impostare tali trasferimenti, si crea un mittente del messaggio che fa riferimento alla coda di destinazione tramite la coda di trasferimento. Sarà inoltre disponibile un destinatario che estrae i messaggi dalla stessa coda. Ad esempio:

```
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Una transazione semplice usa questi elementi, come nell'esempio seguente:

```
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## Passaggi successivi

Per altre informazioni sulle code del bus di servizio, vedere gli articoli seguenti:

- [Concatenamento di entità del bus di servizio con l'inoltro automatico](service-bus-auto-forwarding.md)
- [Esempio di inoltro automatico](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
- [Esempio di transazioni atomiche con il bus di servizio](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
- [Analogie e differenze tra le code di Azure e le code del bus di servizio](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Come usare le code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)

<!---HONumber=AcomDC_0608_2016-->