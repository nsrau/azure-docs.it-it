---
title: Comunicazione remota dei servizi con C# in Service Fabric | Microsoft Docs
description: La comunicazione remota di Service Fabric consente a client e servizi di comunicare con i servizi C# tramite una chiamata di procedura remota.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: e7652fe1b211e6811a4a3aa61bc2aa9d2f529dda
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205817"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Comunicazione remota con i servizi in C# con Reliable Services
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java su Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Per i servizi che non sono legati a un protocollo di comunicazione o uno stack particolare, ad esempio WebAPI, Windows Communication Foundation (WCF) o altri, il framework Reliable Services fornisce un meccanismo remoto per impostare in modo semplice e rapido chiamate di procedura remota per i servizi. In questo articolo viene illustrato come impostare le chiamate di procedura remota per i servizi scritti con C#.

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

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
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
La creazione di ServiceProxy è un'operazione semplice e, pertanto, è possibile creare quanti proxy si desidera. Le istanze del proxy servizio possono essere usate più volte, fintanto che sono necessarie. Se una chiamata di procedura remota genera un'eccezione, è possibile comunque riusare la stessa istanza del proxy. Ogni proxy servizio contiene un client di comunicazione usato per inviare messaggi sulla rete. Durante le chiamate remote, vengono effettuati controlli interni per verificare che il client di comunicazione sia valido. In base ai risultati di tali controlli, se necessario, il client di comunicazione viene ricreato. Pertanto, se si verifica un'eccezione, non è necessario ricreare `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>Durata di ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) è una factory che crea istanze di proxy per interfacce di connessione remota diverse. Se si usa l'API `ServiceProxy.Create` per la creazione di un proxy, il framework crea un singleton ServiceProxy.
È utile per crearne una manualmente quando è necessario eseguire l'override delle proprietà [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory).
La creazione di una factory è un'operazione costosa. ServiceProxyFactory mantiene una cache interna del client di comunicazione.
La procedura consigliata consiste nel memorizzare nella cache ServiceProxyFactory il più a lungo possibile.

## <a name="remoting-exception-handling"></a>Gestione delle eccezioni remote
Tutte le eccezioni generate dall'API del servizio vengono inviate nuovamente al client come AggregateException. RemoteExceptions deve essere serializzabile per DataContract; in caso contrario, l'API del proxy genera una [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) contenente l'errore di serializzazione.

ServiceProxy gestisce tutte le eccezioni di failover per la partizione del servizio per la quale è stato creato. Risolve nuovamente gli endpoint in presenza di eccezioni di failover (eccezioni non temporanee) e tenta di nuovo la chiamata con l'endpoint corretto. Il numero di tentativi per le eccezioni di failover è indefinito.
In caso di eccezioni temporanee, il proxy ripete la chiamata.

