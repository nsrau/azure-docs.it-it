<properties
   pageTitle="Panoramica sulla comunicazione di reliable service | Microsoft Azure"
   description="Panoramica sul modello di comunicazione Reliable Service, compresi listener di apertura sui servizi, endpoint di risoluzione e comunicazione tra servizi."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="vturecek@microsoft.com"/>

# Modello di comunicazione Reliable Service

L'infrastruttura di servizi intesta come piattaforma è completamente indipendente dalle comunicazioni tra servizi. È possibile usare tutti i protocolli e gli stack, da UDP a HTTP. Dipende dallo sviluppatore del servizio scegliere la modalità di comunicazione tra servizi. Il framework dell'applicazione Reliable Services fornisce alcuni stack di comunicazione predefiniti, nonché strumenti per implementare lo stack di comunicazione personalizzato, ad esempio astrazioni che i client possono usare per individuare e comunicare con gli endpoint del servizio.

## Impostazione della comunicazione tra servizi

L'API di Reliable Services usa una semplice interfaccia per la comunicazione tra servizi. Per aprire un endpoint del servizio, è sufficiente implementare questa interfaccia:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Aggiungere quindi l'implementazione del listener di comunicazione restituendola in un override del metodo della classe di base del servizio.

Per un servizio senza stato:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

Per un servizio con stato:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

In entrambi i casi restituire una raccolta di listener che consenta al servizio di usare facilmente più listener. Ad esempio è possibile disporre di un listener HTTP e un listener WebSocket separato. Ogni listener riceve un nome e la raccolta risultante di coppie nome:indirizzo è rappresentato come un oggetto JSON quando un client richiede gli indirizzi di ascolto per un'istanza o una partizione del servizio.

In un servizio senza stato l'override restituisce una raccolta di ServiceInstanceListener. ServiceInstanceListener contiene una funzione per creare ICommunicationListener e assegnargli un nome. Per i servizi con stato l'override restituisce una raccolta di ServiceReplicaListener. Questo è un comportamento leggermente diverso rispetto a quello del servizio senza stato, perché ServiceReplicaListener dispone di un'opzione per aprire ICommunicationListener nelle repliche secondarie. Ciò consente non solo di usare più listener di comunicazione in un servizio, ma anche specificare quale listener accetta le richieste nelle repliche secondarie e quelli che si limitano a rimanere in ascolto sulle repliche primarie.

Ad esempio il listener ServiceRemotingListener può gestire le chiamate RPC solo sulle repliche primarie, mentre un secondo listener personalizzato può accettare le richieste di lettura sulle repliche secondarie:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

Descrivere gli endpoint necessari per il servizio tramite il [manifesto del servizio](service-fabric-application-model.md) nella sezione Endpoints.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

Il listener di comunicazione può accedere alle risorse di endpoint allocate da `CodePackageActivationContext` in `ServiceInitializationParameters` e, quando viene aperto, può avviare l'ascolto delle richieste.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Le risorse di endpoint sono comuni all'intero pacchetto del servizio e vengono allocate da Service Fabric quando viene attivato il pacchetto del servizio. In questo modo tutte le repliche ospitate nella stessa istanza di ServiceHost condividono la stessa porta. Ciò significa che il listener di comunicazione deve supportare la condivisione delle porte. Il metodo consigliato per eseguire questa operazione consiste nell'uso dell'ID partizione e dell'ID replica/istanza da parte del listener di comunicazione durante la generazione dell'indirizzo di ascolto.

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
   replicaOrInstanceId = parameters.InstanceId;
}
else
{
   replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

Per una procedura dettagliata relativa alla scrittura di `ICommunicationListener`, vedere [Introduzione ai servizi delle API Web di Microsoft Azure Service Fabric con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md).

## Comunicazione tra i client e i servizi
L'API di Reliable Services offre le astrazioni seguenti che semplificano la scrittura di client per la comunicazione con i servizi.

### ServicePartitionResolver
Questa classe di utilità consente ai client di determinare l'endpoint di un servizio dell'infrastruttura di servizi in fase di esecuzione. Nella terminologia di Service Fabric il processo di determinazione dell'endpoint di un servizio è denominato Risoluzione degli endpoint del servizio.

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
   long partitionKey,
   CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
   CancellationToken cancellationToken);


```
> [AZURE.NOTE]FabricClient è l'oggetto usato per comunicare al cluster di Service Fabric per varie operazioni di gestione nel cluster di Service Fabric.

In genere non è necessario che il codice client funzioni direttamente con l'oggetto `ServicePartitionResolver`. Il codice viene creato e passato ad altre classi helper nell'API di Reliable Services, che usano internamente il resolver e consentono al client di comunicare con il servizio.

### CommunicationClientFactory
`ICommunicationClientFactory` definisce l'interfaccia di base implementata da una factory di client di comunicazione che produce client in grado di comunicare con un servizio di Service Fabric. L'implementazione di CommunicationClientFactory dipenderà dallo stack di comunicazione usato dal servizio di Service Fabric con cui il client desidera comunicare. L'API di Reliable Services include un oggetto CommunicationClientFactoryBase<TCommunicationClient> che garantisce un'implementazione di base dell'interfaccia `ICommunicationClientFactory` ed esegue attività comuni a tutti gli stack di comunicazione, ad esempio l'uso di un oggetto `ServicePartitionResolver` per determinare l'endpoint di servizio. I client in genere implementano la classe astratta CommunicationClientFactoryBase per gestire la logica specifica dello stack di comunicazione.

```csharp

protected CommunicationClientFactoryBase(
   ServicePartitionResolver servicePartitionResolver = null,
   IEnumerable<IExceptionHandler> exceptionHandlers = null,
   IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
   public MyCommunicationClient(MyCommunicationChannel communicationChannel)
   {
      this.CommunicationChannel = communicationChannel;
   }
   public MyCommunicationChannel CommunicationChannel { get; private set; }
   public ResolvedServicePartition ResolvedServicePartition;
}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

### ServicePartitionClient
`ServicePartitionClient` usa l'oggetto CommunicationClientFactory (e internamente ServicePartitionResolver) e consente di comunicare con il servizio tramite la gestione dei tentativi per le comunicazioni comuni e delle eccezioni temporanee di Service Fabric.

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

Un tipico modello d'uso sarà simile al seguente:

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(
   client =>
   {
      // Communicate with the service using the client.
      throw new NotImplementedException();
   },
   CancellationToken.None);


... other client code ...

```

## Passaggi successivi
* [Chiamata di procedura remota con i Reliable Services remoti](service-fabric-reliable-services-communication-remoting.md)

* [Web API con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Comunicazione di WCF con Reliable Services](service-fabric-reliable-services-communication-wcf.md)

 

<!---HONumber=Nov15_HO4-->