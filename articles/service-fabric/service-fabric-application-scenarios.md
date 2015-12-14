<properties 
   pageTitle="Scenari e progettazione di applicazioni | Microsoft Azure" 
   description="Panoramica delle categorie di applicazioni cloud nell'infrastruttura di servizi. Illustra la progettazione dell’applicazione mediante servizi con e senza stato."
   services="service-fabric" 
   documentationCenter=".net" 
   authors="msfussell" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="11/30/2015"
   ms.author="mfussell"/>

# Scenari di applicazione dell’infrastruttura di servizi

L’infrastruttura di servizi di Microsoft Azure offre una piattaforma flessibile e affidabile che consente di scrivere ed eseguire molti tipi di applicazioni e servizi aziendali. Queste applicazioni e questi microservizi possono essere con o senza stato e implementano il bilanciamento delle risorse tra le macchine virtuali per ottimizzare l'efficienza. La particolare architettura di Service Fabric consente di eseguire operazioni di analisi dei dati e di calcolo in memoria, transazioni parallele ed elaborazione degli eventi quasi in tempo reale nelle applicazioni. È possibile aumentare o ridurre facilmente il numero di applicazioni a seconda dei requisiti di risorse.

La piattaforma Service Fabric in Azure è ideale per le categorie di applicazioni e servizi seguenti:

- **Servizi a disponibilità elevata**: i servizi di Service Fabric garantiscono un failover estremamente veloce. L’infrastruttura di servizi consente di creare più repliche del servizio secondario. In caso di inattività di un nodo, processo o singolo servizio a causa di un errore hardware o altra anomalia, una delle repliche secondarie viene immediatamente innalzata di livello a replica primaria con una perdita trascurabile di servizio per i clienti. 

- **Servizi scalabili**: è possibile partizionare singoli servizi implementando la scalabilità orizzontale dello stato nel cluster. Inoltre, i singoli servizi possono essere creati e rimossi in tempo reale. I servizi possono essere aumentati facilmente e rapidamente da poche istanze su un numero ridotto di nodi a migliaia di istanze su molti nodi, e quindi ridotti nuovamente, a seconda delle esigenze delle risorse. È possibile utilizzare l'infrastruttura di servizi per creare questi servizi e gestire il relativo ciclo di vita completo.
 
- **Calcolo su dati non statici**: Service Fabric consente di creare I/O di dati, nonché applicazioni con stato e a elevato utilizzo di calcolo. L’infrastruttura di servizi consente la condivisione dell’elaborazione (calcolo) e dei dati nelle applicazioni. In genere oggi quando l'applicazione richiede l'accesso ai dati, è presente una latenza di rete associata alla cache dei dati esterni o al livello della risorsa di archiviazione. Con i servizi con stato dei servizi di infrastruttura, la latenza viene eliminata consentendo una lettura e scrittura più efficiente. Si supponga, ad esempio, di disporre di un'applicazione che esegue la selezione delle indicazioni quasi in tempo reale per i clienti con un requisito di tempo di round trip inferiore a 100 ms. Le caratteristiche di latenza e prestazioni dei servizi di infrastruttura (dove il calcolo della selezione delle indicazioni è condiviso dai dati e dalle regole) offrono all'utente un'esperienza reattiva rispetto al modello di implementazione standard con cui si devono recuperare i dati necessari dall'archivio remoto.
 
- **Applicazioni interattive basate sulla sessione**: Service Fabric si rivela utile se le applicazioni, ad esempio un gioco online o la messaggistica istantanea, richiedono operazioni di lettura e scrittura con bassa latenza. Service Fabric consente di creare queste applicazioni interattive con stato senza dover creare un archivio o una cache separata, necessaria invece per le app senza stato, aumentando la latenza e con il rischio di introdurre errori di coerenza.
 
