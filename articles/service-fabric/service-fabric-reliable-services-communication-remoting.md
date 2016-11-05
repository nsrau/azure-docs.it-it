---
title: Servizio remoto in Infrastruttura di servizi | Microsoft Docs
description: La funzionalità remota di Service Fabric consente a client e servizi di comunicare con i servizi tramite una chiamata di procedura remota.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 07/06/2016
ms.author: vturecek

---
# Servizio remoto con Reliable Services
Per i servizi che non sono legati a un protocollo di comunicazione o uno stack particolare, ad esempio WebAPI, Windows Communication Foundation (WCF) o altri, il framework Reliable Services fornisce un meccanismo remoto per impostare in modo semplice e rapido una chiamata di procedura remota per i servizi.

## Impostare la funzionalità remota in un servizio
La procedura di impostazione della funzionalità remota per un servizio è costituita da due semplici passaggi.

1. Creare un'interfaccia per l’implementazione del servizio. Questa interfaccia definisce i metodi che saranno disponibili per una chiamata di procedura remota nel servizio e devono essere metodi asincroni di restituzione di attività. L’interfaccia deve implementare `Microsoft.ServiceFabric.Services.Remoting.IService` per segnalare che il servizio dispone di un'interfaccia remota.
2. Usare un listener di comunicazione remota nel servizio. Si tratta di un’implementazione`ICommunicationListener` che fornisce funzionalità di accesso remoto. Lo spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.Runtime` contiene un metodo di estensione, `CreateServiceRemotingListener`, per i servizi con e senza stato che può essere usato per creare un listener di comunicazione remota con il protocollo di trasporto predefinito per la comunicazione remota.

Ad esempio, il servizio senza stato seguente espone un metodo singolo per ottenere "Hello World" su una chiamata RPC.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> GetHelloWorld();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorld()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context => 
            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Gli argomenti e i tipi restituiti nell'interfaccia del servizio possono essere semplici, complessi o personalizzati ma, in tutti i casi, devono essere serializzabili mediante il serializzatore .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
> 
> 

## Chiamare i metodi del servizio remoto
La chiamata dei metodi su un servizio mediante lo stack remoto viene eseguita usando un proxy locale al servizio tramite la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`. Il metodo `ServiceProxy` crea un proxy locale usando la stessa interfaccia implementata dal servizio. Con tale proxy, è possibile semplicemente chiamare dei metodi nell'interfaccia in modalità remota.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

Il framework remoto propaga le eccezioni generate nel servizio al client. La logica di gestione delle eccezioni nel client tramite `ServiceProxy`, quindi, è in grado di gestire direttamente le eccezioni generate dal servizio.

## Passaggi successivi
* [Web API con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicazione di WCF con Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Proteggere le comunicazioni per Reliable Services](service-fabric-reliable-services-secure-communication.md)

<!---HONumber=AcomDC_0713_2016-->