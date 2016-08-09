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
   ms.date="07/26/2016"
   ms.author="bharatn"/>

# Stack di comunicazione basato su WCF per Reliable Services
Il framework di Reliable Services consente agli autori del servizio di scegliere lo stack di comunicazione da usare per il servizio, nonché di eseguire il plug-in dello stack di comunicazione scelto tramite l'oggetto **ICommunicationListener** restituito dai metodi [CreateServiceReplicaListeners o CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md). Il framework offre un'implementazione dello stack di comunicazione basata su Windows Communication Foundation (WCF) per gli autori del servizio che intendono usare la comunicazione basata su WCF.

## Listener di comunicazione WCF
L'implementazione specifica di WCF dell'oggetto **ICommunicationListener** viene garantita dalla classe **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener**.

Si supponga di avere un contratto di servizio di tipo `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

È possibile creare un listener di comunicazione WCF nel servizio come descritto di seguito.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## Scrittura di client per lo stack di comunicazione WCF
Per consentire ai client di scrittura di comunicare con i servizi usando WCF, il framework fornisce l'oggetto **WcfClientCommunicationFactory**, ovvero l'implementazione specifica di WCF di [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Il canale di comunicazione WCF è accessibile dall'oggetto **WcfCommunicationClient** creato da **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Il codice client può usare **WcfCommunicationClientFactory** insieme a **WcfCommunicationClient** che implementa **ServicePartitionClient** per determinare l'endpoint del servizio e comunicare con esso.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
>[AZURE.NOTE] L'oggetto ServicePartitionResolver predefinito presuppone che il client sia in esecuzione nello stesso cluster del servizio. In caso contrario, creare un oggetto ServicePartitionResolver e passare gli endpoint di connessione del cluster.

## Passaggi successivi
* [Chiamata di procedura remota con i Reliable Services remoti](service-fabric-reliable-services-communication-remoting.md)

* [Web API con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Proteggere le comunicazioni per Reliable Services](service-fabric-reliable-services-secure-communication.md)

<!---HONumber=AcomDC_0727_2016-->