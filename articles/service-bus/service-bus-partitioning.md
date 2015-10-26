<properties 
   pageTitle="Entità di messaggistica partizionate | Microsoft Azure"
   description="Descrive come partizionare le entità di messaggistica usando più broker messaggi."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/18/2015"
   ms.author="sethm" />

# Entità di messaggistica partizionate

Il bus di servizio di Azure usa più broker messaggi per elaborare i messaggi e più archivi di messaggistica per archiviarli. Una coda o un argomento convenzionale è gestito da un singolo broker messaggi e archiviato in un archivio di messaggistica. Il bus di servizio consente anche il partizionamento delle code o degli argomenti tra più broker messaggi e archivi di messaggistica. Questo significa che la velocità effettiva complessiva di una coda o di un argomento partizionato non è più limitata dalle prestazioni di un singolo broker messaggi o archivio di messaggistica. Inoltre, un'interruzione temporanea dell'alimentazione di un archivio di messaggistica non determina la mancanza di disponibilità di una coda o di un argomento partizionato. Le code e gli argomenti partizionati possono contenere tutte le funzionalità avanzate del bus di servizio, ad esempio il supporto delle transazioni e delle sessioni.

Per altre informazioni sugli elementi interni del bus di servizio, vedere l'argomento [Architettura del bus di servizio][].

## Code e argomenti partizionati

Ogni coda o argomento partizionato è costituito da più frammenti. Ogni frammento viene memorizzato in un archivio di messaggistica differente e gestito da un broker messaggi diverso. Quando un messaggio viene inviato a una coda o a un argomento partizionato, il bus di servizio assegna il messaggio a uno dei frammenti. La selezione viene effettuata in modo casuale dal bus di servizio o in base a una chiave di partizione che può essere specificata dal mittente.

Quando un client vuole ricevere un messaggio da una coda, da una sottoscrizione o da un argomento partizionato, il bus di servizio esegue query su tutti i frammenti dei messaggi, quindi restituisce al ricevitore il primo messaggio restituito dagli archivi di messaggistica. Il bus di servizio memorizza nella cache gli altri messaggi e li restituisce quando riceve altre richieste. Un client destinatario non è a conoscenza del partizionamento; il comportamento verso il client di una coda o un argomento partizionato (ad esempio lettura, completamento, rinvio, non recapitabilità, prelettura) è identico a quello di un'entità normale.

I messaggi a una coda o a un argomento partizionato non presentano costi aggiuntivi, né in invio né in ricezione.

## Abilitare il partizionamento

Per usare le code e gli argomenti partizionati con il bus di servizio di Microsoft Azure, è necessario usare Azure SDK 2.2 o versione successiva oppure specificare `api-version=2013-10` nelle richieste HTTP.

È possibile creare code e argomenti del bus di servizio in dimensioni di 1, 2, 3, 4 o 5 GB (il valore predefinito è di 1 GB). Con il partizionamento abilitato, il bus di servizio crea 16 partizioni per ogni GB specificato. Di conseguenza, se si crea una coda con dimensioni pari a 5 GB, con 16 partizioni le dimensioni massime della coda diventano di 80 GB (5 * 16). È possibile visualizzare le dimensioni massime della coda o dell'argomento partizionato esaminando la voce corrispondente nel [portale di Azure][].

Sono disponibili vari modi per creare una coda o un argomento partizionato. Quando si crea la coda o l'argomento dalla propria applicazione, è possibile abilitare il partizionamento per la coda o l'argomento impostando, rispettivamente, la proprietà [QueueDescription.EnablePartitioning][] o [TopicDescription.EnablePartitioning][] su **true**. Queste proprietà devono essere impostate al momento della creazione della coda o dell'argomento. Non è possibile modificare queste proprietà in una coda o in un argomento esistente. Ad esempio:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

In alternativa, è possibile creare una coda o un argomento partizionato in Visual Studio o nel [portale di Azure][]. Quando si crea una nuova coda o un nuovo argomento nel portale, selezionare l'opzione **Abilita partizionamento** nella scheda **Configura** della finestra della coda o dell'argomento. In Visual Studio fare clic sulla casella di controllo **Abilita partizionamento** nella finestra di dialogo **Nuova coda** o **Nuovo argomento**.

## Uso delle chiavi di partizione

Quando un messaggio viene accodato in una coda o in un argomento partizionato, il bus di servizio controlla la presenza di una chiave di partizione. Se ne trova una, seleziona il frammento in base a tale chiave. Se invece non trova alcuna chiave di partizione, seleziona il frammento in base a un algoritmo interno.

### Uso di una chiave di partizione

In alcuni scenari, ad esempio le sessioni o le transazioni, i messaggi devono essere archiviati in un frammento specifico. Tutti questi scenari richiedono l'uso di una chiave di partizione. Tutti i messaggi che usano la stessa chiave di partizione vengono assegnati allo stesso frammento. Se il frammento è temporaneamente non disponibile, il bus di servizio restituisce un errore.

In base allo scenario vengono usate come chiave di partizione proprietà dei messaggi diverse:

**SessionId**: se per un messaggio è impostata la proprietà [BrokeredMessage.SessionId][], il bus di servizio usa questa proprietà come chiave di partizione. In questo modo, tutti i messaggi appartenenti alla stessa sessione vengono gestiti dallo stesso broker messaggi. Questo consente a l bus di servizio di garantire l'ordinamento dei messaggi così come la coerenza degli stati della sessione.

