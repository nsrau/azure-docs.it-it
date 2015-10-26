<properties
   pageTitle="Stack di comunicazione predefinito fornito da Service Fabric"
   description="Questo articolo descrive lo stack di comunicazione predefinito fornito da Reliable Services Framework per le comunicazioni tra servizi e client."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="08/27/2015"
   ms.author="bharatn@microsoft.com"/>

# Stack di comunicazione predefinito fornito da Reliable Services Framework
Per gli autori del servizio che non sono vincolati a una particolare implementazione dello stack di comunicazione (WebAPI, WCF e così via), il framework offre elementi di comunicazione sul lato client e sul lato servizio che possono essere usati per le comunicazioni tra il servizio e il client.

> [AZURE.NOTE]Aggiornare i pacchetti NuGet alla versione più recente per ottenere le funzionalità indicate di seguito.

## Listener di comunicazione con il servizio
Il listener di comunicazione predefinita per il servizio viene implementato nella classe `ServiceCommunicationListener`

```csharp

public class ServiceCommunicationListener<TServiceImplementation> : ICommunicationListener where TServiceImplementation : class
{
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType);
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType, string endpointResourceName);

    public void Abort();
    public Task CloseAsync(CancellationToken cancellationToken);
    public void Initialize(ServiceInitializationParameters serviceInitializationParameters);
    public Task<string> OpenAsync(CancellationToken cancellationToken);
}

```
I metodi che il servizio implementa e desidera esporre ai client sono definiti come metodi asincroni in un'interfaccia che eredita dall'interfaccia `IService`. Il servizio può quindi creare un'istanza dell'oggetto `ServiceCommunicationListener` e restituirla nel metodo [`CreateCommunicationListener`](service-fabric-reliable-services-communication.md). Ad esempio, il codice del servizio HelloWorld per configurare questo stack di comunicazione può essere definito come segue.

```csharp

[DataContract]
public class Message
{
    [DataMember]
    public string Content;
}

public interface IHelloWorld : IService
{
    Task<Message> GetGreeting();
}

public class HelloWorldService : StatelessService, IHelloWorld
{
    public const string ServiceTypeName = "HelloWorldUsingDefaultCommunicationType";

    private Message greeting = new Message() { Content = "Default greeting" };

    protected override ICommunicationListener CreateCommunicationListener()
    {
        return new ServiceCommunicationListener<HelloWorldService>(this);
    }

    public Task<Message> GetGreeting()
    {
        return Task.FromResult(this.greeting);
    }
}

```
> [AZURE.NOTE]Gli argomenti e i tipi restituiti nell'interfaccia del servizio, ad esempio la classe Message precedente, dovrebbero essere serializzabili tramite il serializzatore [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) .NET.


## Scrittura di client per la comunicazione con ServiceCommunicationListener
Per consentire ai client di comunicare con i servizi tramite l'oggetto `ServiceCommunicationListener`, il framework offre una classe `ServiceProxy`.

```csharp

public abstract class ServiceProxy : IServiceProxy
{
...

    public static TServiceInterface Create<TServiceInterface>(Uri serviceName);

...
}

```

I client possono creare un'istanza di un oggetto proxy del servizio che implementa l'interfaccia del servizio corrispondente, nonché richiamare i metodi sull'oggetto proxy.

```csharp

var helloWorldClient = ServiceProxy.Create<IHelloWorld>(helloWorldServiceName);

var message = await helloWorldClient.GetGreeting();

Console.WriteLine("Greeting is {0}", message.Content);


```

>[AZURE.NOTE]Il framework di comunicazione si occupa di propagare al client le eccezioni generate nel servizio. La logica di gestione delle eccezioni nel client tramite ServiceProxy è in grado di gestire direttamente le eccezioni potenzialmente generate dal servizio.
 

<!---HONumber=Oct15_HO3-->