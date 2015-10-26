<properties
   pageTitle="Panoramica del modello di comunicazione con i servizi"
   description="Questo articolo descrive le nozioni di base relative al modello di comunicazione supportato dall'API di Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="08/27/2015"
   ms.author="bharatn@microsoft.com"/>

# Modello di comunicazione con i servizi

Il modello di programmazione Reliable Services consente agli autori dei servizi di specificare il meccanismo di comunicazione che desiderano usare per esporre gli endpoint di servizio e offre anche astrazioni che i client possono usare per individuare l'endpoint di servizio e comunicare con esso.

## Configurazione dello stack di comunicazione del servizio

L'API di Reliable Services consente agli autori dei servizi di inserire lo stack di comunicazione desiderato nel servizio implementando il metodo seguente:

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

L'interfaccia `ICommunicationListener` definisce l'interfaccia che deve essere implementata dal listener di comunicazione per un servizio.

```csharp

public interface ICommunicationListener
{
    void Initialize(ServiceInitializationParameters serviceInitializationParameters);

    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```
Gli endpoint necessari per il servizio sono descritti tramite il [manifesto del servizio](service-fabric-application-model.md) nella sezione Endpoint.

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

## Comunicazione tra i client e i servizi
L'API di Reliable Services offre le astrazioni seguenti che semplificano la scrittura di client per la comunicazione con i servizi.

## ServicePartitionResolver
La classe ServicePartitionResolver consente al client di determinare l'endpoint di un servizio di Service Fabric in fase di esecuzione.

> [AZURE.TIP]Nella terminologia di Service Fabric il processo di determinazione dell'endpoint di un servizio è denominato Risoluzione degli endpoint del servizio.

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

## CommunicationClientFactory
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
    protected override void AbortClient(MyCommunicationClient1 client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(ResolvedServiceEndpoint endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(ResolvedServiceEndpoint endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

## ServicePartitionClient
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
* [Stack di comunicazione predefinito fornito da Reliable Services Framework](service-fabric-reliable-services-communication-default.md)

* [Stack di comunicazione basato su WFC fornito da Reliable Services Framework](service-fabric-reliable-services-communication-wcf.md)

* [Scrittura di un servizio tramite l'API di Reliable Services che usa lo stack di comunicazione WebAPI](service-fabric-reliable-services-communication-webapi.md)
 

<!---HONumber=Oct15_HO3-->