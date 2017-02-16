---
title: Code e argomenti partizionati | Microsoft Docs
description: "Descrive come partizionare code e argomenti del bus di servizio usando più broker messaggi."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a0c7d5a2-4876-42cb-8344-a1fc988746e7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/22/2016
ms.author: sethm;hillaryc
translationtype: Human Translation
ms.sourcegitcommit: 6b169b3501ac938c1c0d621909dfc82418a7146e
ms.openlocfilehash: 7da16e2bca1332b63709ecff39af7124656db98a


---
# <a name="partitioned-queues-and-topics"></a>Code e argomenti partizionati
Il bus di servizio di Azure usa più broker messaggi per elaborare i messaggi e più archivi di messaggistica per archiviarli. Una coda o un argomento convenzionale è gestito da un singolo broker messaggi e archiviato in un archivio di messaggistica. Il bus di servizio consente anche il partizionamento delle code o degli argomenti tra più broker messaggi e archivi di messaggistica. Questo significa che la velocità effettiva complessiva di una coda o di un argomento partizionato non è più limitata dalle prestazioni di un singolo broker messaggi o archivio di messaggistica. Inoltre, un'interruzione temporanea dell'alimentazione di un archivio di messaggistica non determina la mancanza di disponibilità di una coda o di un argomento partizionato. Le code e gli argomenti partizionati possono contenere tutte le funzionalità avanzate del bus di servizio, ad esempio il supporto delle transazioni e delle sessioni.

Per altre informazioni sugli elementi interni del bus di servizio, vedere l'articolo [Architettura del bus di servizio][Service Bus architecture].

## <a name="how-it-works"></a>Funzionamento
Ogni coda o argomento partizionato è costituito da più frammenti. Ogni frammento viene memorizzato in un archivio di messaggistica differente e gestito da un broker messaggi diverso. Quando un messaggio viene inviato a una coda o a un argomento partizionato, il bus di servizio assegna il messaggio a uno dei frammenti. La selezione viene eseguita in modo casuale dal bus di servizio o tramite una chiave di partizione che può essere specificata dal mittente.

Quando un client vuole ricevere un messaggio da una coda partizionata, da una sottoscrizione o da un argomento partizionato, il bus di servizio esegue query su tutti i frammenti dei messaggi, quindi restituisce al ricevitore il primo messaggio ottenuto dagli archivi di messaggistica. Il bus di servizio memorizza nella cache gli altri messaggi e li restituisce quando riceve altre richieste. Un client destinatario non è a conoscenza del partizionamento; il comportamento verso il client di una coda o un argomento partizionato (ad esempio lettura, completamento, rinvio, non recapitabilità, prelettura) è identico a quello di un'entità normale.

I messaggi a una coda o a un argomento partizionato non presentano costi aggiuntivi, né in invio né in ricezione.

## <a name="enable-partitioning"></a>Abilitare il partizionamento
Per usare le code e gli argomenti partizionati con il bus di servizio di Azure, è necessario usare Azure SDK 2.2 o versione successiva oppure specificare `api-version=2013-10` nelle richieste HTTP.

È possibile creare code e argomenti del bus di servizio in dimensioni di 1, 2, 3, 4 o 5 GB (il valore predefinito è 1 GB). Con il partizionamento abilitato, il bus di servizio crea 16 partizioni per ogni GB specificato. Di conseguenza, se si crea una coda con dimensioni pari a 5 GB, con 16 partizioni le dimensioni massime della coda diventano di 80 GB (5 \* 16). È possibile vedere le dimensioni massime della coda o dell'argomento partizionato esaminando la voce corrispondente nel [portale di Azure][Azure portal].

Sono disponibili vari modi per creare una coda o un argomento partizionato. Quando si crea la coda o l'argomento dalla propria applicazione, è possibile abilitare il partizionamento per la coda o l'argomento impostando, rispettivamente, la proprietà [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] o [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] su **true**. Queste proprietà devono essere impostate al momento della creazione della coda o dell'argomento. Non è possibile modificare queste proprietà in una coda o in un argomento esistente. Ad esempio:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

