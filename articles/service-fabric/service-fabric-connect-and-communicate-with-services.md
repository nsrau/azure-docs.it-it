<properties
   pageTitle="Microsoft Azure Service Fabric - Come comunicare con i servizi"
   description="Questo articolo illustra come connettersi e comunicare con i servizi nelle applicazioni di Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/29/2015"
   ms.author="kunalds"/>


# Comunicazione con i servizi
Nel mondo dei microservizi la soluzione di base è costituita da molti servizi diversi, ognuno dei quali esegue un'attività specializzata. Tali microservizi comunicano tra loro per consentire il flusso di lavoro end-to-end. Sono inoltre presenti applicazioni client che si connettono ai servizi e comunicano con essi. Questo documento illustra com'è semplice configurare la comunicazione con i servizi scritti con Service Fabric.

## Concetti principali
Quando si configura la comunicazione con i servizi, è necessario tenere presenti alcuni concetti principali.
### Comunicazione rappresentata come client-server
Le API di comunicazione di Service Fabric rappresentano un'interazione di tipo client-servizio, anche nel caso in cui avvenga tra due servizi in esecuzione nello stesso cluster. Un servizio di destinazione a cui si effettua la connessione da un client o da un altro servizio funge da server e resta in ascolto delle richieste in arrivo. Il client, che può essere anche un altro servizio del cluster, si connette al server ed effettua le chiamate.
### Spostamento dei servizi
In un sistema distribuito le istanze dei servizi eseguite possono spostarsi nel tempo da un computer a un altro per diverse ragioni, ad esempio per il bilanciamento del carico se configurato con apposite metriche per il bilanciamento delle risorse o a seguito di aggiornamenti, failover, implementazione della scalabilità orizzontale e altre situazioni di varia natura. Gli indirizzi degli endpoint di un'istanza del servizio pertanto possono cambiare e, per configurare la comunicazione con essa, deve essere eseguito il loop seguente. Se si usano le API di comunicazione offerte da Service Fabric, l'astrazione di questi dettagli viene eseguita automaticamente.

* **Risoluzione**: tutte le istanze dei servizi in Service Fabric dispongono di URI univoci, ad esempio "fabric:/MyApplication/MyService", che restano invariati nel corso del tempo. Ogni istanza del servizio inoltre espone endpoint che possono cambiare quando tali istanze vengono spostate. Questa situazione è analoga a quella dei siti Web che dispongono di URL costanti, mentre l'indirizzo IP può variare. Lo stesso accade anche per il sistema DNS nel World Wide Web che risolve gli URL dei siti Web in indirizzi Web. Anche la piattaforma Service Fabric dispone di un servizio di sistema in grado di risolvere gli URI negli endpoint. Questo passaggio comporta la risoluzione dell'URI dell'istanza del servizio in un endpoint.

* **Connessione**: dopo che l'URI del servizio è stato risolto negli indirizzi degli endpoint, il passaggio successivo consiste nel tentare una connessione a tale servizio. La connessione può non riuscire se l'indirizzo dell'endpoint è cambiato a seguito di uno spostamento del servizio, causato ad esempio da un malfunzionamento del computer o dal bilanciamento del carico.

* **Ripetizione**: se il tentativo di connessione ha esito negativo, è necessario ritentare i passaggi di risoluzione e connessione precedenti e tale ciclo viene ripetuto finché la connessione non riesce.

## Scelta dell'API di comunicazione da usare
Con Service Fabric sono disponibili diverse opzioni per le API di comunicazione e la scelta di quella più adatta alle proprie esigenze dipende dal modello di programmazione adottato, dal framework di comunicazione e dal linguaggio di programmazione usato per scrivere i servizi.
### Comunicazione per Reliable Actors
Per i servizi scritti usando l'API Reliable Actors, viene eseguita l'astrazione di tutti i dettagli di comunicazione, la quale avviene non appena il metodo chiama ActorProxy.

### Opzioni di comunicazione per Reliable Services
Se il servizio è stato scritto usando l'API Reliable Services, sono disponibili due opzioni diverse. La decisione relativa alle API di comunicazione di Service Fabric utilizzabili dipenderà dal protocollo di comunicazione scelto.

* **Non è necessario usare un protocollo di comunicazione specifico e si vuole partire rapidamente con le operazioni**: se non si ha una preferenza particolare per il framework di comunicazione, l'opzione ideale è lo [stack predefinito](service-fabric-reliable-services-communication-default.md), che consente di usare un modello simile al modello di comunicazione basato su attori. Questo è il modo più semplice e veloce per iniziare a comunicare con i servizi. Garantisce una comunicazione RPC fortemente tipizzata che esegue l'astrazione della maggior parte dei dettagli di comunicazione, in modo tale che il richiamo di un metodo in un servizio remoto nel codice risulti simile alla chiamata di un metodo in un'istanza di oggetto locale. Queste classi eseguono l'astrazione della risoluzione, della connessione, della ripetizione di tali passaggi e della gestione degli errori e al contempo configurano il canale di comunicazione, consentendo di usare un modello di comunicazione basato sulle chiamate ai metodi. Per questo motivo è necessario usare la classe `ServiceCommunicationListener` per il lato server e la classe `ServiceProxy` sul lato client della comunicazione.

* **HTTP**: per usufruire della flessibilità offerta dalla comunicazione basata su HTTP, è possibile usare le API di comunicazione di Service Fabric che consentono di definire il meccanismo di comunicazione, mentre viene ancora eseguita automaticamente l'astrazione della logica di risoluzione, connessione e ripetizione. Ad esempio, è possibile usare l'API Web per specificare il meccanismo di comunicazione e sfruttare le [classi `ICommunicationClient` e `ServicePartitionClient`](service-fabric-reliable-services-communication.md) per configurare la comunicazione.
* **WCF**: se nel codice esistente viene usato WCF come framework di comunicazione, è possibile scegliere WcfCommunicationListener per il lato server e le classi WcfCommunicationClient e ServicePartitionClient per il client. Per altri dettagli, vedere [questo articolo](service-fabric-reliable-services-communication-wcf.md).

* **Altri framework di comunicazione con protocolli personalizzati**: se si intende usare qualsiasi altro framework di comunicazione con protocolli di comunicazione personalizzati, sono disponibili API di comunicazione di Service Fabric in cui inserire come plug-in il proprio stack di comunicazione mentre viene eseguita automaticamente l'astrazione di tutto il lavoro necessario per l'individuazione e la connessione. Per altri dettagli, vedere [questo articolo](service-fabric-reliable-services-communication.md).

### Comunicazione tra servizi scritti in linguaggi di programmazione diversi
Tutte le API di comunicazione di Service Fabric attualmente sono disponibili solo in linguaggio C#. Se pertanto si dispone di un servizio scritto in un altro linguaggio di programmazione, quale ad esempio Java o Node.JS, sarà necessario scrivere il proprio meccanismo di comunicazione.

## Passaggi successivi
* [Stack di comunicazione predefinito fornito dal framework Reliable Services](service-fabric-reliable-services-communication-default.md)
* [Modello di comunicazione di Reliable Services](service-fabric-reliable-services-communication.md)
* [Introduzione ai servizi delle API Web di Microsoft Azure Service Fabric con self-hosting OWIN](service-fabric-reliable-services-communication-webapi.md)
* [Stack di comunicazione basato su WCF per Reliable Services](service-fabric-reliable-services-communication-wcf.md)
 

<!---HONumber=July15_HO4-->