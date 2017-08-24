---
title: Servizio remoto con Azure Service Fabric | Microsoft Docs
description: "La funzionalità remota di Service Fabric consente a client e servizi di comunicare con i servizi tramite una chiamata di procedura remota."
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: dc4a362b5737bb424ca2c196c85f4c51b6ee5e30
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
# <a name="service-remoting-with-reliable-services"></a>Servizio remoto con Reliable Services
> [!div class="op_single_selector"]
> * [C# su Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java su Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Il framework Reliable Services fornisce un meccanismo remoto per impostare in modo semplice e rapido una chiamata di procedura remota per i servizi.

## <a name="set-up-remoting-on-a-service"></a>Impostare la funzionalità remota in un servizio
La procedura di impostazione della funzionalità remota per un servizio è costituita da due semplici passaggi.

1. Creare un'interfaccia per l’implementazione del servizio. Questa interfaccia definisce i metodi che sono disponibili per una chiamata di procedura remota nel servizio e devono essere metodi asincroni di restituzione di attività. L'interfaccia deve implementare `microsoft.serviceFabric.services.remoting.Service` per segnalare che il servizio dispone di un'interfaccia remota.
2. Usare un listener di comunicazione remota nel servizio. Si tratta di un’implementazione `CommunicationListener` che fornisce funzionalità di accesso remoto. `FabricTransportServiceRemotingListener` può essere usato per creare un listener di comunicazione remota con il protocollo di trasporto predefinito per la comunicazione remota.

Ad esempio, il servizio senza stato seguente espone un metodo singolo per ottenere "Hello World" su una chiamata RPC.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Gli argomenti e i tipi restituiti nell'interfaccia del servizio possono essere semplici, complessi o personalizzati ma, in tutti i casi, devono essere serializzabili.
>
>

## <a name="call-remote-service-methods"></a>Chiamare i metodi del servizio remoto
La chiamata dei metodi su un servizio mediante lo stack remoto viene eseguita usando un proxy locale al servizio tramite la classe `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` . Il metodo `ServiceProxyBase` crea un proxy locale usando la stessa interfaccia implementata dal servizio. Con tale proxy, è possibile semplicemente chiamare dei metodi nell'interfaccia in modalità remota.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Il framework remoto propaga le eccezioni generate nel servizio al client. La logica di gestione delle eccezioni nel client tramite `ServiceProxyBase` , quindi, è in grado di gestire direttamente le eccezioni generate dal servizio.

## <a name="service-proxy-lifetime"></a>Durata del proxy servizio
La creazione del proxy servizio è un'operazione semplice, pertanto l'utente può creare quanti proxy desidera. Il proxy servizio può essere usato più volte, fintanto che l'utente ne ha necessità. L'utente può usare nuovamente lo stesso proxy in caso di eccezione. Ogni ServiceProxy contiene il client di comunicazione usato per inviare messaggi sulla rete. Durante la chiamata all'API, vengono effettuati controlli interni per verificare se il client di comunicazione usato è valido. In base al risultato, il client di comunicazione viene ricreato. L'utente non ha pertanto necessità di ricreare il proxy servizio in caso di eccezione.

### <a name="serviceproxyfactory-lifetime"></a>Durata di ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory) è una factory che crea proxy per diverse interfacce di connessione remota. Se si usa l'API `ServiceProxyBase.create` per la creazione del proxy, il framework crea una `FabricServiceProxyFactory`.
È utile per crearne una manualmente quando è necessario eseguire l'override delle proprietà [ServiceRemotingClientFactory](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory).
La factory è un'operazione costosa. `FabricServiceProxyFactory` gestisce la cache dei client di comunicazione.
La procedura consigliata consiste nel mantenere `FabricServiceProxyFactory` memorizzato nella cache il più a lungo possibile.

## <a name="remoting-exception-handling"></a>Gestione delle eccezioni remote
Tutte le eccezioni generate dall'API del servizio vengono inviate nuovamente al client come RuntimeException o FabricException.

Il proxy servizio non gestisce tutte le eccezioni di failover per la partizione del servizio per la quale è stato creato. Risolve nuovamente gli endpoint in presenza di eccezioni di failover (eccezioni non temporanee) e tenta di nuovo la chiamata con l'endpoint corretto. Il numero di tentativi per l'eccezione di failover è indefinito.
In caso di eccezioni temporanee ritenta solo la chiamata.

Parametri di ripetizione dei tentativi predefiniti sono forniti da [OperationRetrySettings]. (https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings) L'utente può configurare questi valori passando l'oggetto OperationRetrySettings al costruttore di ServiceProxyFactory.

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere le comunicazioni per Reliable Services](service-fabric-reliable-services-secure-communication.md)