In alternativa, è possibile creare una coda o un argomento partizionato in Visual Studio o nel [portale di Azure][Azure portal]. Quando si crea una nuova coda o un nuovo argomento nel portale, impostare l'opzione **Abilita partizionamento** nel pannello **Impostazioni generali** della finestra **Impostazioni** della coda o dell'argomento su **true**. In Visual Studio selezionare la casella di controllo **Abilita partizionamento** nella finestra di dialogo **Nuova coda** o **Nuovo argomento**.

## <a name="use-of-partition-keys"></a>Uso delle chiavi di partizione
Quando un messaggio viene accodato in una coda o in un argomento partizionato, il bus di servizio controlla la presenza di una chiave di partizione. Se ne trova una, seleziona il frammento in base a tale chiave. Se invece non trova alcuna chiave di partizione, seleziona il frammento in base a un algoritmo interno.

### <a name="using-a-partition-key"></a>Uso di una chiave di partizione
In alcuni scenari, ad esempio le sessioni o le transazioni, i messaggi devono essere archiviati in un frammento specifico. Tutti questi scenari richiedono l'uso di una chiave di partizione. Tutti i messaggi che usano la stessa chiave di partizione vengono assegnati allo stesso frammento. Se il frammento è temporaneamente non disponibile, il bus di servizio restituisce un errore.

In base allo scenario vengono usate come chiave di partizione proprietà dei messaggi diverse:

**SessionId**: se per un messaggio è impostata la proprietà [BrokeredMessage.SessionId][BrokeredMessage.SessionId], il bus di servizio usa questa proprietà come la chiave di partizione. In questo modo, tutti i messaggi appartenenti alla stessa sessione vengono gestiti dallo stesso broker messaggi. Questo consente a l bus di servizio di garantire l'ordinamento dei messaggi così come la coerenza degli stati della sessione.

**PartitionKey**: se per un messaggio è impostata la proprietà [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey], ma non la proprietà [BrokeredMessage.SessionId][BrokeredMessage.SessionId], il bus di servizio usa la proprietà [PartitionKey][PartitionKey] come la chiave di partizione. Se per il messaggio sono impostate entrambe le proprietà [SessionId][SessionId] e [PartitionKey][PartitionKey], queste devono avere un valore identico. Se la proprietà [PartitionKey][PartitionKey] è impostata su un valore diverso rispetto a quello della proprietà [SessionId][SessionId], il bus di servizio restituisce un'eccezione di operazione non valida. La proprietà [PartitionKey][PartitionKey] deve essere usata se un mittente invia messaggi transazionali non sono in grado di riconoscere le sessioni. La chiave di partizione assicura che tutti i messaggi inviati all'interno di una transazione vengano gestiti dallo stesso broker di messaggistica.

**MessageId**: se per la coda o l'argomento la proprietà [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] è impostata su **true** e la proprietà [BrokeredMessage.SessionId][BrokeredMessage.SessionId] o [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] non è impostata, la proprietà [BrokeredMessage.MessageId][BrokeredMessage.MessageId] viene usata come chiave di partizione. (tenere presente che le librerie di Microsoft .NET e AMQP assegnano automaticamente un ID messaggio, se questo non viene assegnato dall'applicazione mittente). In questo caso tutte le copie dello stesso messaggio vengono gestite dallo stesso broker messaggi. Questo consente al bus di servizio di rilevare ed eliminare i messaggi duplicati. Se la proprietà [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] non è impostata su **true**, il bus di servizio non considera la proprietà [MessageId][MessageId] come chiave di partizione.

### <a name="not-using-a-partition-key"></a>Senza l'uso di una chiave di partizione
In assenza di una chiave di partizione, il bus di servizio distribuisce, con un'alternanza di tipo round robin, i messaggi ai frammenti della coda o dell'argomento partizionato. Se il frammento scelto non è disponibile, il bus di servizio assegna il messaggio a un altro frammento. In questo modo, l'operazione di invio viene completata correttamente indipendentemente dalla disponibilità o meno di un archivio di messaggistica.

Per concedere al bus di servizio tempo sufficiente per l'accodamento del messaggio in un frammento diverso, il valore [MessagingFactorySettings.OperationTimeout][MessagingFactorySettings.OperationTimeout] specificato dal client che invia il messaggio deve essere maggiore di 15 secondi. È consigliabile impostare la proprietà [OperationTimeout][OperationTimeout] sul valore predefinito di 60 secondi.

