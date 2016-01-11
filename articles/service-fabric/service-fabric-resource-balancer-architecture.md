<properties
   pageTitle="Architettura di Resource Balancer | Microsoft Azure"
   description="Panoramica dell'architettura di Resource Balancer di Service Fabric."
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

Resource Balancer in Service Fabric di Azure è costituito da un unico servizio di bilanciamento delle risorse centralizzato e da un componente esistente in ogni nodo. Questi sono collocati concettualmente con la Gestione failover di Service Fabric e con il nodo locale di Service Fabric.

L'agente locale è responsabile della raccolta e della memorizzazione nel buffer dei report sul carico provenienti dai servizi in esecuzione sul nodo locale, allo scopo di inviarli al servizio Resource Balancer e segnalare errori e altri eventi alla Gestione failover e a Resource Manager (1 e 2 nella figura precedente). Gestione failover e Resource Balancer collaborano per rispondere agli eventi di carico e agli altri eventi nel sistema. Questi eventi potrebbero includere gli errori di replica o nodo, i report sul carico, i servizi e le applicazioni che vengono create ed eliminate.

Ad esempio, quando una replica ha esito negativo, la Gestione Failover richiede che Resource Balancer suggerisca una posizione sostitutiva basata su dati relativi al carico di nodi diversi. Analogamente, al ricevimento di una nuova richiesta di servizio, la Gestione failover richiede indicazioni a Resource Balancer circa la posizione in cui inserire il servizio. In questi casi, Resource Balancer risponde con modifiche alle varie configurazioni di servizio (3). Queste modifiche vengono poi applicate concretamente dalla Gestione failover. Nell'esempio precedente, la Gestione failover crea una nuova replica sul nodo, che determina un bilanciamento generale ottimale (4).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Funzionalità di bilanciamento delle risorse:

- [Descrizione del cluster](service-fabric-resource-balancer-cluster-description.md)
- [Descrizione dei servizi](service-fabric-resource-balancer-service-description.md)
- [Creazione di report sul carico dinamico](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [Raggruppamento proattivo basato sulle metriche](service-fabric-resource-balancer-proactive-metric-packing.md)
- [Percentuale buffer dei nodi](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png

<!---HONumber=AcomDC_1223_2015-->