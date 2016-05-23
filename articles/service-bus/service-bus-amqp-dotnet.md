<properties 
    pageTitle="Bus di servizio con .NET e AMQP 1.0 | Microsoft Azure"
    description="Uso del bus di servizio da .NET con AMQP"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/06/2016"
    ms.author="sethm" />

# Uso del bus di servizio da .NET con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## Download di Service Bus SDK

Il supporto per AMQP 1.0 è disponibile in Service Bus SDK versione 2.1 o successiva. È possibile scaricare i codici più recenti del bus di servizio da [NuGet][].

## Configurazione delle applicazioni .NET per l'uso di AMQP 1.0

Per impostazione predefinita, la libreria client .NET del bus di servizio comunica con il bus di servizio tramite un protocollo dedicato basato su SOAP. Per usare AMQP 1.0 anziché il protocollo predefinito, è necessaria una configurazione esplicita nella stringa di connessione del bus di servizio, come illustrato nella sezione successiva. A parte questa modifica, il codice dell'applicazione rimane essenzialmente invariato durante l'uso di AMQP 1.0.

Nella versione corrente alcune funzionalità API non sono supportate se si usa AMQP. Queste funzionalità non supportate sono elencate più avanti nella sezione [Funzionalità non supportate, restrizioni e differenze di comportamento](#unsupported-features-restrictions-and-behavioral-differences). Anche alcune impostazioni di configurazione avanzate assumono un significato differente quando si utilizza AMQP.

### Configurazione mediante App.config

È consigliabile usare il file di configurazione App.config per l'archiviazione delle impostazioni delle applicazioni. Per le applicazioni del bus di servizio, è possibile usare App.config per archiviare le impostazioni del valore **ConnectionString** del bus di servizio. Ecco un file App.config di esempio:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <appSettings>
	        <add key="Microsoft.ServiceBus.ConnectionString"
	             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    </appSettings>
	</configuration>

Il valore dell'impostazione `Microsoft.ServiceBus.ConnectionString` corrisponde alla stringa di connessione del bus di servizio, usata per configurare la connessione con il bus di servizio. Il formato è il seguente:

	Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Dove `[namespace]` e `SharedAccessKey` vengono ottenuti dal [portale di Azure classico][]. Per altre informazioni, vedere [Come usare le code del bus di servizio][].

Quando si usa AMQP, aggiungere `;TransportType=Amqp` alla fine della stringa di connessione. Questa notazione segnala alla libreria client di effettuare la connessione al bus di servizio mediante AMQP 1.0.

## Serializzazione dei messaggi

Quando si usa il protocollo predefinito, il comportamento di serializzazione predefinito della libreria client .NET consiste nell'usare il tipo [DataContractSerializer][] per serializzare un'istanza di [BrokeredMessage][] per il trasporto tra la libreria client e il servizio del bus di servizio. Quando si usa la modalità di trasporto AMQP, la libreria client usa il sistema di tipi AMQP per la serializzazione del [messaggio negoziato][BrokeredMessage] in un messaggio AMQP. Questa serializzazione consente la ricezione e l'interpretazione dei messaggi da parte di un'applicazione ricevente che è potenzialmente in esecuzione in una piattaforma diversa, ad esempio un'applicazione Java che usa l'API JMS per accedere al bus di servizio.

Quando si crea un'istanza di [BrokeredMessage][], è possibile fornire al costruttore un oggetto .NET come parametro da usare come corpo del messaggio. Per gli oggetti che possono essere mappati a tipi primitivi AMQP, il corpo viene serializzato in tipi di dati AMQP. Se non è possibile eseguire direttamente il mapping dell'oggetto a un tipo primitivo, ovvero un tipo personalizzato definito dall'applicazione, l'oggetto verrà serializzato mediante [DataContractSerializer][] e i byte serializzati verranno inviati in un messaggio di dati AMQP.

Per semplificare l'interoperabilità con client non .NET, usare solo tipi .NET che possono essere serializzati direttamente in tipi AMQP per il corpo del messaggio. La tabella seguente descrive in modo dettagliato questi tipi e il mapping corrispondente al sistema di tipi AMQP.

| Tipo di oggetto del corpo .NET | Tipo AMQP mappato | Tipo di sezione del corpo AMQP |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool | boolean | Valore AMQP |
| byte | ubyte | Valore AMQP |
| ushort | ushort | Valore AMQP |
| uint | uint | Valore AMQP |
| ulong | ulong | Valore AMQP |
| sbyte | byte | Valore AMQP |
| short | short | Valore AMQP |
| int | int | Valore AMQP |
| long | long | Valore AMQP |
| float | float | Valore AMQP |
| double | double | Valore AMQP |
| decimal | decimal128 | Valore AMQP |
| char | char | Valore AMQP |
| DateTime | timestamp | Valore AMQP |
| Guid | uuid | Valore AMQP |
| byte | binary | Valore AMQP |
| string | string | Valore AMQP |
| System.Collections.IList | list | Valore AMQP: la raccolta può includere solo gli elementi definiti in questa tabella. |
| System.Array | array | Valore AMQP: la raccolta può includere solo gli elementi definiti in questa tabella. |
| System.Collections.IDictionary | map | Valore AMQP: la raccolta può includere solo gli elementi definiti in questa tabella. Nota: sono supportate solo chiavi di tipo String. |
| Uri | Stringa descritta (vedere la tabella seguente) | Valore AMQP |
| DateTimeOffset | Elemento Long descritto (vedere la tabella seguente) | Valore AMQP |
| TimeSpan | Elemento Long descritto (vedere la tabella seguente) | Valore AMQP |
| Stream | binary | Dati AMQP (possono essere multipli). Le sezioni Data contengono i byte non elaborati dall'oggetto Stream. |
| Altro oggetto | binary | Dati AMQP (possono essere multipli). Contiene i dati binari serializzati dell'oggetto che usa DataContractSerializer o un serializzatore fornito dall'applicazione. |

| Tipo .NET | Tipo descritto AMQP mappato | Note |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| Uri | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` | Uri.AbsoluteUri |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` | TimeSpan.Ticks |

## Funzionalità non supportate, restrizioni e differenze di comportamento

Le seguenti funzionalità dell'API .NET del bus di servizio non sono al momento supportate con AMQP:

-   Transazioni.

-   Invio tramite destinazione del trasferimento.

-   Ricezione in base al numero di sequenza del messaggio.

-   Esplorazione di messaggi e sessioni.

-   Stato della sessione.

-   API basate su batch.

-   Ricezione con scalabilità orizzontale

-   Manipolazione in runtime delle regole di sottoscrizione.

-   Rinnovo del blocco della sessione.

In particolare, le API seguenti non sono attualmente supportate quando si usa AMQP:

- [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession][]
- [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)][]

