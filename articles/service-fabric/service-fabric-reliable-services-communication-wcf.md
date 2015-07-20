<properties
   pageTitle="Stack di comunicazione basato su WFC fornito dall'API di Reliable Services"
   description="Questo articolo descrive lo stack di comunicazione basato su WFC fornito dall'API di Reliable Services."
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
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# Stack di comunicazione basato su WCF per Reliable Services
Reliable Services Framework consente agli autori del servizio di decidere quale stack di comunicazione usare per il servizio. Gli autori del servizio possono collegare lo stack di comunicazione desiderato tramite l'oggetto `ICommunicationListener` restituito dal metodo [`CreateCommunicationListener`](../service-fabric-reliable-service-communication.md). Il framework offre un'implementazione basata su WCF dello stack di comunicazione per gli autori del servizio che desiderano usare la comunicazione basata su WCF.

## Listener di comunicazione WCF
L'implementazione specifica di WCF dell'oggetto `ICommunicationListener` è garantita dalla classe `WcfCommunicationListener`.

```csharp

public WcfCommunicationListener(
    Type communicationInterfaceType,
    Type communicationImplementationType);

protected override ICommunicationListener CreateCommunicationListener()
    {
        WcfCommunicationListener communicationListener = new WcfCommunicationListener(typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // which identifies the endpoint that the wcf servicehost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        };

        return communicationListener;
    }

```

## Scrittura di client per stack di comunicazione WCF
Per consentire ai client di scrittura di comunicare con i servizi tramite WCF, il framework fornisce l'oggetto `WcfClientCommunicationFactory`, ovvero l'implementazione specifica di WCF di [`ClientCommunicationFactoryBase`](../service-fabric-reliable-service-communication.md).

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

Il canale di comunicazione di WCF è accessibile dall'oggetto `WcfCommunicationClient` creato da `WcfCommunicationClientFactory`

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

Il codice client può usare `WcfCommunicationClientFactory` insieme a `ServicePartitionClient` per determinare l'endpoint di servizio e comunicare con il servizio.

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // donot retry Exception types


//
// Create a client for communicating with the calc service which has been created with
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;


```
 

<!---HONumber=July15_HO2-->