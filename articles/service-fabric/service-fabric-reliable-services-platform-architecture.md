---
title: Architettura di reliable services | Microsoft Docs
description: Panoramica dell'architettura per servizi Reliable Services con e senza stato
services: service-fabric
documentationcenter: .net
author: AlanWarwick
manager: timlt
editor: vturecek

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/30/2016
ms.author: alanwar

---
# Architettura per servizi Reliable Services con e senza stato
Un servizio Reliable Services di Azure Service Fabric può essere con o senza stato. Ogni tipo di servizio viene eseguito all'interno di un'architettura specifica, descritta in questo articolo. Per altre informazioni sulle differenze tra i servizi con e senza stato, vedere [Panoramica di Reliable Services](service-fabric-reliable-services-introduction.md).

## Reliable Services con stato
### Architettura di un servizio con stato
![Diagramma dell'architettura di un servizio con stato](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### Reliable Services con stato
Un servizio Reliable Services con stato può derivare dalla classe StatefulService o StatefulServiceBase. Queste classi di base sono entrambe fornite da Service Fabric e offrono diversi livelli di supporto e astrazione per consentire al servizio con stato di interagire con Service Fabric e di partecipare come servizio al cluster di Service Fabric.

La classe StatefulService deriva dalla classe StatefulServiceBase, che offre ai servizi maggiore flessibilità ma richiede una comprensione approfondita dei componenti interni di Service Fabric. Per altre informazioni sulle specifiche di sviluppo di servizi con le classi StatefulService e StatefulServiceBase, vedere [Panoramica di Reliable Services](service-fabric-reliable-services-introduction.md) e [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md).

Entrambe le classi di base gestiscono la durata e il ruolo dell'implementazione del servizio. L'implementazione del servizio può eseguire l'override dei metodi virtuali di una delle classi di base se deve eseguire operazioni in quelle fasi del ciclo di vita dell'implementazione o se deve creare un oggetto CommunicationListener. Si noti che, anche se l'implementazione del servizio può implementare il proprio oggetto CommunicationListener esponendo ICommunicationListener, come mostrato nel diagramma precedente, il listener di comunicazione viene effettivamente implementato da Service Fabric (poiché l'implementazione del servizio usa un listener di comunicazione implementato da Service Fabric).

Un servizio Reliable Services con stato usa Reliable State Manager per sfruttare i vantaggi offerti dalle raccolte Reliable Collections. Le raccolte Reliable Collections sono strutture dati locali altamente disponibili per il servizio, ovvero sono sempre disponibili indipendentemente dai failover del servizio. Ogni tipo di raccolta Reliable Collections viene implementato da un provider di stato affidabile. Per altre informazioni sulle raccolte Reliable Collections, vedere [Introduzione alle Reliable Collections](service-fabric-reliable-services-reliable-collections.md).

### Reliable state manager e provider di stato
Reliable State Manager è l'oggetto che gestisce i provider di stato affidabili. Integra funzionalità che consentono di creare, eliminare, enumerare e assicurare che i provider di stato affidabili siano persistenti e a disponibilità elevata. Un'istanza di un provider di stato affidabile rappresenta un'istanza di una struttura dati persistente e a disponibilità elevata, ad esempio un dizionario o una coda.

Ogni provider di stato affidabile espone un'interfaccia che viene usata dal servizio con stato per interagire con il provider di stato stesso. L'interfaccia IReliableDictionary, ad esempio, viene usata con il dizionario affidabile (ReliableDictionary), mentre l'interfaccia IReliableQueue viene usata per interagire con la coda affidabile (ReliableQueue). Tutti i provider di stato affidabili implementano l'interfaccia IReliableState.

Reliable State Manager ha un'interfaccia denominata IReliableStateManager, che consente l'accesso da un servizio con stato. Le interfacce per i provider di stato affidabili vengono restituite tramite IReliableStateManager.

Reliable State Manager usa un'architettura plug-in dinamica che consente di integrare dinamicamente nuovi tipi di raccolte Reliable Collections.

Gli oggetti ReliableDictionary e ReliableQueue sono basati sull'implementazione di un archivio differenziale ad alte prestazioni con controllo delle versioni.

### Transactional Replicator
Il componente Transactional Replicator deve garantire che lo stato del servizio, ovvero lo stato all'interno di Reliable State Manager e Reliable Collections, sia coerente in tutte le repliche che eseguono il servizio e che sia inoltre persistente nel log. Reliable State Manager interagisce con Transactional Replicator tramite un meccanismo privato.

