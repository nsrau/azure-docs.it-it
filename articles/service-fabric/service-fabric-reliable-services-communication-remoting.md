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
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 53c9072f98dfe9c03b85eb7409b8ed91c3c0ce33
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="service-remoting-with-reliable-services"></a>Comunicazione remota con i servizi con Reliable Services
Per i servizi che non sono legati a un protocollo di comunicazione o uno stack particolare, ad esempio WebAPI, Windows Communication Foundation (WCF) o altri, il framework Reliable Services fornisce un meccanismo remoto per impostare in modo semplice e rapido una chiamata di procedura remota per i servizi.

## <a name="set-up-remoting-on-a-service"></a>Impostare la comunicazione remota in un servizio
La procedura di impostazione della funzionalità remota per un servizio è costituita da due semplici passaggi.

1. Creare un'interfaccia per l’implementazione del servizio. Questa interfaccia definisce i metodi che sono disponibili per una chiamata di procedura remota nel servizio e devono essere metodi asincroni di restituzione di attività. L'interfaccia deve implementare `Microsoft.ServiceFabric.Services.Remoting.IService` per segnalare che il servizio dispone di un'interfaccia remota.
2. Usare un listener di comunicazione remota nel servizio. RemotingListener è un'implementazione `ICommunicationListener` che offre funzionalità di comunicazione remota. Lo spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.Runtime` contiene un metodo di estensione `CreateServiceRemotingListener`, per i servizi con e senza stato, che può essere usato per creare un listener di comunicazione remota con il protocollo di trasporto predefinito per la comunicazione remota.

>[!NOTE]
>Lo spazio dei nomi `Remoting` è disponibile come pacchetto NuGet separato denominato `Microsoft.ServiceFabric.Services.Remoting`

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
La chiamata dei metodi su un servizio mediante lo stack remoto viene eseguita usando un proxy locale al servizio tramite la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . Il metodo `ServiceProxy` crea un proxy locale usando la stessa interfaccia implementata dal servizio. Con tale proxy, è possibile chiamare i metodi nell'interfaccia in modalità remota.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Il framework remoto propaga le eccezioni generate dal servizio al client. Di conseguenza, quando si usa `ServiceProxy`, il client è responsabile per la gestione delle eccezioni generate dal servizio.

## <a name="service-proxy-lifetime"></a>Durata del proxy servizio
La creazione del proxy servizio è un'operazione semplice e, pertanto, l'utente può creare quanti proxy desidera. Le istanze del proxy servizio possono essere usate più volte, fintanto che l'utente ne ha necessità. Se una chiamata di procedura remota genera un'eccezione, gli utenti possono comunque riusare la stessa istanza del proxy. Ogni proxy servizio contiene un client di comunicazione usato per inviare messaggi sulla rete. Durante chiamate remote, vengono effettuati controlli interni per verificare che il client di comunicazione sia valido. In base al risultato, il client di comunicazione viene ricreato. Pertanto, se si verifica un'eccezione, l'utente non deve ricreare `ServiceProxy` perché questa operazione viene eseguita in modo trasparente.

### <a name="serviceproxyfactory-lifetime"></a>Durata di ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) è una factory che crea istanze di proxy per interfacce di connessione remota diverse. Se si usa l'API `ServiceProxy.Create` per la creazione di un proxy, il framework crea un singleton ServiceProxy.
È utile per crearne una manualmente quando è necessario eseguire l'override delle proprietà [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory).
La creazione di una factory è un'operazione costosa. ServiceProxyFactory mantiene una cache interna del client di comunicazione.
La procedura consigliata consiste nel memorizzare nella cache ServiceProxyFactory il più a lungo possibile.

## <a name="remoting-exception-handling"></a>Gestione delle eccezioni remote
Tutte le eccezioni generate dall'API del servizio vengono inviate nuovamente al client come AggregateException. RemoteExceptions deve essere serializzabile per DataContract; in caso contrario, l'API del proxy genera una [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) contenente l'errore di serializzazione.

ServiceProxy gestisce tutte le eccezioni di failover per la partizione del servizio per la quale è stato creato. Risolve nuovamente gli endpoint in presenza di eccezioni di failover (eccezioni non temporanee) e tenta di nuovo la chiamata con l'endpoint corretto. Il numero di tentativi per le eccezioni di failover è indefinito.
In caso di eccezioni temporanee, il proxy ripete la chiamata.

