<properties 
   pageTitle="Scenari e progettazione di applicazioni con Service Fabric" 
   description="Categorie di applicazioni. Progettazione di applicazioni con servizi con e senza stato" 
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
   ms.date="04/24/2015"
   ms.author="mfussell"/>

# Scenari di applicazioni di Service Fabric

Service Fabric e Azure offrono una piattaforma di infrastrutture cloud flessibile e affidabile che consente di eseguire molti tipi di applicazioni e servizi aziendali. Queste applicazioni e questi microservizi possono essere con o senza stato e implementano il bilanciamento delle risorse tra le macchine virtuali per ottimizzare l'efficienza. La particolare architettura di Service Fabric consente di eseguire operazioni di analisi dei dati e di calcolo in memoria, transazioni parallele ed elaborazione degli eventi quasi in tempo reale nelle applicazioni. È possibile aumentare o ridurre facilmente il numero di applicazioni a seconda dei requisiti di risorse.

La piattaforma Service Fabric in Azure è ideale per le categorie di applicazioni e servizi seguenti:

- **Servizi a disponibilità elevata**: i servizi di Service Fabric garantiscono un failover estremamente veloce. Restano disponibili più repliche di servizi secondarie. In caso di inattività di un nodo a causa di un errore hardware, una delle repliche secondarie viene immediatamente innalzata di livello a replica primaria con una perdita trascurabile di servizio per i clienti. Il numero di servizi può essere facilmente e rapidamente aumentato da poche istanze a migliaia di istanze e quindi ridotto a poche istanze, a seconda delle esigenze delle risorse. È possibile usare Service Fabric per creare e gestire il ciclo di vita di questi servizi cloud scalabili.

- **Servizi scalabili**: è possibile partizionare singoli servizi implementando la scalabilità orizzontale dello stato nel cluster. È anche possibile creare più servizi di Service Fabric e implementarne la scalabilità orizzontale.
 
- **Calcolo su dati non statici**: Service Fabric consente di creare I/O di dati, nonché applicazioni con stato e a elevato utilizzo di calcolo. Poiché le risorse di elaborazione e dati nelle applicazioni di Service Fabric sono posizionate insieme, quando un'applicazione richiede operazioni di lettura e scrittura rapide, la latenza di rete associata a un livello di archiviazione o a una cache di dati esterna viene eliminata. Si supponga ad esempio di disporre di un'applicazione che esegue una selezione di annunci pubblicitari quasi in tempo reale con un tempo di round trip < 100 ms. In questo caso il calcolo delle regole di selezione degli annunci pubblicitari fornisce prestazioni ottimali all'utente.
 
- **Applicazioni interattive basate sulla sessione**: Service Fabric si rivela utile se le applicazioni, ad esempio un gioco online o la messaggistica istantanea, richiedono operazioni di lettura e scrittura con bassa latenza. Service Fabric consente di creare queste applicazioni interattive con stato senza dover creare un archivio o una cache separata, necessaria invece per le app senza stato.
 
- **Elaborazione di grafici distribuiti**: con l'espansione dei social network è aumentata la necessità di analizzare grafici su larga scala in parallelo. Le funzionalità di scalabilità rapida e di elaborazione di carichi in parallelo rendono Service Fabric la piattaforma naturale per grafici su larga scala. Service Fabric consente di creare servizi a elevata scalabilità per gruppi, come ad esempio social networking, business intelligence e ricerche scientifiche.
 
- **Analisi dei dati e flussi di lavoro**: le operazioni di lettura/scrittura rapide di Service Fabric abilitano applicazioni che devono elaborare in modo affidabile eventi o flussi di dati. Service Fabric abilita anche applicazioni che descrivono una pipeline di elaborazione, dove i risultati devono essere affidabili e passare senza perdite di dati alla fase di elaborazione successiva, inclusi sistemi transazionali e finanziari in cui la coerenza dei dati e le garanzie di calcolo sono essenziali.

## Progettazione di applicazioni costituite da microservizi con e senza stato ##
La creazione di applicazioni con ruoli di lavoro del servizio cloud di Azure è un esempio di servizi senza stato. Al contrario, i microservizi con stato mantengono lo stato autorevole oltre alla richiesta e alla relativa risposta, fornendo tuttavia la replica dello stato mediante semplici API. I servizi con stato sono la democratizzazione della disponibilità elevata per tutte le applicazioni e non solo per i database e altri archivi dati. Questo è il naturale progresso della progettazione, poiché le applicazioni sono passate dall'uso di soli database relazionali per la disponibilità elevata all'uso di database non SQL e ora alle applicazioni stesse, in cui lo stato è gestito internamente per i dati "critici" da tenere sempre a disposizione per il calcolo.

Quando si creano applicazioni costituite da microservizi, si dispone in genere di una combinazione di app Web senza stato (ASP.NET, node.js e così via) che effettuano chiamate a servizi aziendali di livello intermedio con e senza stato, tutti distribuiti nello stesso cluster di Service Fabric con i comandi di distribuzione di Service Fabric. L'indipendenza della scalabilità. dell'affidabilità e dell'uso delle risorse per ognuno di questi microservizi garantisce questa flessibilità di sviluppo e di gestione del ciclo di vita.
  
I microservizi con stato semplificano la progettazione delle applicazioni poiché non è più necessario usare code e cache aggiuntive, che invece erano necessarie in passato per soddisfare i requisiti di disponibilità e latenza di un'applicazione interamente senza stato. L'elevata disponibilità e la bassa latenza dei servizi con stato implicano un uso ridotto di componenti mobili da gestire in un'applicazione nel suo insieme. I diagrammi seguenti illustrano le differenze tra la progettazione di un'applicazione senza stato rispetto alla progettazione di un'applicazione con stato. Nel caso dell'applicazione con stato l'uso dei modelli di programmazione [Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md) e [Reliable Actors](service-fabric-reliable-actors-introduction.md) comporta per i servizi con stato una semplificazione della complessità delle applicazioni, pur garantendo un'elevata velocità effettiva e una bassa latenza.

## Applicazione creata con servizi senza stato##
![Applicazione che usa un servizio senza stato][Image1]

## Applicazione creata con servizi con stato##
![Applicazione che usa un servizio senza stato][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi


Per iniziare a creare servizi senza e con stato, usare i modelli di programmazione [Reliable Services](service-fabric-reliable-services-quick-start.md) e [Reliable Actors](service-fabric-reliable-actors-get-started.md) di Service Fabric.

Vedere anche gli argomenti seguenti:

[Definizione e gestione dello stato di un servizio](service-fabric-concepts-state.md)

[Disponibilità dei servizi](../service-fabric-concepts-availability-services.md)

[Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)

[Partizionamento dei servizi di Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=July15_HO4-->