Tenere presente che una chiave di partizione "aggiunge" un messaggio a un frammento specifico. Se l'archivio di messaggistica contenente questo frammento non è disponibile, il bus di servizio restituisce un errore. In assenza di una chiave di partizione, il bus di servizio può scegliere un frammento diverso e l'operazione verrà completata correttamente. È quindi consigliabile non specificare una chiave di partizione, a meno che non sia necessario.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Argomenti avanzati: usare le transazioni con entità partizionate
I messaggi inviati come parte di una transazione devono specificare una chiave di partizione. Tale chiave può essere una delle proprietà seguenti: [BrokeredMessage.SessionId][BrokeredMessage.SessionId], [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] o [BrokeredMessage.MessageId][BrokeredMessage.MessageId]. Tutti i messaggi che vengono inviati come parte della stessa transazione devono specificare la stessa chiave di partizione. Se si prova a inviare un messaggio senza una chiave di partizione in una transazione, il bus di servizio restituisce un'eccezione di operazione non valida. Se si prova a inviare più messaggi con chiavi di partizione diverse nella stessa transazione, il bus di servizio restituisce un'eccezione di operazione non valida. Ad esempio:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Se le proprietà che fungono da chiave di partizione sono impostate, il bus di servizio aggiunge il messaggio a un frammento specifico. Questo comportamento si verifica indipendentemente dall'uso di una transazione. È consigliabile non specificare una chiave di partizione, a meno che non sia necessario.

## <a name="using-sessions-with-partitioned-entities"></a>Uso delle sessioni con entità partizionate
Per inviare un messaggio transazionale a un argomento o una coda in grado di riconoscere la sessione, per il messaggio la proprietà [BrokeredMessage.SessionId][BrokeredMessage.SessionId] deve essere impostata. Se è specificata anche la proprietà [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey], quest'ultima deve avere un valore identico a quello della proprietà [SessionId][SessionId]. In caso contrario, il bus di servizio restituisce un'eccezione di operazione non valida.

A differenza delle code o degli argomenti normali (non partizionati), non è possibile usare una singola transazione per inviare più messaggi a sessioni diverse. Se è stato effettuato un tentativo, il bus di servizio restituirà un'eccezione di operazione non valida, ad esempio:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Inoltro automatico dei messaggi con entità partizionate
Il bus di servizio supporta l'inoltro automatico dei messaggi da, a o tra entità partizionate. Per abilitare l'inoltro automatico dei messaggi, impostare la proprietà [QueueDescription.ForwardTo][QueueDescription.ForwardTo] nella coda o nella sottoscrizione di origine. Se il messaggio specifica una chiave di partizione ([SessionId][SessionId], [PartitionKey][PartitionKey] o [MessageId][MessageId]), tale chiave viene usata per l'entità di destinazione.