Transactional Replicator usa un protocollo di rete per comunicare lo stato alle altre repliche dell'istanza del servizio, in modo che tutte le repliche abbiano informazioni aggiornate sullo stato.

Transactional Replicator usa un log per rendere persistenti le informazioni sullo stato, in modo che queste ultime non vengano danneggiate da eventuali arresti anomali dei processi o dei nodi. L'interazione con il log avviene tramite un meccanismo privato.

### Log
Il componente Log offre un archivio persistente ad alte prestazioni che può essere ottimizzato sia per la scrittura su unità disco rigido o SSD (Solid State Disk) La progettazione del componente Log è finalizzata a rendere l'archiviazione persistente (ad es. i dischi rigidi) locale rispetto ai nodi che eseguono il servizio con stato, in modo da consentire una più bassa latenza e una maggiore velocità effettiva rispetto all'archiviazione persistente remota, non locale rispetto al nodo.

Il componente Log usa più file di log. Esiste un file di log condiviso a livello di nodo utilizzato da tutte le repliche perché è in grado di fornire la latenza minima e la massima velocità effettiva per l'archiviazione dei dati sullo stato. Per impostazione predefinita il log condiviso viene posizionato nella directory di lavoro del nodo Service Fabric ma, può anche essere configurato per essere posizionato in un altro percorso, preferibilmente su un disco riservato solo al log condiviso. Ogni replica del servizio dispone inoltre di un file di log dedicato che si trova all'interno della directory di lavoro del servizio. Non esiste alcun meccanismo per configurare il log dedicato in modo da posizionarlo in un percorso diverso.

Il log condiviso è un'area di transizione per le informazioni sullo stato della replica, mentre il file di log dedicato è la destinazione finale in cui le informazioni vengono rese persistenti. In questa progettazione le informazioni sullo stato vengono prima scritte nel file di log condiviso e quindi spostate in background al file di log dedicato. In questo modo, la scrittura nel log condiviso dovrebbe presentare il più basso livello di latenza e il più alto livello di velocità effettiva, così da rendere più rapido lo stato di avanzamento del servizio.

Letture e scritture nel log condiviso vengono eseguite tramite IO diretto nello spazio su disco preassegnato per il file di log condiviso. Per consentire un utilizzo ottimale dello spazio su disco nell'unità con log dedicati, il file di log dedicato viene creato come file NTFS sparse. Questo permetterà l'overprovisioning dello spazio su disco e il sistema operativo visualizzerà i file del log dedicato utilizzando molto più spazio su disco rispetto a quello effettivamente utilizzato.

Ad eccezione di un'interfaccia utente minima usata per l'interazione con il componente Log, il file di log viene scritto come driver in modalità kernel. L'esecuzione del log come driver in modalità kernel può assicurare prestazioni elevate a tutti i servizi che lo usano.

Per altre informazioni sulla configurazione del log, vedere [Configurazione di Reliable Services con stato](service-fabric-reliable-services-configuration.md).

## Reliable Services senza stato
### Architettura di un servizio senza stato
![Diagramma dell'architettura di un servizio senza stato](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### Reliable Services senza stato
Le implementazioni di un servizio senza stato derivano dalla classe StatelessService o StatelessServiceBase, che consente una maggiore flessibilità rispetto alla classe StatelessService. Entrambe le classi di base gestiscono la durata e il ruolo di un servizio.

L'implementazione del servizio può eseguire l'override dei metodi virtuali di una delle classi di base se il servizio deve eseguire operazioni in quelle fasi del ciclo di vita del servizio o se deve creare un oggetto CommunicationListener. Si noti che, anche se il servizio può implementare il proprio oggetto CommunicationListener esponendo ICommunicationListener, come mostrato nel diagramma precedente, il listener di comunicazione viene effettivamente implementato da Service Fabric (poiché l'implementazione del servizio usa un listener di comunicazione implementato da Service Fabric).

Per altre informazioni sulle specifiche di sviluppo di servizi con le classi StatelessService e StatelessServiceBase, vedere [Panoramica di Reliable Services](service-fabric-reliable-services-introduction.md) e [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
Per altre informazioni su Service Fabric, vedere:

[Panoramica di Reliable Services](service-fabric-reliable-services-introduction.md)

[Avvio rapido](service-fabric-reliable-services-quick-start.md)

[Introduzione alle Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)

[Configurazione di Reliable Services con stato](service-fabric-reliable-services-configuration.md)

<!---HONumber=AcomDC_0406_2016-->