---
title: Creare code e argomenti partizionati del bus di servizio di Azure | Microsoft Docs
description: Descrive come partizionare code e argomenti del bus di servizio usando più broker messaggi.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2019
ms.author: aschhab
ms.openlocfilehash: 699581c7ccd3f36da0cd0c1def623607b7c0a13b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589667"
---
# <a name="partitioned-queues-and-topics"></a>Code e argomenti partizionati

Il bus di servizio di Azure usa più broker messaggi per elaborare i messaggi e più archivi di messaggistica per archiviarli. Una coda o un argomento convenzionale è gestito da un singolo broker messaggi e archiviato in un archivio di messaggistica. Le *partizioni* del bus di servizio consentono il partizionamento di code e argomenti, o *entità di messaggistica*, tra più broker messaggi e archivi di messaggistica. Il partizionamento indica che la velocità effettiva complessiva di un'entità partizionata non è più limitata dalle prestazioni di un singolo broker messaggi o archivio di messaggistica. Inoltre, un'interruzione temporanea dell'alimentazione di un archivio di messaggistica non determina la mancanza di disponibilità di una coda o di un argomento partizionato. Le code e gli argomenti partizionati possono contenere tutte le funzionalità avanzate del bus di servizio, ad esempio il supporto delle transazioni e delle sessioni.

> [!NOTE]
> Il partizionamento è disponibile alla creazione dell'entità per tutte le code e gli argomenti in SKU di base o standard. Non è disponibile per lo SKU di messaggistica Premium, ma tutte le entità partizionate esistenti negli spazi dei nomi Premium continueranno a funzionare come previsto.
 
Non è possibile modificare l'opzione di partizionamento su una coda o un argomento esistente. L'opzione può essere impostata solo in fase di creazione dell'entità.

## <a name="how-it-works"></a>Funzionamento

Ogni coda o argomento partizionato è costituito da più partizioni. Ogni partizione viene archiviato in un archivio di messaggistica differente e gestito da un broker di messaggi diversi. Quando viene inviato un messaggio a una coda o argomento partizionato, il Bus di servizio assegna il messaggio a una delle partizioni. La selezione viene eseguita in modo casuale dal bus di servizio o tramite una chiave di partizione che può essere specificata dal mittente.

Quando un client desidera ricevere un messaggio da una coda partizionata o da una sottoscrizione a un argomento partizionato, il Bus di servizio esegue query tutte le partizioni per i messaggi, quindi restituisce il primo messaggio ottenuto dagli archivi di messaggistica al destinatario. Il bus di servizio memorizza nella cache gli altri messaggi e li restituisce quando riceve altre richieste. Un client destinatario non è a conoscenza del partizionamento; il comportamento verso il client di una coda o un argomento partizionato (ad esempio lettura, completamento, rinvio, non recapitabilità, prelettura) è identico a quello di un'entità normale.

I messaggi a una coda o a un argomento partizionato non presentano costi aggiuntivi, né in invio né in ricezione.

## <a name="enable-partitioning"></a>Abilitare il partizionamento

Per usare le code e gli argomenti partizionati con il bus di servizio di Azure, usare Azure SDK 2.2 o versione successiva oppure specificare `api-version=2013-10` o una versione successiva nelle richieste HTTP.

### <a name="standard"></a>Standard

A livello di messaggistica Standard è possibile creare code e argomenti del bus di servizio in dimensioni di 1, 2, 3, 4 o 5 GB (il valore predefinito è 1 GB). Con il partizionamento abilitato, il Bus di servizio crea 16 copie (16 partizioni) dell'entità, ognuna delle stesse dimensioni specificata. Di conseguenza, se si crea una coda con dimensioni pari a 5 GB, con 16 partizioni le dimensioni massime della coda diventano di 80 GB (5 \* 16). È possibile vedere le dimensioni massime della coda o dell'argomento partizionato esaminando la voce corrispondente nel [portale di Azure][Azure portal], nel pannello **Panoramica** relativo all'entità.

### <a name="premium"></a>Premium

