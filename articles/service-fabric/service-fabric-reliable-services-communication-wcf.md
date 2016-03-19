<properties
   pageTitle="Stack di comunicazione WCF di Reliable Services | Microsoft Azure"
   description="Lo stack di comunicazione WCF integrato in Service Fabric consente la comunicazione client-servizio di WCF per Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="bharatn@microsoft.com"/>

# Stack di comunicazione basato su WCF per Reliable Services
Il framework di Reliable Services consente agli autori del servizio di scegliere lo stack di comunicazione da usare per il servizio, nonché di eseguire il plug-in dello stack di comunicazione scelto tramite l'oggetto **ICommunicationListener** restituito dai metodi [CreateServiceReplicaListeners o CreateServiceInstanceListeners](service-fabric-reliable-service-communication.md). Il framework offre un'implementazione dello stack di comunicazione basata su Windows Communication Foundation (WCF) per gli autori del servizio che intendono usare la comunicazione basata su WCF.

## Listener di comunicazione WCF
L'implementazione specifica di WCF dell'oggetto **ICommunicationListener** viene garantita dalla classe **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener**.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    // TODO: If your service needs to handle user requests, return a list of ServiceReplicaListeners here.
    return new[] { new ServiceReplicaListener(parameters =>
        new WcfCommunicationListener(ServiceInitializationParameters,typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        }
    )};
}

```

## Scrittura di client per lo stack di comunicazione WCF
Per consentire ai client di scrittura di comunicare con i servizi tramite WCF, il framework fornisce l'oggetto **WcfClientCommunicationFactory**, ovvero l'implementazione specifica di WCF di [ClientCommunicationFactoryBase](service-fabric-reliable-service-communication.md).

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

Il canale di comunicazione WCF è accessibile dall'oggetto **WcfCommunicationClient** creato da **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

Il codice client può usare **WcfCommunicationClientFactory** insieme a **ServicePartitionClient** per determinare l'endpoint del servizio e comunicare con esso.

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
    null);          // do not retry Exception types


//
// Create a client for communicating with the calc service that has been created with the
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

## Passaggi successivi
* [Chiamata di procedura remota con i Reliable Services remoti](service-fabric-reliable-services-communication-remoting.md)

* [Web API con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_0128_2016-->