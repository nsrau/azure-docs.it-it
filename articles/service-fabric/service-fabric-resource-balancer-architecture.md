<properties
   pageTitle="Architettura di Resource Balancer"
   description="Panoramica dell'architettura di Service Fabric Resource Balancer"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Panoramica dell'architettura di Resource Balancer

![Architettura di Resource Balancer][Image1]

Service Fabric Resource Balancer è costituito da un servizio di bilanciamento delle risorse centralizzato e da un componente presente su ogni nodo, collocati da un punto di vista concettuale rispettivamente con Service Fabric Failover Manager e con il nodo locale di Service Fabric.

L'agente locale è responsabile della raccolta e della memorizzazione nel buffer dei report sul carico provenienti dai servizi in esecuzione sul nodo locale, allo scopo di inviarli al servizio Resource Balancer e segnalare errori e altri eventi a Failover Manager e Resource Manager (1 e 2 nella figura precedente). Resource Balancer e Failover Manager funzionano in modo integrato per rispondere a eventi relativi al carico e ad altri eventi del sistema, ad esempio errori delle repliche o dei nodi, report sul carico e servizi e applicazioni che vengono creati ed eliminati. Ad esempio, quando una replica ha esito negativo, Failover Manager richiede a Service Fabric Resource Balancer di suggerire una posizione sostitutiva basata su dati relativi al carico di nodi diversi. Analogamente, al ricevimento di una nuova richiesta di servizio, Failover Manager richiede indicazioni a Resource Balancer circa la posizione in cui inserire il servizio. In tutti questi casi, Resource Balancer risponde con modifiche alle varie configurazioni di servizio (3), che vengono poi applicate concretamente da Failover Manager. Nell'esempio precedente Failover Manager crea una nuova replica sul nodo, che determina un bilanciamento generale ottimale (4).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Funzionalità di bilanciamento delle risorse:

- [Descrizione del cluster](service-fabric-resource-balancer-cluster-description.md)
- [Descrizione dei servizi](service-fabric-resource-balancer-service-description.md)
- [Creazione di report sul carico dinamico](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [Raggruppamento proattivo basato sulle metriche](service-fabric-resource-balancer-proactive-metric-packing.md)
- [Percentuale buffer dei nodi](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png
 

<!---HONumber=Oct15_HO3-->