In uno spazio dei nomi livello Premium, entità di partizionamento non sono supportate. È tuttavia possibile creare code e argomenti del bus di servizio in dimensioni di 1, 2, 3, 4, 5, 10, 20, 40 o 80 GB (il valore predefinito è 1 GB). È possibile vedere le dimensioni della coda o dell'argomento esaminando la voce corrispondente nel [portale di Azure][Azure portal], nel pannello **Panoramica** relativo all'entità.

### <a name="create-a-partitioned-entity"></a>Creare una tabella partizionata

Sono disponibili vari modi per creare una coda o un argomento partizionato. Quando si crea la coda o l'argomento dalla propria applicazione, è possibile abilitare il partizionamento per la coda o l'argomento impostando, rispettivamente, la proprietà [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] o [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] su **true**. Queste proprietà devono essere impostate al momento della creazione della coda o dell'argomento e sono disponibili solo nella versione precedente della libreria [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Come detto in precedenza, non è possibile modificare queste proprietà in una coda o in un argomento esistente. Ad esempio: 

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

È possibile, in alternativa, creare una coda o un argomento partizionato nel [portale di Azure][Azure portal]. Quando si crea una coda o un argomento nel portale, l'opzione **Abilita partizionamento** nella finestra di dialogo **Crea** della coda o dell'argomento è selezionata per impostazione predefinita. L'opzione può essere disabilitata solo in un'entità di livello Standard. Nel livello Premium il partizionamento non è supportato e la casella di controllo è disattivata. 

## <a name="use-of-partition-keys"></a>Uso delle chiavi di partizione

Quando un messaggio viene accodato in una coda o in un argomento partizionato, il bus di servizio controlla la presenza di una chiave di partizione. Se ne trova una, seleziona la partizione in base a tale chiave. Se non trova una chiave di partizione, seleziona la partizione in base a un algoritmo interno.

### <a name="using-a-partition-key"></a>Uso di una chiave di partizione

Alcuni scenari, ad esempio le sessioni o le transazioni, richiedono i messaggi da archiviare in una partizione specifica. Tutti questi scenari richiedono l'uso di una chiave di partizione. Tutti i messaggi che usano la stessa chiave di partizione vengono assegnati alla stessa partizione. Se la partizione è temporaneamente non disponibile, il Bus di servizio restituisce un errore.

In base allo scenario vengono usate come chiave di partizione proprietà dei messaggi diverse:

**SessionId**: Se per un messaggio è impostata la proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), il bus di servizio usa **SessionID** come chiave di partizione. In questo modo, tutti i messaggi appartenenti alla stessa sessione vengono gestiti dallo stesso broker messaggi. Le sessioni consentono al bus di servizio di garantire l'ordinamento dei messaggi così come la coerenza degli stati della sessione.

**PartitionKey**: Se per un messaggio è impostata la proprietà [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), ma non la proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), il bus di servizio usa il valore della proprietà [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) come chiave di partizione. Se per il messaggio sono impostate le proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) e [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), queste devono avere un valore identico. Se la proprietà [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) è impostata su un valore diverso rispetto a quello della proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), il bus di servizio restituisce un'eccezione di operazione non valida. La proprietà [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) deve essere usata se un mittente invia messaggi transazionali che non sono in grado di riconoscere le sessioni. La chiave di partizione assicura che tutti i messaggi inviati all'interno di una transazione vengano gestiti dallo stesso broker di messaggistica.

**MessageId**: Se per la coda o l'argomento la proprietà [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) è impostata su **true** e le proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) o [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) non sono impostate, la proprietà [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) verrà usata come chiave di partizione. (Le librerie di Microsoft .NET e AMQP assegnano automaticamente un ID messaggio, se questo non viene assegnato dall'applicazione mittente) In questo caso tutte le copie dello stesso messaggio vengono gestite dallo stesso broker messaggi. Questo ID consente al bus di servizio di rilevare ed eliminare i messaggi duplicati. Se la proprietà [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) non è impostata su **true**, il bus di servizio non considera la proprietà [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) come chiave di partizione.

### <a name="not-using-a-partition-key"></a>Senza l'uso di una chiave di partizione

