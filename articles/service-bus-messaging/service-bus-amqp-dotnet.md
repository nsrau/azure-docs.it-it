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
    ms.date="10/03/2016"
    ms.author="sethm" />


# <a name="using-service-bus-from-.net-with-amqp-1.0"></a>Uso del bus di servizio da .NET con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>Download di Service Bus SDK

Il supporto per AMQP 1.0 è disponibile in Service Bus SDK versione 2.1 o successiva. È possibile assicurarsi di avere la versione più recente scaricando l'ultima versione del bus di servizio da [NuGet][].

## <a name="configuring-.net-applications-to-use-amqp-1.0"></a>Configurazione delle applicazioni .NET per l'uso di AMQP 1.0

Per impostazione predefinita, la libreria client .NET del bus di servizio comunica con il bus di servizio tramite un protocollo dedicato basato su SOAP. Per usare AMQP 1.0 anziché il protocollo predefinito, è necessaria una configurazione esplicita nella stringa di connessione del bus di servizio, come illustrato nella sezione successiva. A parte questa modifica, il codice dell'applicazione rimane invariato quando si usa AMQP 1.0.

Nella versione corrente alcune funzionalità API non sono supportate se si usa AMQP. Queste funzionalità non supportate sono elencate più avanti nella sezione [Funzionalità non supportate, restrizioni e differenze di comportamento](#unsupported-features-restrictions-and-behavioral-differences). Anche alcune impostazioni di configurazione avanzate assumono un significato differente quando si utilizza AMQP.

### <a name="configuration-using-app.config"></a>Configurazione mediante App.config

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

Dove `[namespace]` e `SharedAccessKey` vengono ottenuti dal [portale di Azure][]. Per altre informazioni, vedere [Introduzione alle code del bus di servizio][].

Quando si usa AMQP, aggiungere `;TransportType=Amqp` alla fine della stringa di connessione. Questa notazione segnala alla libreria client di effettuare la connessione al bus di servizio mediante AMQP 1.0.

## <a name="message-serialization"></a>Serializzazione dei messaggi

Quando si usa il protocollo predefinito, il comportamento di serializzazione predefinito della libreria client .NET consiste nell'usare il tipo [DataContractSerializer][] per serializzare un'istanza di [BrokeredMessage][] per il trasporto tra la libreria client e il servizio del bus di servizio. Quando si usa la modalità di trasporto AMQP, la libreria client usa il sistema di tipo AMQP per la serializzazione del [messaggio negoziato] in un messaggio AMQP. Questa serializzazione consente la ricezione e l'interpretazione dei messaggi da parte di un'applicazione ricevente che è potenzialmente in esecuzione in una piattaforma diversa, ad esempio un'applicazione Java che usa l'API JMS per accedere al bus di servizio.

Quando si crea un'istanza di [BrokeredMessage][], è possibile specificare al costruttore un oggetto .NET come parametro da usare come corpo del messaggio. Per gli oggetti che possono essere mappati a tipi primitivi AMQP, il corpo viene serializzato in tipi di dati AMQP. Se non è possibile eseguire direttamente il mapping dell'oggetto a un tipo primitivo, ovvero un tipo personalizzato definito dall'applicazione, l'oggetto viene serializzato tramite [DataContractSerializer][] e i byte serializzati vengono inviati in un messaggio di dati AMQP.

Per semplificare l'interoperabilità con client non .NET, usare solo tipi .NET che possono essere serializzati direttamente in tipi AMQP per il corpo del messaggio. La tabella seguente descrive in modo dettagliato questi tipi e il mapping corrispondente al sistema di tipi AMQP.

| Tipo di oggetto del corpo .NET          | Tipo AMQP mappato                          | Tipo di sezione del corpo AMQP                                                                                                                                    |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool                           | boolean                                   | Valore AMQP                                                                                                                                                |
| byte                           | ubyte                                     | Valore AMQP                                                                                                                                                |
| ushort                         | ushort                                    | Valore AMQP                                                                                                                                                |
| uint                           | uint                                      | Valore AMQP                                                                                                                                                |
| ulong                          | ulong                                     | Valore AMQP                                                                                                                                                |
| sbyte                          | byte                                      | Valore AMQP                                                                                                                                                |
| short                          | short                                     | Valore AMQP                                                                                                                                                |
| int                            | int                                       | Valore AMQP                                                                                                                                                |
| long                           | long                                      | Valore AMQP                                                                                                                                                |
| float                          | float                                     | Valore AMQP                                                                                                                                                |
| double                         | double                                    | Valore AMQP                                                                                                                                                |
| decimal                        | decimal128                                | Valore AMQP                                                                                                                                                |
| char                           | char                                      | Valore AMQP                                                                                                                                                |
| DateTime                       | timestamp                                 | Valore AMQP                                                                                                                                                |
| Guid                           | uuid                                      | Valore AMQP                                                                                                                                                |
| byte[]                         | binary                                    | Valore AMQP                                                                                                                                                |
| string                         | stringa                                    | Valore AMQP                                                                                                                                                |
| System.Collections.IList       | list                                      | Valore AMQP: la raccolta può includere solo gli elementi definiti in questa tabella.                                                             |
| System.Array                   | array                                     | Valore AMQP: la raccolta può includere solo gli elementi definiti in questa tabella.                                                             |
| System.Collections.IDictionary | map                                       | Valore AMQP: la raccolta può includere solo gli elementi definiti in questa tabella. Nota: sono supportate solo chiavi di tipo String.                        |
| Uri                            | Stringa descritta (vedere la tabella seguente) | Valore AMQP                                                                                                                                                |
| DateTimeOffset                 | Elemento Long descritto (vedere la tabella seguente)   | Valore AMQP                                                                                                                                                |
| TimeSpan                       | Elemento Long descritto (vedere la tabella seguente)         | Valore AMQP                                                                                                                                                |
| Flusso                         | binary                                    | Dati AMQP (possono essere multipli). Le sezioni Data contengono i byte non elaborati dall'oggetto Stream.                                                           |
| Altro oggetto                   | binary                                    | Dati AMQP (possono essere multipli). Contiene i dati binari serializzati dell'oggetto che usa DataContractSerializer o un serializzatore fornito dall'applicazione. |

| Tipo .NET      | Tipo descritto AMQP mappato                                                                                              | Note                   |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| Uri            | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>`                       | Uri.AbsoluteUri         |
| Datetimeoffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan       | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> `              | TimeSpan.Ticks          |

## <a name="unsupported-features,-restrictions,-and-behavioral-differences"></a>Funzionalità non supportate, restrizioni e differenze di comportamento

Le seguenti funzionalità dell'API .NET del bus di servizio non sono al momento supportate con AMQP:

-   Transazioni

-   Invio tramite destinazione del trasferimento

Esistono anche alcune piccole differenze di comportamento dell'API .NET del bus di servizio durante l'uso di AMQP, rispetto al protocollo predefinito:

-   La proprietà [OperationTimeout][] viene ignorata.

-   `MessageReceiver.Receive(TimeSpan.Zero)` viene implementato come `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

## <a name="controlling-amqp-protocol-settings"></a>Controllo delle impostazioni del protocollo AMQP

Le API .NET espongono diverse impostazioni per controllare il comportamento del protocollo AMQP:

-   **MessageReceiver.PrefetchCount**: controlla il credito iniziale applicato a un collegamento. Il valore predefinito è 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: controlla la dimensione massima del frame AMQP offerta durante la negoziazione in fase di apertura della connessione. Il valore predefinito è 65.536 byte.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: se i trasferimenti possono essere eseguiti in batch, questo valore determina il ritardo massimo per l'invio di disposizioni. Per impostazione predefinita, il valore viene ereditato dai mittenti/destinatari. Un singolo mittente/destinatario può sostituire il valore predefinito di 20 millisecondi.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: controlla se le connessioni AMQP vengono stabilite su una connessione SSL. Il valore predefinito è **true**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere i collegamenti seguenti:

- [Panoramica di AMQP per il bus di servizio]
- [Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio]
- [AMQP nel bus di servizio per Windows Server]

  [Introduzione alle code del bus di servizio]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[portale di Azure]: https://portal.azure.com
[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md
[Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP nel bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx


<!--HONumber=Oct16_HO2-->


