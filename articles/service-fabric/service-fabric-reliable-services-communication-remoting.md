---
title: Comunicazione remota usando C# su Service Fabric | Microsoft Docs
description: La comunicazione remota di Service Fabric consente a client e servizi di comunicare con i servizi C# tramite una chiamata di procedura remota.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: f9cd6e2fee738d2d42c790b4eb7b9a876a44b01d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772976"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Comunicazione remota nei servizi C# con Reliable Services

> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java su Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Per i servizi che non sono legati a un protocollo di comunicazione o a uno stack particolare, ad esempio API Web, Windows Communication Foundation (WCF) o altri, il framework Reliable Services fornisce un meccanismo remoto per impostare in modo semplice e rapido chiamate di procedura remota per i servizi. In questo articolo viene illustrato come impostare le chiamate di procedura remota per i servizi scritti con C#.

## <a name="set-up-remoting-on-a-service"></a>Impostare la funzionalità remota in un servizio

La procedura di configurazione della funzionalità remota per un servizio è costituita da due semplici passaggi:

1. Creare un'interfaccia per l’implementazione del servizio. Questa interfaccia definisce i metodi che sono disponibili per una chiamata di procedura remota nel servizio e devono essere metodi asincroni di restituzione di attività. L'interfaccia deve implementare `Microsoft.ServiceFabric.Services.Remoting.IService` per segnalare che il servizio dispone di un'interfaccia remota.
2. Usare un listener di comunicazione remota nel servizio. Un listener di comunicazione remota è un'implementazione `ICommunicationListener` che offre funzionalità di comunicazione remota. Lo spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.Runtime` contiene il metodo di estensione `CreateServiceRemotingListener` per i servizi con e senza stato, che consente di creare un listener di comunicazione remota usando il protocollo di trasporto predefinito per la comunicazione remota.

>[!NOTE]
>Lo spazio dei nomi `Remoting` è disponibile come pacchetto separato, denominato NuGet `Microsoft.ServiceFabric.Services.Remoting`.

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

Il framework remoto propaga le eccezioni generate dal servizio al client. Di conseguenza, quando è in uso `ServiceProxy`, il client è responsabile per la gestione delle eccezioni generate dal servizio.

## <a name="service-proxy-lifetime"></a>Durata del proxy servizio

La creazione del proxy servizio è un'operazione semplice e, pertanto, potrai creare tutti i proxy che vuoi. Le istanze del proxy servizio possono essere usate più volte, fintantoché necessarie. Se una chiamata di procedura remota genera un'eccezione, sarà comunque possibile usare nuovamente la stessa istanza del proxy. Ogni proxy servizio contiene un client di comunicazione usato per inviare messaggi sulla rete. Durante le chiamate remote, vengono effettuati controlli interni per verificare che il client di comunicazione sia valido. In base ai risultati di tali controlli, se necessario, il client di comunicazione viene ricreato. Inoltre, se si genera un'eccezione, non sarà necessario creare nuovamente `ServiceProxy`.

### <a name="service-proxy-factory-lifetime"></a>Durata del proxy servizio factory

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) è una factory che crea istanze di proxy per interfacce di connessione remota diverse. Se si usa l'API `ServiceProxyFactory.CreateServiceProxy` per la creazione di un proxy, il framework crea un singleton proxy servizio.
È utile per crearne una manualmente quando è necessario eseguire l'override delle proprietà [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory).
La creazione di una factory è un'operazione costosa. Il proxy servizio factory mantiene una cache interna di comunicazione client.
Una best practice consiste nel salvare nella cache il proxy servizio factory il più a lungo possibile.

## <a name="remoting-exception-handling"></a>Gestione delle eccezioni di comunicazione remota

Tutte le eccezioni generate dall'API del servizio vengono inviate nuovamente al client come AggregateException. Le eccezioni di comunicazione remota devono riuscire a essere serializzate da DataContract. In caso contrario, l'API del proxy genera una [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) contenente l'errore di serializzazione.

Il proxy servizio gestisce tutte le eccezioni di failover per la partizione del servizio per la quale è stato creato. Risolve nuovamente gli endpoint in presenza di eccezioni di failover (eccezioni non temporanee) e tenta di nuovo la chiamata con l'endpoint corretto. Il numero di tentativi per le eccezioni di failover è illimitato.
In caso di eccezioni temporanee, il proxy ripete la chiamata.