In assenza di una chiave di partizione, del Bus di servizio distribuisce messaggi in modo round robin per tutte le partizioni della coda partizionata o dell'argomento. Se la partizione selezionata non è disponibile, il Bus di servizio assegna il messaggio a una partizione diversa. In questo modo, l'operazione di invio viene completata correttamente indipendentemente dalla disponibilità o meno di un archivio di messaggistica. Non si otterrà tuttavia l'ordinamento garantito fornito da una chiave di partizione.

Per un'analisi più approfondita del compromesso tra disponibilità (nessuna chiave di partizione) e coerenza (uso di una chiave di partizione), vedere [questo articolo](../event-hubs/event-hubs-availability-and-consistency.md). Queste informazioni si applicano ugualmente alle entità del bus di servizio partizionate.

Per concedere al Bus di servizio tempo sufficiente per accodare il messaggio in una partizione diversa, il [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) valore specificato dal client che invia il messaggio deve essere maggiore di 15 secondi. È consigliabile impostare la proprietà [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) sul valore predefinito di 60 secondi.

Una chiave di partizione "associa" un messaggio a una partizione specifica. Se l'archivio di messaggistica che contiene la partizione non è disponibile, il Bus di servizio restituisce un errore. In assenza di una chiave di partizione, del Bus di servizio può scegliere una partizione diversa e l'operazione ha esito positivo. È quindi consigliabile non specificare una chiave di partizione, a meno che non sia necessario.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Argomenti avanzati: usare le transazioni con entità partizionate

I messaggi inviati come parte di una transazione devono specificare una chiave di partizione. La chiave può essere uno dei seguenti valori: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) o [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Tutti i messaggi che vengono inviati come parte della stessa transazione devono specificare la stessa chiave di partizione. Se si prova a inviare un messaggio senza una chiave di partizione in una transazione, il bus di servizio restituisce un'eccezione di operazione non valida. Se si prova a inviare più messaggi con chiavi di partizione diverse nella stessa transazione, il bus di servizio restituisce un'eccezione di operazione non valida. Ad esempio: 

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

Se le proprietà che servono come chiave di partizione sono impostate, il Bus di servizio aggiunge il messaggio a una partizione specifica. Questo comportamento si verifica indipendentemente dall'uso di una transazione. È consigliabile non specificare una chiave di partizione, a meno che non sia necessario.

## <a name="using-sessions-with-partitioned-entities"></a>Uso delle sessioni con entità partizionate

Per inviare un messaggio transazionale a un argomento o una coda in grado di riconoscere la sessione, per il messaggio deve essere impostata la proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid). Se è specificata anche la proprietà [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), quest'ultima deve avere un valore identico a quello della proprietà [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid). In caso contrario, il bus di servizio restituisce un'eccezione di operazione non valida.

A differenza delle code o degli argomenti normali (non partizionati), non è possibile usare una singola transazione per inviare più messaggi a sessioni diverse. Se è stato effettuato un tentativo, il bus di servizio restituirà un'eccezione di operazione non valida, Ad esempio: 

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Inoltro automatico dei messaggi con entità partizionate

Il bus di servizio supporta l'inoltro automatico dei messaggi da, a o tra entità partizionate. Per abilitare l'inoltro automatico dei messaggi, impostare la proprietà [QueueDescription.ForwardTo][QueueDescription.ForwardTo] nella coda o nella sottoscrizione di origine. Se il messaggio specifica una chiave di partizione ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) o [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)), questa viene usata per l'entità di destinazione.