## <a name="considerations-and-guidelines"></a>Considerazioni e indicazioni
* **Funzionalità a coerenza elevata**: se un'entità usa funzionalità come le sessioni, il rilevamento dei duplicati o il controllo esplicito della chiave di partizionamento, le operazioni di messaggistica vengono indirizzate sempre a frammenti specifici. In caso di traffico elevato in uno dei frammenti o di stato non integro dell'archivio sottostante, queste operazioni hanno esito negativo e la disponibilità viene ridotta. La coerenza è complessivamente molto più elevata rispetto alle entità non partizionate. I problemi si verificano solo per un sottoinsieme del traffico, invece che per tutto il traffico.
* **Gestione**: le operazioni di creazione, aggiornamento ed eliminazione devono essere eseguite su tutti i frammenti dell'entità. Se un frammento non è integro, potrebbero verificarsi errori per queste operazioni. Per l'operazione Get è necessario aggregare da tutti i frammenti le informazioni quali il numero di messaggi. Se un frammento non è integro, lo stato di disponibilità dell'entità risulta limitato.
* **Scenari con volumi ridotti di messaggi**: per questi scenari, in particolare se si usa il protocollo HTTP, potrebbe essere necessario eseguire più operazioni di ricezione per ottenere tutti i messaggi. Per le richieste di ricezione, il front-end esegue una ricezione su tutti i frammenti e memorizza nella cache tutte le risposte ricevute. Una richiesta di ricezione successiva sulla stessa connessione sarebbe avvantaggiata dalla memorizzazione nella cache e le latenze di ricezione saranno minori. Se tuttavia sono presenti più connessioni o si usa HTTP, ciò crea una nuova connessione per ogni richiesta. Non è quindi possibile assicurare che la richiesta venga ricevuta sullo stesso nodo. Se tutti i messaggi esistenti sono bloccati e memorizzati nella cache in un altro front-end, l'operazione di ricezione restituisce **null**. I messaggi raggiungeranno infine la scadenza e verranno ricevuti di nuovo. È consigliabile usare la connessione keep-alive HTTP.
* **Esaminare/Visualizzare l'anteprima dei messaggi**: [PeekBatch](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) non restituisce sempre il numero di messaggi specificati nella proprietà [MessageCount](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_MessageCount) a causa di due motivi comuni. Il primo motivo consiste nel fatto che le dimensioni aggregate della raccolta di messaggi superano le dimensioni massime peri a 256 KB. L'altro motivo dipende dal fatto che, se la [proprietà EnablePartitioning](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnablePartitioning) della coda o dell'argomento è impostata su **true**, è possibile che una partizione non includa una quantità di messaggi sufficiente per completare il numero di messaggi richiesto. In genere, se un'applicazione vuole ricevere un numero specifico di messaggi, deve chiamare ripetutamente [PeekBatch](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) fino a ottenere tale numero di messaggi o fino a quando non siano più presenti messaggi di cui visualizzare l'anteprima. Per altre informazioni, inclusi esempi di codice, vedere [QueueClient.PeekBatch](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) o [SubscriptionClient.PeekBatch](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_PeekBatch_System_Int32_).

## <a name="latest-added-features"></a>Funzionalità aggiunte di recente
* L'aggiunta o la rimozione di una regola è ora supportata con le entità partizionate. A differenza delle entità non partizionate, queste operazioni non sono supportate nelle transazioni. 
* AMQP è ora supportato per l'invio e la ricezione di messaggi verso e da un'entità partizionata.
* AMQP è ora supportato per le operazioni seguenti: [invio batch](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_BrokeredMessage__), [ricezione batch](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_ReceiveBatch_System_Int32_), [ricezione per numero di sequenza](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_Receive_System_Int64_), [visualizzazione dell'anteprima](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_Peek), [rinnovo del blocco](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_RenewMessageLock_System_Guid_), [pianificazione del messaggio](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_ScheduleMessageAsync_Microsoft_ServiceBus_Messaging_BrokeredMessage_System_DateTimeOffset_), [annullamento del messaggio pianificato](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_CancelScheduledMessageAsync_System_Int64_), [aggiunta di una nuova regola](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.ruledescription), [rimozione di una regola](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.ruledescription), [blocco del rinnovo della sessione](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_RenewLock), [impostazione dello stato della sessione](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_), [recupero dello stato della sessione](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_GetState) ed [enumerazione delle sessioni](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessionsAsync).

## <a name="partitioned-entities-limitations"></a>Limiti delle entità partizionate
Attualmente il bus di servizio impone alle code o agli argomenti partizionati i limiti seguenti:

* Le code e gli argomenti partizionati non supportano l'invio di messaggi che appartengono a sessioni diverse in una singola transazione.
* Il bus di servizio attualmente consente fino a 100 code o argomenti partizionati per spazio dei nomi. Ogni coda o argomento partizionato viene conteggiato ai fini della quota di 10.000 entità per spazio dei nomi (non si applica al livello Premium).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul partizionamento delle entità di messaggistica, vedere [Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio][AMQP 1.0 support for Service Bus partitioned queues and topics]. 

[Service Bus architecture]: service-bus-architecture.md
[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnablePartitioning
[TopicDescription.EnablePartitioning]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.topicdescription#Microsoft_ServiceBus_Messaging_TopicDescription_EnablePartitioning
[BrokeredMessage.SessionId]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId
[BrokeredMessage.PartitionKey]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_PartitionKey
[SessionId]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId
[PartitionKey]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_PartitionKey
[QueueDescription.RequiresDuplicateDetection]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection
[BrokeredMessage.MessageId]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[MessageId]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[MessagingFactorySettings.OperationTimeout]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[OperationTimeout]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[QueueDescription.ForwardTo]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md



<!--HONumber=Dec16_HO4-->


