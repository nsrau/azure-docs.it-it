
---
title: "Funzionalità remota dei servizi in Service Fabric | Microsoft Docs"
description: "La funzionalità remota di Service Fabric consente a client e servizi di comunicare con i servizi tramite una chiamata di procedura remota."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 92a8894f24c234fbf38eda086531b524cceccfc1
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017


---
# <a name="service-remoting-with-reliable-services"></a>Servizio remoto con Reliable Services
Per i servizi che non sono legati a un protocollo di comunicazione o uno stack particolare, ad esempio WebAPI, Windows Communication Foundation (WCF) o altri, il framework Reliable Services fornisce un meccanismo remoto per impostare in modo semplice e rapido una chiamata di procedura remota per i servizi.

## <a name="set-up-remoting-on-a-service"></a>Impostare la funzionalità remota in un servizio
La procedura di impostazione della funzionalità remota per un servizio è costituita da due semplici passaggi.

1. Creare un'interfaccia per l'implementazione del servizio. Questa interfaccia definisce i metodi che saranno disponibili per una chiamata di procedura remota nel servizio e devono essere metodi asincroni di restituzione di attività. L'interfaccia deve implementare `Microsoft.ServiceFabric.Services.Remoting.IService` per segnalare che il servizio dispone di un'interfaccia remota.
2. Usare un listener di comunicazione remota nel servizio. Si tratta di un'implementazione `ICommunicationListener` che fornisce funzionalità di accesso remoto. Lo spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.Runtime` contiene un metodo di estensione `CreateServiceRemotingListener`, per i servizi con e senza stato, che può essere usato per creare un listener di comunicazione remota con il protocollo di trasporto predefinito per la comunicazione remota.

Nota: lo spazio dei nomi `Remoting` è disponibile come pacchetto NuGet separato denominato `Microsoft.ServiceFabric.Services.Remoting` 

Ad esempio, il servizio senza stato seguente espone un metodo singolo per ottenere "Hello World" su una chiamata RPC.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Gli argomenti e i tipi restituiti nell'interfaccia del servizio possono essere semplici, complessi o personalizzati ma, in tutti i casi, devono essere serializzabili mediante il serializzatore .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Chiamare i metodi del servizio remoto
La chiamata dei metodi su un servizio mediante lo stack remoto viene eseguita usando un proxy locale al servizio tramite la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . Il metodo `ServiceProxy` crea un proxy locale usando la stessa interfaccia implementata dal servizio. Con tale proxy, è possibile semplicemente chiamare dei metodi nell'interfaccia in modalità remota.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Il framework remoto propaga le eccezioni generate nel servizio al client. La logica di gestione delle eccezioni nel client tramite `ServiceProxy` , quindi, è in grado di gestire direttamente le eccezioni generate dal servizio.

## <a name="service-proxy-lifetime"></a>Durata del proxy servizio
La creazione del proxy servizio è un'operazione semplice, pertanto l'utente può creare quanti proxy desidera. Il proxy servizio può essere usato più volte, fintanto che l'utente ne ha necessità. L'utente può usare nuovamente lo stesso proxy in caso di eccezione. Ogni proxy servizio contiene il client di comunicazione usato per inviare messaggi sulla rete. Durante la chiamata all'API, vengono effettuati controlli interni per verificare se il client di comunicazione usato è valido. In base al risultato, il client di comunicazione viene ricreato. L'utente non ha pertanto necessità di ricreare il proxy servizio in caso di eccezione.

### <a name="serviceproxyfactory-lifetime"></a>Durata di ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) è una factory che crea proxy per interfacce di connessione remota diverse. Se si usa ServiceProxy.Create API per la creazione di proxy, il framework crea la ServiceProxyFactory singleton.
È utile per crearne una manualmente quando è necessario eseguire l'override delle proprietà [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory).
La factory è un'operazione costosa. ServiceProxyFactory gestisce la cache del client di comunicazione.
La procedura consigliata consiste nel memorizzare nella cache ServiceProxyFactory il più a lungo possibile.

## <a name="remoting-exception-handling"></a>Gestione delle eccezioni remote
Tutte le eccezioni generate dall'API del servizio vengono inviate nuovamente al client come AggregateException. Le eccezioni remote devono essere serializzabili per DataContract; in caso contrario, viene generata una [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) nell'API del proxy contenente l'errore di serializzazione.

Il proxy servizio non gestisce tutte le eccezioni di failover per la partizione del servizio per la quale è stato creato. Risolve nuovamente gli endpoint in presenza di eccezioni di failover (eccezioni non temporanee) e tenta di nuovo la chiamata con l'endpoint corretto. Il numero di tentativi per l'eccezione di failover è indefinito.
In caso di eccezioni temporanee ritenta solo la chiamata.

Parametri di ripetizione dei tentativi predefiniti sono forniti da [OperationRetrySettings]. (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) L'utente può configurare questi valori passando l'oggetto OperationRetrySettings al costruttore di ServiceProxyFactory.

## <a name="next-steps"></a>Passaggi successivi
* [Web API con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicazione di WCF con Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Proteggere le comunicazioni per Reliable Services](service-fabric-reliable-services-secure-communication.md)