**PartitionKey**: se per un messaggio è impostata la proprietà [BrokeredMessage.PartitionKey][], ma non la proprietà [BrokeredMessage.SessionId][], il bus di servizio usa la proprietà [PartitionKey][] come chiave di partizione. Se per il messaggio sono impostate le proprietà [SessionId][] e [PartitionKey][], queste devono avere un valore identico. Se la proprietà [PartitionKey][] è impostata su un valore diverso rispetto a quello della proprietà [SessionId][], il bus di servizio restituisce un'eccezione **InvalidOperationException**. La proprietà [PartitionKey][] deve essere usata se un mittente invia messaggi transazionali che non sono in grado di riconoscere le sessioni. La chiave di partizione assicura che tutti i messaggi inviati all'interno di una transazione vengano gestiti dallo stesso broker di messaggistica.

**MessageId**: se per la coda o l'argomento la proprietà [QueueDescription.RequiresDuplicateDetection][] è impostata su **true** e le proprietà [BrokeredMessage.SessionId][] o [BrokeredMessage.PartitionKey][] non sono impostate, la proprietà [BrokeredMessage.MessageId][] verrà usata come chiave di partizione (tenere presente che le librerie di Microsoft .NET e AMQP assegnano automaticamente un ID messaggio, se questo non viene assegnato dall'applicazione mittente). In questo caso tutte le copie dello stesso messaggio vengono gestite dallo stesso broker messaggi. Questo consente al bus di servizio di rilevare ed eliminare i messaggi duplicati. Se la proprietà [QueueDescription.RequiresDuplicateDetection][] non è impostata su **true**, il bus di servizio non considera la proprietà [MessageId][] come chiave di partizione.

### Senza l'uso di una chiave di partizione

In assenza di una chiave di partizione, il bus di servizio distribuisce, con un'alternanza di tipo round robin, i messaggi ai frammenti della coda o dell'argomento partizionato. Se il frammento scelto non è disponibile, il bus di servizio assegna il messaggio a un altro frammento. In questo modo, l'operazione di invio viene completata correttamente indipendentemente dalla disponibilità o meno di un archivio di messaggistica.

Per concedere al bus di servizio tempo sufficiente per l'accodamento del messaggio in un frammento diverso, il valore [MessagingFactorySettings.OperationTimeout][] specificato dal client che invia il messaggio deve essere maggiore di 15 secondi. È consigliabile impostare la proprietà [OperationTimeout][] sul valore predefinito di 60 secondi.

Tenere presente che una chiave di partizione "aggiunge" un messaggio a un frammento specifico. Se l'archivio di messaggistica contenente questo frammento non è disponibile, il bus di servizio restituisce un errore. In assenza di una chiave di partizione, il bus di servizio può scegliere un frammento diverso e l'operazione verrà completata correttamente. È quindi consigliabile non specificare una chiave di partizione, a meno che non sia necessario.

## Argomenti avanzati: usare le transazioni con entità partizionate

I messaggi inviati come parte di una transazione devono specificare una chiave di partizione. Questa può essere una delle seguenti proprietà: [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][] o [BrokeredMessage.MessageId][]. Tutti i messaggi che vengono inviati come parte della stessa transazione devono specificare la stessa chiave di partizione. Se si prova a inviare un messaggio senza una chiave di partizione all'interno di una transazione, il bus di servizio restituisce un'eccezione **InvalidOperationException**. Se si prova a inviare più messaggi con chiavi di partizione diverse all'interno della stessa transazione, il bus di servizio restituisce un'eccezione **InvalidOperationException**. Ad esempio:

```
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

## Usare le sessioni con entità partizionate

Per inviare un messaggio transazionale a un argomento o una coda in grado di riconoscere la sessione, il messaggio deve avere la proprietà [BrokeredMessage.SessionId][] impostata. Se è specificata anche la proprietà [BrokeredMessage.PartitionKey][], questa deve avere un valore identico a quello della proprietà [SessionId][]. Se i valori sono diversi, il bus di servizio restituisce un'eccezione **InvalidOperationException**.

A differenza delle code o degli argomenti normali (non partizionati), non è possibile usare una singola transazione per inviare più messaggi a sessioni diverse. Se si prova a eseguire questa operazione, il bus di servizio restituisce un'eccezione **InvalidOperationException**. Ad esempio:

```
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

## Inoltro automatico dei messaggi con entità partizionate

Il bus di servizio supporta l'inoltro automatico dei messaggi da, a o tra entità partizionate. Per abilitare l'inoltro automatico dei messaggi, impostare la proprietà [QueueDescription.ForwardTo][] nella coda o nella sottoscrizione di origine. Se il messaggio specifica una chiave di partizione ([SessionId][], [PartitionKey][] o [MessageId][]), questa viene usata per l'entità di destinazione.

## Limiti delle entità partizionate

Nell'implementazione corrente il bus di servizio impone alle code o agli argomenti partizionati i limiti seguenti:

-   Le code e gli argomenti partizionati sono disponibili tramite SBMP o HTTP/HTTPS, nonché AMQP.

-   Le code e gli argomenti partizionati non supportano l'invio di messaggi che appartengono a sessioni diverse in una singola transazione.

-   Il bus di servizio attualmente consente fino a 100 code o argomenti partizionati per spazio dei nomi. Ogni coda o argomento partizionato viene conteggiato ai fini della quota di 10.000 entità per spazio dei nomi.

-   Le code e gli argomenti partizionati non sono supportati nelle versioni 1.0 e 1.1 del bus di servizio per Windows Server.

## Passaggi successivi

Per altre informazioni sul partizionamento delle entità di messaggistica, vedere la discussione relativa al supporto di AMQP 1.0 per le code e gli argomenti partizionati del bus di servizio (presto disponibile).

  [Architettura del bus di servizio]: service-bus-architecture.md
  [portale di Azure]: http://manage.windowsazure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-entities-amqp-overview.md

<!---HONumber=Oct15_HO3-->