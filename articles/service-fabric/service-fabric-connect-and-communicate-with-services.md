<properties
   pageTitle="Comunicare con i servizi e connettersi ad essi in Azure Service Fabric | Microsoft Azure"
   description="Informazioni su come connettersi e comunicare con i servizi nelle applicazioni di Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# Comunicare con i servizi
Nel mondo dei microservizi, una soluzione completa è costituita da molti servizi diversi, ognuno dei quali esegue un'attività specializzata. Questi microservizi comunicano tra loro per consentire il flusso di lavoro end-to-end. Sono inoltre presenti applicazioni client che si connettono ai servizi e comunicano con essi. Questo documento dimostra come Azure Service Fabric consenta di configurare facilmente la comunicazione con i servizi scritti con Service Fabric.

## Concetti chiave
Quando si configura la comunicazione con i servizi, è necessario tenere presenti alcuni concetti essenziali.

### Comunicazione rappresentata come client-server
Le API di comunicazione di Service Fabric rappresentano interazioni di tipo client-server, anche se si verificano tra due servizi in esecuzione sullo stesso cluster. Un servizio di destinazione, a cui si effettua la connessione da un client o da un altro servizio, svolge la funzione di server e resta in ascolto delle richieste in arrivo. Il client, che può essere anche un altro servizio del cluster, si connette al server ed effettua le chiamate.

### Spostamento dei servizi
In un sistema distribuito, le istanze di servizio eseguite possono spostarsi nel tempo da un computer a un altro per vari motivi, ad esempio per il bilanciamento delle risorse (se configurato con metriche di caricamento per il bilanciamento delle risorse) o in caso di aggiornamenti, failover e scalabilità orizzontale. Di conseguenza, gli indirizzi degli endpoint di un'istanza di servizio possono cambiare e, per configurare la comunicazione con l'istanza, è necessario eseguire il loop seguente. Se si usano le API di comunicazione offerte da Service Fabric, l'astrazione di questi dettagli viene eseguita automaticamente.

* **Risoluzione**: tutte le istanze di servizi denominati in Service Fabric hanno URI univoci. Ad esempio, l'istanza `"fabric:/MyApplication/MyService"` rimane fissa per il servizio denominato. Ogni istanza di servizio denominato, inoltre, espone endpoint che possono cambiare quando le istanze vengono spostate, analogamente ai siti Web che hanno URL costanti, ma il cui indirizzo IP può cambiare. E come il sistema DNS sul Web, che risolve gli URL dei siti Web in indirizzi IP, Service Fabric ha un servizio di sistema, Naming Service, in grado di risolvere gli URI in endpoint. Questo passaggio comporta la risoluzione dell'URI dell'istanza del servizio in un endpoint.

* **Connessione**: dopo che l'URI del servizio denominato è stato risolto in un indirizzo di endpoint, il passaggio successivo consiste nel tentare una connessione al servizio. La connessione può non riuscire se l'indirizzo dell'endpoint è cambiato a seguito di uno spostamento del servizio, causato ad esempio da un malfunzionamento del computer o dal bilanciamento delle risorse.

* **Ripetizione**: se il tentativo di connessione ha esito negativo, è necessario ritentare i passaggi di risoluzione e connessione precedenti finché la connessione non riesce.

## Opzioni delle API di comunicazione
Con Service Fabric vengono fornite anche diverse opzioni per le API di comunicazione, la cui scelta dipende dal modello di programmazione adottato, dal framework di comunicazione e dal linguaggio di programmazione usato per scrivere i servizi.

### Comunicazione per Reliable Actors
Per i servizi scritti usando l'API Reliable Actors, viene eseguita l'astrazione di tutti i dettagli di comunicazione, che avviene non appena il metodo chiama ActorProxy. In questo caso, quindi, è possibile interrompere qui la lettura.

### Opzioni di comunicazione per Reliable Services
Se il servizio è stato scritto usando l'API Reliable Services, sono disponibili due diverse opzioni. La decisione in merito alle API di comunicazione di Service Fabric da usare dipenderà dal protocollo di comunicazione scelto.

* **Nessun protocollo specifico**: se non si ha una preferenza particolare per il framework di comunicazione, ma si vuole essere immediatamente operativi, l'opzione ideale è lo [stack predefinito](service-fabric-reliable-services-communication-remoting.md), che consente di usare un modello simile al modello di comunicazione basato su attori. Questo è il modo più semplice e veloce per iniziare a comunicare con i servizi. Garantisce una comunicazione RPC fortemente tipizzata che esegue l'astrazione della maggior parte dei dettagli di comunicazione, in modo che il richiamo di un metodo in un servizio remoto nel codice risulti simile alla chiamata di un metodo in un'istanza di oggetto locale. Queste classi eseguono l'astrazione della risoluzione, della connessione, della ripetizione dei passaggi e della gestione degli errori e al contempo configurano il canale di comunicazione, consentendo così un modello di comunicazione basato sulle chiamate ai metodi. Per questo motivo è necessario usare la classe `ServiceRemotingListener` sul lato server e la classe `ServiceProxy` sul lato client della comunicazione.

* **HTTP**: per usufruire della flessibilità offerta dalla comunicazione basata su HTTP, è possibile usare le API di comunicazione di Service Fabric, che consentono di definire il meccanismo di comunicazione mentre è ancora in esecuzione l'astrazione della logica di risoluzione, connessione e ripetizione dei passaggi. Ad esempio, è possibile usare l'API Web per specificare il meccanismo di comunicazione e usufruire delle [classi `ICommunicationClient` e `ServicePartitionClient`](service-fabric-reliable-services-communication.md) per configurare la comunicazione.
* **WCF**: se il codice esistente usa WCF come framework di comunicazione, è possibile scegliere `WcfCommunicationListener` per il lato server e le classi `WcfCommunicationClient` e `ServicePartitionClient` per il client. Per altre informazioni, vedere l'articolo [Stack di comunicazione basato su WCF per Reliable Services](service-fabric-reliable-services-communication-wcf.md).

* **Altri framework di comunicazione con protocolli personalizzati**: se si intende usare qualsiasi altro framework di comunicazione con protocolli di comunicazione personalizzati, sono disponibili API di comunicazione di Service Fabric in cui poter inserire come plug-in il proprio stack di comunicazione mentre viene eseguita l'astrazione di tutto il lavoro necessario per l'individuazione e la connessione. Per altre informazioni, vedere l'articolo [Modello di comunicazione Reliable Service](service-fabric-reliable-services-communication.md).

### Comunicazione tra servizi scritti in linguaggi di programmazione diversi
Tutte le API di comunicazione di Service Fabric sono attualmente disponibili solo in linguaggio C#. Se si ha un servizio scritto in un altro linguaggio di programmazione, ad esempio Java o Node.JS, sarà quindi necessario scrivere il proprio meccanismo di comunicazione.

## Passaggi successivi
* [Stack di comunicazione predefinito fornito dal framework Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Modello di comunicazione di Reliable Services](service-fabric-reliable-services-communication.md)
* [Introduzione ai servizi API Web di Microsoft Azure Service Fabric con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md)
* [Stack di comunicazione basato su WCF per Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0224_2016-->