I parametri di ripetizione dei tentativi predefiniti sono forniti da [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

L'utente può configurare questi valori passando l'oggetto OperationRetrySettings al costruttore ServiceProxyFactory.

## <a name="use-the-remoting-v2-stack"></a>Usare lo stack V2 per la comunicazione remota

A partire dalla versione 2.8 del pacchetto di comunicazione remota NuGet, è possibile usare lo stack V2 per la comunicazione remota. Lo stack di comunicazione remota V2 offre prestazioni migliori. Fornisce inoltre funzionalità come la serializzazione personalizzata e più API di collegamento.
Il codice del modello continua a usare lo stack V1 per la comunicazione remota.
La comunicazione remota V2 non è compatibile con V1 (lo stack di comunicazione remota precedente). Seguire le istruzioni nell'articolo [Aggiornamento da V1 a V2](#upgrade-from-remoting-v1-to-remoting-v2) per evitare gli eventuali effetti sulla disponibilità del servizio.

Gli approcci seguenti sono disponibili per l'abilitazione dello stack V2.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Usare un attributo assembly per lo stack V2

Questi passaggi modificano il modello di codice per usare lo stack V2 mediante un attributo assembly.

1. Modificare la risorsa endpoint da `"ServiceEndpoint"` a `"ServiceEndpointV2"` nel manifesto del servizio.

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

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Usare le classi V2 esplicite per lo stack V2

Come alternativa all'uso di un attributo assembly, lo stack V2 può anche essere abilitato usando classi V2 esplicite.

Questi passaggi modificano il codice del modello per usare lo stack V2, mediante classi V2 esplicite.

1. Modificare la risorsa endpoint da `"ServiceEndpoint"` a `"ServiceEndpointV2"` nel manifesto del servizio.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Usare [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) dallo spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime`.

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

3. Usare [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) dallo spazio dei nomi `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client`, per creare i client.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Aggiornamento dalla comunicazione remota V1 a quella V2

Per eseguire l'aggiornamento da V1 a V2, sono necessari due passaggi di aggiornamento. Seguire la procedura descritta in questa sequenza.

1. Aggiornare il servizio da V1 a V2 mediante il presente attributo.
Questa modifica assicura che il servizio sia in ascolto sul listener V1 e V2.

    a. Aggiungere una risorsa endpoint con il nome "ServiceEndpointV2" nel manifesto del servizio.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Usare il seguente metodo di estensione per creare un listener di comunicazione remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Aggiungere l'attributo assembly nelle interfacce per la comunicazione remota per usare i listener V1 e V2 e il client V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Aggiornare il client dalla versione V1 a quella V2 con l'attributo client V2.
Questo passaggio garantisce che il client usi lo stack V2.
Non è richiesta alcuna modifica nel progetto/servizio client. È sufficiente la compilazione di progetti client con assembly di interfaccia aggiornata.

3. Questo passaggio è facoltativo. Usare l'attributo del listener V2 e aggiornare il servizio V2.
Questo passaggio assicura che il servizio sia in ascolto solo sul listener V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Usare lo stack V2 per la comunicazione remota (compatibile con l'interfaccia)

 Lo stack di comunicazione remota V2 (compatibile con l'interfaccia e noto come V2_1) include tutte le funzionalità dello stack di comunicazione remota V2. La sua interfaccia è compatibile con lo stack di comunicazione remota V1, ma non è compatibile con le precedenti V1 e V2. Per eseguire l'aggiornamento da V1 a V2_1, senza influire sulla disponibilità del servizio, attenersi ai passaggi dell'articolo Aggiornamento da V1 a V2 (compatibile con l'interfaccia).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Usare l'attributo assembly per lo stack di comunicazione remota V2 (compatibile con l'interfaccia)

Per passare a uno stack V2_1, seguire questi passaggi.

1. Aggiungere una risorsa endpoint con il nome "ServiceEndpointV2_1" nel manifesto del servizio.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Usare il seguente metodo di estensione per creare un listener di comunicazione remota.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Aggiungere l'[attributo assembly](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) nelle interfacce di comunicazione remota.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Non è necessario apportare modifiche nel progetto client.
Compilare l'assembly client con l'assembly dell'interfaccia, per assicurarsi che venga usato l'attributo assembly usato in precedenza.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Usare le classi esplicite di comunicazione remota per creare un listener/client factory per la versione V2 (compatibile con l'interfaccia)

A tale scopo, seguire questa procedura:

1. Aggiungere una risorsa endpoint con il nome "ServiceEndpointV2_1" nel manifesto del servizio.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Usare il [listener V2 per la comunicazione remota](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Il nome predefinito della risorsa endpoint servizio usato è "ServiceEndpointV2_1". Quest'ultimo deve essere definito nel manifesto del servizio.

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

3. Usare la versione V2 della [factory client](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Aggiornamento dal servizio di comunicazione remota V1 a V2 (compatibile con l'interfaccia)

Per eseguire l'aggiornamento da V1 a V2 (compatibile con l'interfaccia e noto anche come V2_1), sono necessari due passaggi di aggiornamento. Seguire la procedura descritta in questa sequenza.

1. Aggiornare il servizio da V1 a V2_1 mediante il seguente attributo.
Questa modifica assicura che il servizio sia in ascolto sui listener V1 e V2_1.

    a. Aggiungere una risorsa endpoint con il nome "ServiceEndpointV2_1" nel manifesto del servizio.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Usare il seguente metodo di estensione per creare un listener di comunicazione remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Aggiungere l'attributo assembly nelle interfacce per la comunicazione remota, per usare i listener V1, V2_1 e il client V2_1.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Aggiornare il client dalla versione V1 a quella V2_1, mediante l'attributo client V2_1.
Questo passaggio si accerta che il client usi lo stack V2_1.
Non è richiesta alcuna modifica nel progetto/servizio client. È sufficiente la compilazione di progetti client con assembly di interfaccia aggiornata.

3. Questo passaggio è facoltativo. Rimuovere la versione del listener V1 dall'attributo e aggiornare il servizio V2.
Questo passaggio assicura che il servizio sia in ascolto solo sul listener V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Usare la serializzazione personalizzata con i messaggi di comunicazione remota su cui è stato eseguito il wrapping

Nei messaggi di comunicazione remota su cui è stato eseguito il wrapping, si creano singoli oggetti sottoposti a wrapping, i cui parametri vengono visualizzati al loro interno come campi.
A tale scopo, seguire questa procedura:

1. Implementare l'interfaccia `IServiceRemotingMessageSerializationProvider`, al fine di fornire l'implementazione per la serializzazione personalizzata.
    Questo frammento di codice mostra come appare l'implementazione.

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

2. Sostituire il provider di serializzazione predefinito con `JsonSerializationProvider` per un listener per la comunicazione remota.

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

3. Sostituire il provider di serializzazione predefinito con `JsonSerializationProvider` per un client factory per la comunicazione remota.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Passaggi successivi

* [Web API con OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicazioni di Windows Communication Foundation con Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Comunicazioni protette per Reliable Services](service-fabric-reliable-services-secure-communication.md)