- **Elaborazione di grafici distribuiti**: con l'espansione dei social network è aumentata la necessità di analizzare grafici su larga scala in parallelo. Le funzionalità di scalabilità rapida e di elaborazione di carichi in parallelo rendono Service Fabric la piattaforma naturale per grafici su larga scala. Service Fabric consente di creare servizi a elevata scalabilità per gruppi, come ad esempio social networking, business intelligence e ricerche scientifiche.
 
- **Analisi dei dati e flussi di lavoro**: le operazioni di lettura/scrittura rapide di Service Fabric abilitano applicazioni che devono elaborare in modo affidabile eventi o flussi di dati. Service Fabric abilita anche applicazioni che descrivono una pipeline di elaborazione, dove i risultati devono essere affidabili e passare senza perdite di dati alla fase di elaborazione successiva, inclusi sistemi transazionali e finanziari in cui la coerenza dei dati e le garanzie di calcolo sono essenziali.

## Progettazione di applicazioni costituite da microservizi con e senza stato
La creazione di applicazioni con ruoli di lavoro del servizio cloud di Azure è un esempio di servizi senza stato. Al contrario, i microservizi con stato mantengono lo stato autorevole oltre la richiesta e la risposta, fornendo disponibilità elevata e coerenza dello stato tramite semplici API che forniscono garanzie transazionali sostenute dalla replica. I servizi con stato dell’infrastruttura di servizi democratizzano la disponibilità elevata (HA), per tutti i tipi di applicazioni, non solo per database e altri archivi di dati. Si tratta di una progressione naturale: le applicazioni sono già passate dall'utilizzo di database esclusivamente relazionali per la disponibilità elevata, a database NoSQL; ora le applicazioni stesse possono avere lo stato "attivo" e i dati gestiti all'interno di essi per ulteriori miglioramenti delle prestazioni senza compromettere disponibilità, affidabilità o la coerenza.

Quando si creano applicazioni costituite da microservizi, si dispone in genere di una combinazione di app Web senza stato (ASP.NET, node.js e così via) che effettuano chiamate a servizi aziendali di livello intermedio con e senza stato, tutti distribuiti nello stesso cluster di Service Fabric con i comandi di distribuzione di Service Fabric. Ognuno di questi servizi è indipendente per quanto riguarda l'utilizzo di scalabilità, affidabilità e risorse, migliorando notevolmente la flessibilità dello sviluppo e della gestione del ciclo di vita.
  
I microservizi con stato semplificano la progettazione delle applicazioni poiché non è più necessario usare code e cache aggiuntive, che invece erano necessarie in passato per soddisfare i requisiti di disponibilità e latenza di un'applicazione interamente senza stato. L'elevata disponibilità e la bassa latenza dei servizi con stato implicano un uso ridotto di componenti mobili da gestire in un'applicazione nel suo insieme. I diagrammi seguenti illustrano le differenze tra la progettazione di un'applicazione senza stato rispetto alla progettazione di un'applicazione con stato. Sfruttando i modelli di programmazione [Reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors](service-fabric-reliable-actors-introduction.md), i servizi con stato semplificano la complessità delle applicazioni, pur garantendo un'elevata velocità effettiva e una bassa latenza.

## Applicazione creata con servizi senza stato##
![Applicazione che usa un servizio senza stato][Image1]

## Applicazione creata con servizi con stato##
![Applicazione che usa un servizio senza stato][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi


Per iniziare a creare servizi senza e con stato, usare i modelli di programmazione [Reliable Services](service-fabric-reliable-services-quick-start.md) e [Reliable Actors](service-fabric-reliable-actors-get-started.md) di Service Fabric.

Vedere anche gli argomenti seguenti:

[Informazioni sui microservizi](service-fabric-overview-microservices.md)

[Definizione e gestione dello stato di un servizio](service-fabric-concepts-state.md)

[Disponibilità dei servizi](service-fabric-availability-services.md)

[Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)

[Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=AcomDC_1203_2015-->