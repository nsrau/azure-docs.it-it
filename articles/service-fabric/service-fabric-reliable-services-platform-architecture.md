<properties
   pageTitle="Architettura di reliable services | Microsoft Azure"
   description="Panoramica dell’architettura di reliable service per servizi con e senza stato."
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="alanwar"/>

# Architettura per reliable services con e senza stato

Un servizio Reliable Services di infrastruttura di servizi può essere con o senza stato. Ciascun tipo di servizio viene eseguito all'interno di un'architettura specifica, descritta in questo articolo. Per altre informazioni sulle differenze tra i servizi con e senza stato, vedere [panoramica di reliable services](service-fabric-reliable-services-introduction.md)

## Reliable Services con stato

### Architettura di un servizio con stato
![Diagramma dell'architettura](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### Reliable Services con stato

Un servizio Reliable Services con stato può derivare dalla classe StatefulService o StatefulServiceBase. Queste classi di base sono entrambe fornite da infrastruttura di servizi e offrono diversi livelli di supporto e astrazione per consentire al servizio con stato di interagire con infrastruttura di servizi e di partecipare come servizio al cluster di infrastruttura di servizi. La classe StatefulService deriva dalla classe StatefulServiceBase. StatefulServiceBase offre ai servizi maggiore flessibilità, ma richiede una comprensione approfondita dei componenti interni di Service Fabric. Per altre informazioni sulle specifiche di sviluppo di servizi mediante le classi StatefulService e StatefulServiceBase, vedere [panoramica di reliable services](service-fabric-reliable-services-introduction.md) e [uso avanzato del modello di programmazione reliable services](service-fabric-reliable-services-advanced-usage.md).

Entrambe le classi di base gestiscono la durata e il ruolo dell'implementazione del servizio. L'implementazione del servizio può eseguire l'override dei metodi virtuali di una delle classi di base se l'implementazione del servizio deve eseguire operazioni in quei punti del ciclo di vita o se desidera creare un oggetto CommunicationListener. Si noti che, anche se l'implementazione del servizio può implementare il proprio oggetto CommunicationListener esponendo ICommunicationListener, come mostrato nel diagramma precedente, il listener di comunicazione viene effettivamente implementato da Service Fabric.

Il servizio Reliable Services con stato usa Reliable State Manager per sfruttare i vantaggi offerti dalle raccolte Reliable Collections. Le raccolte Reliable Collections sono strutture dati locali altamente disponibili per il servizio. In altri termini, sono sempre disponibili indipendentemente dai failover del servizio. Ogni tipo di raccolta Reliable Collections viene implementato da un provider di stato affidabile. Per altre informazioni, vedere [Reliable Collections](service-fabric-reliable-services-reliable-collections.md).

### Reliable state manager e provider di stato

Reliable State Manager è l'oggetto che gestisce i provider di stato affidabili. Integra funzionalità che consentono di creare, eliminare, enumerare e assicurare che i provider di stato affidabili siano persistenti e a disponibilità elevata. Un'istanza di un provider di stato affidabile rappresenta un'istanza di una struttura dati persistente e a disponibilità elevata, ad esempio un dizionario o una coda. Ogni provider di stato affidabile espone un'interfaccia che viene usata dal servizio con stato per interagire con il provider di stato affidabile stesso. L'interfaccia IReliableDictionary, ad esempio, viene usata con il dizionario affidabile (ReliableDictionary), mentre l'interfaccia IReliableQueue viene usata con la coda affidabile (ReliableQueue). Tutti i provider di stato affidabili implementano l'interfaccia IReliableState.

Reliable State Manager dispone di un'interfaccia denominata IReliableStateManager. Tale interfaccia consente l’accesso dal servizio con stato. Le interfacce per i provider di stato affidabili vengono restituite tramite IReliableStateManager.

Reliable State Manager usa un'architettura plug-in dinamica che consente di integrare dinamicamente nuovi tipi di raccolte Reliable Collections.

Gli oggetti ReliableDictionary e ReliableQueue sono basati sull'implementazione di un archivio differenziale ad alte prestazioni con versioni.

### Transactional Replicator

Il componente Transactional Replicator deve garantire che lo stato del servizio, ovvero lo stato all'interno di Reliable State Manager e Reliable Collections, sia coerente in tutte le repliche che eseguono tale servizio e che sia inoltre persistente nel log. Reliable State Manager interagisce con Transactional Replicator tramite un meccanismo privato.

Transactional Replicator usa un protocollo di rete per comunicare lo stato alle altre repliche dell'istanza del servizio, in modo che tutte le repliche dispongano di informazioni sullo stato aggiornate.

Transactional Replicator usa un log per rendere persistenti le informazioni sullo stato, in modo che queste ultime non vengano danneggiate da eventuali arresti anomali dei processi o dei nodi. L'interazione con il log avviene tramite un meccanismo privato.

### Log

Il componente Log offre un archivio persistente ad alte prestazioni che può essere ottimizzato per la scrittura su unità disco rigido o SSD (Solid State Disk) e anche per un uso più efficiente dello spazio su disco. La progettazione del componente Log è finalizzata a rendere l'archiviazione persistente, ovvero i dischi rigidi, locale rispetto ai nodi che eseguono il servizio con stato, così da favorire una più bassa latenza e una maggiore velocità effettiva rispetto all'archiviazione persistente non locale rispetto al nodo.

Il componente Log usa due tipi di file di log. Il primo è un file di log condiviso a livello di nodo che deve trovarsi su un disco usato esclusivamente per tale file. Questo file si trova nella directory di lavoro del nodo di Service Fabric. Ogni replica del servizio dispone inoltre di un file di log dedicato che si trova all'interno della directory di lavoro del servizio. Il log condiviso è un'area di transizione per le informazioni sullo stato, mentre il file di log dedicato è la destinazione finale in cui tali informazioni vengono rese persistenti. In questa progettazione le informazioni sullo stato vengono prima scritte nel file di log condiviso e quindi trasferite in background al file di log dedicato. In questo modo la scrittura nel log condiviso dovrebbe avere la latenza più bassa e la velocità effettiva più alta per rendere più rapido lo stato di avanzamento del servizio.

Tuttavia, quando il componente Log viene configurato per l'ottimizzazione delle unità SSD mediante l'impostazione OptimizeForLocalSSD, le informazioni sullo stato vengono scritte direttamente nel file di log dedicato, ignorando quello condiviso. Dal momento che le unità SSD non subiscono ritardi a causa di situazioni di contesa della testina, la scrittura diretta sul file di log dedicato non presenta controindicazioni.

Quando il componente Log è ottimizzato per ridurre al minimo l'uso di spazio su disco mediante l'impostazione OptimizeLogForLowerDiskUsage, i file di log dedicati vengono creati come file NTFS sparse. Poiché in genere i file di log contengono solo in parte informazioni sullo stato, l'uso di file sparse consente l'overprovisioning dello spazio su disco disponibile per ospitare altre repliche. Se non è configurato in questo modo, lo spazio del file di log viene preassegnato e il componente Log può scrivere direttamente nel file con le prestazioni più alte.

A parte un'interfaccia utente minima usata per l'interazione con il componente Log, il file di log viene scritto come driver in modalità kernel. L'esecuzione del log come driver in modalità kernel può assicurare prestazioni elevate a tutti i servizi che lo usano.

Per altre informazioni sulla configurazione del log, vedere l'articolo [configurazione di reliable services con stato](service-fabric-reliable-services-configuration.md).

## Reliable services senza stato

### Architettura di un servizio senza stato
![Diagramma dell'architettura](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### Reliable services senza stato

Le implementazioni del servizio senza stato derivano dalla classe StatelessService o StatelessServiceBase. La classe StatelessServiceBase, tuttavia, consente una maggiore flessibilità rispetto alla classe StatelessService. Entrambe le classi di base gestiscono la durata e il ruolo del servizio. L'implementazione del servizio può eseguire l'override dei metodi virtuali di una delle classi di base se il servizio deve eseguire operazioni in quei punti del ciclo di vita o se desidera creare un oggetto CommunicationListener. Si noti che, anche se il servizio può implementare il proprio oggetto CommunicationListener esponendo ICommunicationListener, come mostrato nel diagramma precedente, il listener di comunicazione viene effettivamente implementato da Service Fabric.

Per altre informazioni sulle specifiche di sviluppo di servizi mediante le classi StatelessService e StatelessServiceBase classes, vedere [panoramica di reliable services](service-fabric-reliable-services-introduction.md) e [uso avanzato del modello di programmazione reliable services](service-fabric-reliable-services-advanced-usage.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Per altre informazioni su Service Fabric, vedere:

[Panoramica di Reliable Services](service-fabric-reliable-services-introduction.md)

[Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)

[Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)

[Configurazione di Reliable Services con stato](service-fabric-reliable-services-configuration.md)
 

<!---HONumber=Nov15_HO2-->