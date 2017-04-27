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
ms.date: 03/09/2017
ms.author: pakunapa
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 11e300b3b1d0433bd4790332593ada2d3eede883
ms.lasthandoff: 04/03/2017


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

1. Creare un'interfaccia per l’implementazione del servizio. Questa interfaccia definisce i metodi che sono disponibili per una chiamata di procedura remota nel servizio e devono essere metodi asincroni di restituzione di attività. L’interfaccia deve implementare `microsoft.serviceFabric.services.remoting.Service` per segnalare che il servizio dispone di un'interfaccia remota.
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

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere le comunicazioni per Reliable Services](service-fabric-reliable-services-secure-communication.md)