- [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})][]

- [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch][]

- [Microsoft.ServiceBus.Messaging.QueueClient.Peek][]
- [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch][]

- [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})][]

- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)][]

- [Microsoft.ServiceBus.Messaging.MessageSession.GetState][]
- [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)][]
- [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock][]

- [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock][]

Esistono anche alcune piccole differenze di comportamento dell'API .NET del bus di servizio durante l'uso di AMQP, rispetto al protocollo predefinito:

-   La proprietà [OperationTimeout][] verrà ignorata.

-   `MessageReceiver.Receive(TimeSpan.Zero)` viene implementato come `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

## Controllo delle impostazioni del protocollo AMQP

Le API .NET espongono diverse impostazioni per controllare il comportamento del protocollo AMQP:

-   **MessageReceiver.PrefetchCount**: controlla il credito iniziale applicato a un collegamento. Il valore predefinito è 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: controlla le dimensioni massime del frame AMQP offerte durante la negoziazione in fase di apertura della connessione. Il valore predefinito è 65.536 byte.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: se i trasferimenti possono essere eseguiti in batch, questo valore determina il ritardo massimo per l'invio di disposizioni. Per impostazione predefinita, il valore viene ereditato dai mittenti/destinatari. Un singolo mittente/destinatario può sostituire il valore predefinito di 20 millisecondi.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: controlla se le connessioni AMQP vengono stabilite su una connessione SSL. Il valore predefinito è **true**.

## Passaggi successivi

Per altre informazioni, vedere i collegamenti seguenti:

- [Panoramica di AMQP per il bus di servizio]
- [Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio]
- [AMQP nel bus di servizio per Windows Server]

  [Come usare le code del bus di servizio]: service-bus-dotnet-how-to-use-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.sendbatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)]: https://msdn.microsoft.com/library/azure/hh322665.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.receivebatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.completebatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek]: https://msdn.microsoft.com/library/azure/jj908731.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.QueueClient.Peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx
  [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.sendbatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)]: https://msdn.microsoft.com/library/azure/hh293110.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.receivebatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.completebatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peek.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.addrule.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.removerule.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.GetState]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx
  [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[portale di Azure classico]: http://manage.windowsazure.com
[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md
[Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP nel bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

<!---HONumber=AcomDC_0511_2016-->