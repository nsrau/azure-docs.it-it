<properties
   pageTitle="Servizio remoto in Infrastruttura di servizi | Microsoft Azure"
   description="L’infrastruttura dei servizi consente ai client e ai servizi di comunicare con i servizi tramite una chiamata di procedura remota."
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
   ms.date="11/12/2015"
   ms.author="bharatn@microsoft.com"/>

# Servizio remoto con Reliable Services
Per i servizi che non sono legati a un protocollo di comunicazione o stack particolare, ad esempio WebAPI, WCF o altri, il framework fornisce un meccanismo remoto per impostare rapidamente e facilmente la chiamata di procedura remota per i servizi.

## Impostare la funzionalità remota in un servizio
L’impostazione della funzionalità remota per un servizio viene eseguita in due semplici passaggi.

1. Creare un'interfaccia per l’implementazione del servizio. Questa interfaccia definisce i metodi che saranno disponibili per la chiamata di procedura remota nel servizio e devono essere metodi asincroni di restituzione di attività. L’interfaccia deve implementare `Microsoft.ServiceFabric.Services.Remoting.IService` per segnalare che il servizio dispone di un'interfaccia remota. 
2. Utilizzare `Microsoft.ServiceFabric.Services.Remoting.Runtime.ServiceRemotingListener` nel servizio. Si tratta di un’implementazione`ICommunicationListener` che fornisce funzionalità di accesso remoto.

Ad esempio, il servizio di Hello World espone un metodo singolo per ottenere "Hello World" su una chiamata di procedura remota:

```csharp
public interface IHelloWorldStateful : IService
{
    Task<string> GetHelloWorld();
}

internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[] { new ServiceReplicaListener(parameters => new ServiceRemotingListener<HelloWorldStateful>(parameters, this)) };
    }

    public Task<string> GetHelloWorld()
    {
        return Task.FromResult("Hello World!");
    }
}

```
> [AZURE.NOTE]Gli argomenti e i tipi restituiti nell'interfaccia del servizio possono essere semplici, complessi, o personalizzati, ma devono essere serializzabili mediante il serializzatore [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) .NET.


## Chiamare i metodi del servizio remoto
La chiamata dei metodi su un servizio mediante lo stack remoto viene eseguita utilizzando un proxy locale al servizio tramite la classe`Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`. Il `ServiceProxy` crea un proxy locale usando la stessa interfaccia implementata dal servizio. Con tale proxy, è possibile semplicemente chiamare dei metodi nell'interfaccia in modalità remota.


```csharp

IHelloWorldStateful helloWorldClient = ServiceProxy.Create<IHelloWorldStateful>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

Il framework remoto propaga le eccezioni generate nel servizio al client. La logica di gestione delle eccezioni nel client tramite `ServiceProxy` è in grado di gestire direttamente le eccezioni generate dal servizio.
 
## Passaggi successivi

* [Web API con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Comunicazione di WCF con Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=Nov15_HO4-->