## <a name="considerations-and-guidelines"></a>Considerazioni e indicazioni
* **Funzionalità a coerenza elevata**: Se un'entità Usa funzionalità quali sessioni, il rilevamento dei duplicati o controllo esplicito della chiave di partizionamento, le operazioni di messaggistica vengano sempre indirizzate alla partizione specifica. Se una delle partizioni esperienza traffico elevato o nell'archivio sottostante non è integro, esito negativo di queste operazioni e la disponibilità risulta ridotto. La coerenza è complessivamente molto più elevata rispetto alle entità non partizionate. I problemi si verificano solo per un sottoinsieme del traffico, invece che per tutto il traffico. Per altre informazioni, vedere la [discussione relativa a disponibilità e coerenza](../event-hubs/event-hubs-availability-and-consistency.md).
* **Gestione**: Operazioni quali la creazione, aggiornamento ed eliminazione devono essere eseguite in tutte le partizioni dell'entità. Se tutte le partizioni non sono integra, potrebbero verificarsi errori per queste operazioni. Per l'operazione Get, informazioni quali il numero di messaggi devono essere aggregate di tutte le partizioni. Se tutte le partizioni non sono integra, lo stato di disponibilità di entità risulta limitato.
* **Scenari di messaggi a basso volume**: Per questi scenari, in particolare se si usa il protocollo HTTP, potrebbe essere necessario eseguire più operazioni di ricezione per ottenere tutti i messaggi. Per le richieste di ricezione, il front-end esegue un'operazione di ricezione di tutte le partizioni e memorizza nella cache tutte le risposte ricevute. Una richiesta di ricezione successiva sulla stessa connessione sarebbe avvantaggiata dalla memorizzazione nella cache e le latenze di ricezione saranno minori. Se tuttavia sono presenti più connessioni o si usa HTTP, ciò crea una nuova connessione per ogni richiesta. Non è quindi possibile assicurare che la richiesta venga ricevuta sullo stesso nodo. Se tutti i messaggi esistenti sono bloccati e memorizzati nella cache in un altro front-end, l'operazione di ricezione restituisce **null**. I messaggi raggiungeranno infine la scadenza e verranno ricevuti di nuovo. È consigliabile usare la connessione keep-alive HTTP.
* **Esaminare/visualizzare messaggi**: Disponibile solo nella versione precedente della libreria [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) non restituisce sempre il numero di messaggi specificato nella proprietà [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount). a causa di due motivi comuni. Il primo motivo consiste nel fatto che le dimensioni aggregate della raccolta di messaggi superano le dimensioni massime pari a 256 kB. L'altro motivo dipende dal fatto che, se la [proprietà EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) della coda o dell'argomento è impostata su **true**, è possibile che una partizione non includa una quantità di messaggi sufficiente per completare il numero di messaggi richiesto. In genere, se un'applicazione vuole ricevere un numero specifico di messaggi, deve chiamare ripetutamente [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) fino a ottenere tale numero di messaggi o fino a quando non siano più presenti messaggi di cui visualizzare l'anteprima. Per altre informazioni, inclusi esempi di codice, vedere la documentazione relativa all'API [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) o [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch).

## <a name="latest-added-features"></a>Funzionalità aggiunte di recente

* L'aggiunta o la rimozione di una regola è ora supportata con le entità partizionate. A differenza delle entità non partizionate, queste operazioni non sono supportate nelle transazioni. 
* AMQP è ora supportato per l'invio e la ricezione di messaggi verso e da un'entità partizionata.
* AMQP è ora supportato per le operazioni seguenti: [Invio batch](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [ricezione batch](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [ricezione per numero di sequenza](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [visualizzazione](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [rinnovo del blocco](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [pianificazione del messaggio](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [annullamento del messaggio pianificato](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [aggiunta di una nuova regola](/dotnet/api/microsoft.azure.servicebus.ruledescription), [rimozione di una regola](/dotnet/api/microsoft.azure.servicebus.ruledescription), [blocco del rinnovo della sessione](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [impostazione dello stato della sessione](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [recupero dello stato della sessione](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate) ed [enumerazione delle sessioni](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Limiti delle entità partizionate

Attualmente il bus di servizio impone alle code o agli argomenti partizionati i limiti seguenti:

* Code e argomenti partizionati non sono supportati nella messaggistica Premium. Le sessioni sono supportate nel livello Premium mediante SessionId. 
* Le code e gli argomenti partizionati non supportano l'invio di messaggi che appartengono a sessioni diverse in una singola transazione.
* Il bus di servizio attualmente consente fino a 100 code o argomenti partizionati per spazio dei nomi. Ogni coda o argomento partizionato viene conteggiato ai fini della quota di 10.000 entità per spazio dei nomi (non si applica al livello Premium).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui concetti di base relativi alle specifiche di messaggistica di AMQP 1.0 consultare la [guida al protocollo AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