I parametri di ripetizione dei tentativi predefiniti sono forniti da [OperationRetrySettings](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
L'utente può configurare questi valori passando l'oggetto OperationRetrySettings al costruttore ServiceProxyFactory.
## <a name="how-to-use-remoting-v2-stack"></a>Come usare lo stack V2 per la comunicazione remota
Con il pacchetto per la comunicazione remota NuGet 2.8, è possibile usare lo stack V2 per la comunicazione remota. Lo stack V2 per la comunicazione remota è più efficiente e offre funzionalità come API serializzabili personalizzate e più collegabili.
Per impostazione predefinita, se non si effettuano le modifiche seguenti, viene ancora usato lo stack V1 per la comunicazione remota.
La comunicazione remota V2 non è compatibile con V1, ovvero con lo stack per la comunicazione remota precedente. Continuare a leggere l'articolo seguente su come passare da V1 a V2 senza compromettere la disponibilità del servizio.

### <a name="using-assembly-attribute-to-use-v2-stack"></a>Uso dell'attributo assembly per usare lo stack V2.

Ecco i passaggi da seguire per passare allo stack V2.

1. Aggiungere una risorsa endpoint con il nome "ServiceEndpointV2" nel manifesto del servizio.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2.  Usare il metodo di estensione per la comunicazione remota per creare un listener di comunicazione remota.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Aggiungere l'attributo assembly nelle interfacce di comunicazione remota.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```
Non sono necessarie modifiche nel progetto client.
Compilare l'assembly client con l'assembly dell'interfaccia, per assicurarsi che venga usato l'attributo assembly precedente.

### <a name="using-explicit-v2-classes-to-create-listener-clientfactory"></a>Uso di classi V2 in modo esplicito per creare listener/factory client
Ecco i passaggi da seguire.
1.  Aggiungere una risorsa endpoint con il nome "ServiceEndpointV2" nel manifesto del servizio.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2. Usare il [listener V2 per la comunicazione remota](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingistener?view=azure-dotnet). Il nome predefinito della risorsa per l'endpoint di servizio usato è "ServiceEndpointV2" e deve essere definito nel manifesto del servizio.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Usare [factory client](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) V2.
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Come eseguire l'aggiornamento dal servizio di comunicazione remota V1 a V2.
Per eseguire l'aggiornamento da V1 a V2, sono necessari gli aggiornamenti in due passaggi. La procedura seguente deve essere eseguita nell'ordine elencato.

1. Aggiornare il servizio da V1 a V2 mediante l'attributo CompactListener.
Questa modifica assicura che il servizio sia in ascolto sul listener V1 e V2.

    a) Aggiungere una risorsa endpoint con il nome "ServiceEndpointV2" nel manifesto del servizio.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b) Usare il metodo di estensione seguente per creare un listener di comunicazione remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) Aggiungere l'attributo assembly nelle interfacce per la comunicazione remota per usare CompatListener e il client V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Aggiornare il client V1 alla versione V2 con l'attributo client V2.
Questo passaggio si accerta che il client usi lo stack V2.
Non è richiesta alcuna modifica nel progetto/servizio client. La compilazione di progetti client con assembly di interfaccia aggiornata è sufficiente.

3. Questo passaggio è facoltativo. Usare l'attributo V2Listener e aggiornare il servizio V2.
Questo passaggio assicura che il servizio sia in ascolto solo sul listener V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Come usare la serializzazione personalizzata con la comunicazione remota V2.
L'esempio seguente usa la serializzazione Json con la comunicazione remota V2.
1. Implementare l'interfaccia IServiceRemotingMessageSerializationProvider per offrire l'implementazione per la serializzazione personalizzata.
    Di seguito è riportato il frammento di codice che illustra come dovrebbe apparire l'implementazione.

    ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
      public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
          IEnumerable<Type> requestBodyTypes)
      {
          return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
      }

      public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
          IEnumerable<Type> responseBodyTypes)
      {
          return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
      }

      public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
      {
          return new JsonMessageFactory();
      }
     }

  class JsonMessageFactory: IServiceRemotingMessageBodyFactory
  {
      public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
          int numberOfParameters)
      {
          return new JsonRemotingRequestBody(new JObject());
      }

      public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
      {
          return  new JsonRemotingResponseBody();
      }
   }

  class ServiceRemotingRequestJsonMessageBodySerializer: IServiceRemotingRequestMessageBodySerializer
  {
      public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
          IEnumerable<Type> parameterInfo)
      {
      }

      public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
      {
          if (serviceRemotingRequestMessageBody == null)
          {
              return null;
          }

          var json = serviceRemotingRequestMessageBody.ToString();
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var segments = new List<ArraySegment<byte>> {segment};
          return new OutgoingMessageBody(segments);
      }

      public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
      {
          using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

          using (JsonReader reader = new JsonTextReader(sr))
          {
              var serializer = new JsonSerializer();
              var ob = serializer.Deserialize<JObject>(reader);
              var ob2 = new JsonRemotingRequestBody(ob);
              return ob2;
          }
      }
  }

  class ServiceRemotingResponseJsonMessageBodySerializer: IServiceRemotingResponseMessageBodySerializer
  {

      public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
          IEnumerable<Type> parameterInfo)
      {
      }

      public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
      {
          var json = JsonConvert.SerializeObject(responseMessageBody,new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
          });
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var list = new List<ArraySegment<byte>> {segment};
          return new OutgoingMessageBody(list);
      }

      public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
      {
          using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

          using (var reader = new JsonTextReader(sr))
          {
              var serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                  TypeNameHandling = TypeNameHandling.All
              });
              return serializer.Deserialize<JsonRemotingResponseBody>(reader);
          }
      }
  }
  internal class JsonRemotingResponseBody: IServiceRemotingResponseMessageBody
  {
      public object Value;

      public void Set(object response)
      {
          this.Value = response;
      }

      public object Get(Type paramType)
      {
          return this.Value;
      }
  }

  class JsonRemotingRequestBody: IServiceRemotingRequestMessageBody
    {
      private readonly JObject jobject;

        public JsonRemotingRequestBody(JObject ob)
        {
            this.jobject = ob;
        }

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.jobject.Add(parameName, JToken.FromObject(parameter));
        }

        public object GetParameter(int position, string parameName, Type paramType)
       {
           var ob = this.jobject[parameName];
           return ob.ToObject(paramType);
       }

       public override string ToString()
        {
            return this.jobject.ToString();
        }
    }
    ```

2.    Eseguire l'override del provider di serializzazione predefinito con JsonSerializationProvider per il listener di comunicazione remota.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Eseguire l'override del provider di serializzazione predefinito con JsonSerializationProvider per il factory client di comunicazione remota.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>Passaggi successivi
* [Web API con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicazione di WCF con Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Proteggere le comunicazioni per Reliable Services](service-fabric-reliable-services-secure-communication.md)