I parametri di ripetizione dei tentativi predefiniti sono forniti da [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

L'utente può configurare questi valori passando l'oggetto OperationRetrySettings al costruttore ServiceProxyFactory.

## <a name="how-to-use-the-remoting-v2-stack"></a>Come usare lo stack V2 per la comunicazione remota

Così come per il pacchetto per la comunicazione remota NuGet 2.8, è possibile usare lo stack V2 per la comunicazione remota. Lo stack V2 per la comunicazione remota è più efficiente e offre funzionalità come API serializzabili personalizzate e più collegabili.
Il codice del modello continua a usare lo stack V1 per la comunicazione remota.
La comunicazione remota V2 non è compatibile con V1, ovvero con lo stack per la comunicazione remota precedente, seguire le istruzioni qui di seguito su [come passare da V1 a V2](#how-to-upgrade-from-remoting-v1-to-remoting-v2) senza compromettere la disponibilità del servizio.

Gli approcci seguenti sono disponibili per l'abilitazione dello stack V2.

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>Uso di un attributo assembly per usare lo stack V2

Questa procedura modifica il codice del modello per usare lo Stack V2 usando un attributo assembly.

1. Modificare la risorsa Endpoint da `"ServiceEndpoint"` a `"ServiceEndpointV2"` nel manifesto del servizio.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Usare il metodo di estensione `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` per creare i listener di comunicazione remota (uguale per V1 e V2).

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. Contrassegnare l'assembly che contiene le interfacce di comunicazione remota con un attributo `FabricTransportServiceRemotingProvider`.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
  ```

Non sono necessarie modifiche di codice nel progetto client.
Compilare l'assembly client con l'assembly dell'interfaccia, per assicurarsi che venga usato l'attributo assembly indicato in precedenza.

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>Uso delle classi V2 esplicite per usare lo stack V2

Come alternativa all'uso di un attributo assembly, lo stack V2 può anche essere abilitato usando classi V2 esplicite.

Questa procedura modifica il codice del modello per usare lo Stack V2 usando classi V2 esplicite.

1. Modificare la risorsa Endpoint da `"ServiceEndpoint"` a `"ServiceEndpointV2"` nel manifesto del servizio.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Usare [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) dello spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime`.

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

3. Usare [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) dello spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` per creare i client.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Come eseguire l'aggiornamento dal servizio di comunicazione remota V1 a V2.
Per eseguire l'aggiornamento da V1 a V2, sono necessari gli aggiornamenti in due passaggi. La procedura seguente deve essere eseguita nell'ordine elencato.

1. Aggiornare il servizio da V1 a V2 mediante l'attributo seguente.
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

    c) Aggiungere l'attributo assembly nelle interfacce per la comunicazione remota per usare i listener V1 e V2 e il client V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Aggiornare il client V1 alla versione V2 con l'attributo client V2.
Questo passaggio si accerta che il client usi lo stack V2.
Non è richiesta alcuna modifica nel progetto/servizio client. La compilazione di progetti client con assembly di interfaccia aggiornata è sufficiente.

3. Questo passaggio è facoltativo. Usare l'attributo V2Listener e aggiornare il servizio V2.
Questo passaggio assicura che il servizio sia in ascolto solo sul listener V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
```


## <a name="how-to-use-remoting-v2interfacecompatible-stack"></a>Come usare lo stack di comunicazione remota V2(InterfaceCompatible)
 Lo stack di comunicazione remota V2(InterfaceCompatible noto anche come V2_1) include tutte le funzionalità dello stack di comunicazione remota V2 oltre ad essere uno stack di interfaccia compatibile con lo stack V1 di comunicazione remota, ma non è compatibile con V1 e V2. Per eseguire l'aggiornamento da V1 a V2_1 senza influire sulla disponibilità del servizio, attenersi al seguente articolo [Come eseguire l'aggiornamento da V1 a V2(InterfaceCompatible)](#how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible).


### <a name="using-assembly-attribute-to-use-remoting-v2interfacecompatible-stack"></a>Come usare l'attributo assembly per l'utilizzo dello stack di comunicazione remota V2(InterfaceCompatible)

Di seguito, i passaggi da seguire per passare allo stack V2_1.

1. Aggiungere una risorsa endpoint con il nome "ServiceEndpointV2_1" nel manifesto del servizio.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
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

3.  Aggiungere l'[attributo assembly](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) nelle interfacce di comunicazione remota.

  ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

  ```
Non sono necessarie modifiche nel progetto client.
Compilare l'assembly client con l'assembly dell'interfaccia, per assicurarsi che venga usato l'attributo assembly precedente.

### <a name="using-explicit-remoting-classes-to-create-listener-clientfactory-for-v2interfacecompatible-version"></a>Come usare le classi esplicite di comunicazione remota per creare listener/clientfactory per la versione V2(InterfaceCompatible)
Ecco i passaggi da seguire.
1.  Aggiungere una risorsa endpoint con il nome "ServiceEndpointV2_1" nel manifesto del servizio.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. Usare il [listener V2 per la comunicazione remota](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Il nome predefinito della risorsa per l'endpoint di servizio usato è "ServiceEndpointV2_1" e deve essere definito nel manifesto del servizio.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
  ```

3. Usare [factory client](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) V2.
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible"></a>Come eseguire l'aggiornamento dal servizio di comunicazione remota V1 a V2(InterfaceCompatible)
Per eseguire l'aggiornamento da V1 a V2(InterfaceCompatible noto anche come V2_1), sono necessari gli aggiornamenti in due passaggi. La procedura seguente deve essere eseguita nell'ordine elencato.

1. Aggiornare il servizio da V1 a V2_1 mediante l'attributo seguente.
Questa modifica assicura che il servizio sia in ascolto sul listener V1 e V2_1.

    a) Aggiungere una risorsa endpoint con il nome "ServiceEndpointV2_1" nel manifesto del servizio.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
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

    c) Aggiungere l'attributo assembly nelle interfacce per la comunicazione remota per usare i listener V1, V2_1 e il client V2_1.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Aggiornare il client V1 alla versione V2_1 con l'attributo client V2_1.
Questo passaggio si accerta che il client usi lo stack V2_1.
Non è richiesta alcuna modifica nel progetto/servizio client. La compilazione di progetti client con assembly di interfaccia aggiornata è sufficiente.

3. Questo passaggio è facoltativo. Rimuovere la versione del listener V1 dall'attributo e aggiornare il servizio V2.
Questo passaggio assicura che il servizio sia in ascolto solo sul listener V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
```

### <a name="how-to-use-custom-serialization-with-remoting-wrapped-message"></a>Come usare la serializzazione personalizzata con i messaggi di comunicazione remota su cui è stato eseguito il wrapping
Nei messaggi di comunicazione remota su cui è stato eseguito il wrapping, vengono creati singoli oggetti sottoposti a wrapping i cui parametri vengono visualizzati al suo interno come campi.
Di seguito sono riportati i passaggi necessari:

1. Implementare l'interfaccia IServiceRemotingMessageSerializationProvider per offrire l'implementazione per la serializzazione personalizzata.
    Di seguito è riportato il frammento di codice che illustra come dovrebbe apparire l'implementazione.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
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
