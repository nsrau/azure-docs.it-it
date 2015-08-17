<properties
   pageTitle="Sviluppare un servizio di Service Fabric"
   description="Informazioni concettuali ed esercitazioni utili per comprendere come sviluppare un servizio di Service Fabric usando il modello di programmazione Reliable Actors o Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/14/2015"
   ms.author="ryanwi"/>

# Sviluppare un servizio di Service Fabric
Questa pagina contiene i collegamenti per accedere ad articoli di panoramica e concettuali e a un'esercitazione utili per imparare a sviluppare un servizio di Service Fabric. Service Fabric offre due modelli di programmazione di alto livello per la creazione di servizi: le API Reliable Actors e le API Reliable Services. In entrambi i casi si usufruisce degli stessi componenti principali di Service Fabric, ma si ottiene un diverso compromesso tra semplicità e flessibilità in termini di concorrenza, partizionamento e comunicazione. Per scegliere il framework più appropriato per un determinato servizio di un'applicazione, è utile comprendere tutti e due i modelli.

- [Scegliere un modello di programmazione](service-fabric-choose-framework.md)
- [Introduzione al modello Service Fabric Actors](service-fabric-reliable-actors-introduction.md)
- [Introduzione al modello di programmazione Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md)

## Modello di programmazione Reliable Actors
 Reliable Actors offre un modello basato su attori asincrono a thread singolo. Gli attori rappresentano le unità di stato e calcolo distribuite all'interno del cluster per ottenere una scalabilità elevata. Il modello Reliable Actors sfrutta l'archivio distribuito fornito dalla piattaforma Service Fabric sottostante per garantire una gestione dello stato altamente disponibile e coerente per gli sviluppatori di applicazioni. Per altre informazioni, leggere gli articoli seguenti:

- [Introduzione a Reliable Actors](service-fabric-reliable-actors-get-started.md)
- [Ciclo di vita degli attori e Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
- [Modalità d'uso della piattaforma Service Fabric da parte di Fabric Actors](service-fabric-reliable-actors-platform.md)
- [Note sulla serializzazione dei tipi di Azure Service Fabric Actors](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

La comunicazione con gli attori è descritta negli articoli seguenti:

- [Introduzione al modello Service Fabric Actors](service-fabric-reliable-actors-introduction.md#actor-communication)
- [Comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md)

Gli articoli seguenti illustrano scenari e modelli di progettazione utili:

- [Modelli di progettazione per il modello basato su attori](service-fabric-reliable-actors-patterns-introduction.md)  
- [Modello: Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)
- [Modello: Reti distribuite e grafici](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)
- [Modello: Governance delle risorse](service-fabric-reliable-actors-pattern-resource-governance.md)
- [Modello: Composizione dei servizi con stato](service-fabric-reliable-actors-pattern-stateful-service-composition.md)
- [Modello: Internet delle cose](service-fabric-reliable-actors-pattern-internet-of-things.md)
- [Modello: Calcolo distribuito](service-fabric-reliable-actors-pattern-distributed-computation.md)
- [Alcuni anti-modelli](service-fabric-reliable-actors-anti-patterns.md)

Per i metodi Reliable Actors viene garantita una semplice concorrenza basata su turni. La concorrenza, i timer, i promemoria e la reentrancy sono illustrati negli articoli seguenti:

- [Concorrenza](service-fabric-reliable-actors-introduction.md#concurrency)
- [Eventi e contatori delle prestazioni relativi alla concorrenza](service-fabric-reliable-actors-diagnostics.md)
- [Reentrancy degli attori](service-fabric-reliable-actors-reentrancy.md)
- [Timer degli attori](service-fabric-reliable-actors-timers-reminders.md)
 
Per informazioni sulla configurazione di Reliable Actors, vedere gli articoli seguenti:

- [Configurazione di KVSActorStateProvider](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)  
- [Configurazione di Reliable Actors - ReliableDictionaryActorStateProvider](../service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

Gli attori Reliable Actors emettono eventi e contatori delle prestazioni utilizzabili per eseguire la diagnostica e il monitoraggio del servizio:

- [Diagnostica degli attori](service-fabric-reliable-actors-diagnostics.md)
- [Eventi degli attori](service-fabric-reliable-actors-events.md)


## Modello di programmazione Reliable Services
Reliable Services offre un modello di programmazione di alto livello, semplice e potente, per specificare gli elementi importanti per l'applicazione. Per altre informazioni, leggere gli articoli seguenti:

- [Introduzione a Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Panoramica del modello di programmazione](../service-fabric-reliable-services-service-overview.md)  
- [Architettura](service-fabric-reliable-services-platform-architecture.md)
- [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
- [Configurazione di Reliable Services con stato](../Service-Fabric/service-fabric-reliable-services-configuration.md)
- [Uso avanzato del modello di programmazione Reliable Services](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)

La comunicazione con Reliable Services e le astrazioni utilizzabili dai client per individuare e comunicare con gli endpoint dei servizi sono descritte negli articoli seguenti:

- [Comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md)
- [Modello di comunicazione con i servizi](service-fabric-reliable-services-communication.md)
- [Stack di comunicazione predefinito fornito dal framework Reliable Services](service-fabric-reliable-services-communication-default.md)
- [Stack di comunicazione basato su WCF per Reliable Services](service-fabric-reliable-services-communication-wcf.md)
- [Introduzione ai servizi delle API Web di Microsoft Azure Service Fabric con self-hosting OWIN (VS 2015 RC)](service-fabric-reliable-services-communication-webapi.md)

I servizi Reliable Services emettono eventi e contatori delle prestazioni utilizzabili per eseguire la diagnostica e il monitoraggio del servizio:

- [Diagnostica di Reliable Services con stato](service-fabric-reliable-services-diagnostics.md)
 

<!---HONumber=August15_HO6-->