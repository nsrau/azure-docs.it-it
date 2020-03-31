---
title: Scenari applicativi e progettazione
description: Panoramica delle categorie di applicazioni cloud nell'infrastruttura di servizi. Illustra la progettazione di applicazioni con servizi con e senza stato.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024733"
---
# <a name="service-fabric-application-scenarios"></a>Scenari di applicazione di Service Fabric

Azure Service Fabric offre una piattaforma affidabile e flessibile in cui è possibile scrivere ed eseguire molti tipi di applicazioni e servizi aziendali. Queste applicazioni e microservizi possono essere senza stato o con stato e sono bilanciate in termini di risorse tra le macchine virtuali per massimizzare l'efficienza.

La particolare architettura di Service Fabric consente di eseguire operazioni di analisi dei dati e di calcolo in memoria, transazioni parallele ed elaborazione degli eventi quasi in tempo reale nelle applicazioni. È possibile scalare facilmente le applicazioni in base ai requisiti delle risorse in evoluzione.

Per indicazioni sulla progettazione di applicazioni, leggere Architettura dei [microservizi in Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) e Procedure consigliate per la progettazione di applicazioni [tramite Service Fabric](service-fabric-best-practices-applications.md).

Valutare la possibilità di usare la piattaforma Service Fabric per i tipi di applicazioni seguenti:Consider using the Service Fabric platform for the following types of applications:

* **Raccolta dati, elaborazione e IoT:** Service Fabric gestisce la scalabilità su larga scala e ha una latenza bassa tramite i servizi con stato. Può aiutare a elaborare i dati su milioni di dispositivi in cui i dati per il dispositivo e il calcolo sono collocati.

    I clienti che hanno costruito servizi IoT utilizzando Service Fabric includono [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)e [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Applicazioni interattive basate su giochi e sessioni:** Service Fabric è utile se l'applicazione richiede letture e scritture a bassa latenza, ad esempio nei giochi online o nella messaggistica istantanea. Service Fabric consente di compilare queste applicazioni interattive con stato senza dover creare un archivio o una cache separata. Visitare le [soluzioni](https://azure.microsoft.com/solutions/gaming/) di gioco di Azure per istruzioni di progettazione [sull'uso](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)di Service Fabric nei servizi di gioco.

    I clienti che hanno creato servizi di gioco includono [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) e [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). I clienti che hanno costruito sessioni interattive includono [Honeywell con Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Analisi dei dati ed elaborazione del flusso di lavoro:** le applicazioni che devono elaborare in modo affidabile eventi o flussi di dati traggono vantaggio dalle letture e scritture ottimizzate in Service Fabric. Service Fabric supporta anche le pipeline di elaborazione delle applicazioni, in cui i risultati devono essere affidabili e trasmessi alla fase di elaborazione successiva senza perdite. Queste pipeline includono sistemi transazionali e finanziari, in cui la coerenza dei dati e le garanzie di calcolo sono essenziali.

    I clienti che hanno creato servizi di flusso di lavoro aziendale includono il gruppo di lavoro [quorum](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)e [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Calcolo sui dati:** Service Fabric consente di creare applicazioni con stato che eseguono calcoli dati intensivi. Service Fabric consente la colocation dell'elaborazione (calcolo) e dei dati nelle applicazioni. 

   In genere, quando l'applicazione richiede l'accesso ai dati, la latenza di rete associata a una cache di dati esterna o a un livello di archiviazione limita il tempo di calcolo. I servizi di Stateful Service Fabric eliminano tale latenza, consentendo letture e scritture più ottimizzate.

   Si consideri, ad esempio, un'applicazione che esegue selezioni di raccomandazioni quasi in tempo reale per i clienti, con un requisito di tempo di andata e ritorno inferiore a 100 millisecondi. Le caratteristiche di latenza e prestazioni dei servizi service Fabric offrono all'utente un'esperienza reattiva, rispetto al modello di implementazione standard di dover recuperare i dati necessari dall'archiviazione remota. Il sistema è più reattivo perché il calcolo della selezione delle raccomandazioni è collocato con i dati e le regole.

    I clienti che hanno creato servizi di calcolo includono [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) e [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Servizi a disponibilità elevata:** Service Fabric fornisce un failover rapido creando più repliche di servizio secondarie. In caso di inattività di un nodo, di un processo o di un singolo servizio a causa di un errore hardware o di un'altra anomalia, una delle repliche secondarie viene innalzata di livello a replica primaria con una perdita trascurabile di servizio.

* **Servizi scalabili**: è possibile partizionare singoli servizi implementando la scalabilità orizzontale dello stato nel cluster. I singoli servizi possono anche essere creati e rimossi al volo. È possibile scalare i servizi da alcune istanze in pochi nodi a migliaia di istanze in molti nodi e quindi ridimensionarli di nuovo in base alle esigenze. È possibile utilizzare Service Fabric per compilare questi servizi e gestirne i cicli di vita completi.

## <a name="application-design-case-studies"></a>Case study sulla progettazione delle applicazioni

Case study che mostrano come Service Fabric viene usato per progettare le applicazioni vengono pubblicati nelle [storie dei clienti](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) e microservizi nei siti di [Azure.Case studies](https://azure.microsoft.com/solutions/microservice-applications/) that show how Service Fabric is used to design applications are published on the Customer stories and Microservices in Azure sites.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Progettazione di applicazioni composte da microservizi senza stato e con statoDesigning applications composed of stateless and stateful microservices

La creazione di applicazioni con ruoli di lavoro di Servizi cloud di Azure è un esempio di servizio senza stato. Al contrario, i microservizi con stato mantengono lo stato autorevole oltre alla richiesta e alla relativa risposta. Questa funzionalità fornisce disponibilità elevata e coerenza dello stato tramite API semplici che forniscono garanzie transazionali supportate dalla replica.

I servizi con stato in Service Fabric portano disponibilità elevata a tutti i tipi di applicazioni, non solo ai database e ad altri archivi dati. Si tratta di un progresso naturale. Le applicazioni sono già passate dall'uso di database puramente relazionali per la disponibilità elevata a database NoSQL. Ora le applicazioni stesse possono avere un proprio stato "attivo" e i dati possono essere gestiti al loro interno per migliorare ancora le prestazioni senza compromettere l'affidabilità, la coerenza o la disponibilità.

Quando si creano applicazioni costituite da microservizi, in genere si dispone di una combinazione di app Web senza stato (ad esempio ASP.NET e Node.js) che chiamano servizi di livello intermedio senza stato e con stato. Le app e i servizi vengono tutti distribuiti nello stesso cluster di Service Fabric tramite i comandi di distribuzione di Service Fabric.The apps and services are all deployed in the same Service Fabric cluster through the Service Fabric deployment commands. Ognuno di questi servizi è indipendente per quanto riguarda la scalabilità, l'affidabilità e l'utilizzo delle risorse. Questa indipendenza migliora l'agilità e la flessibilità nello sviluppo e nella gestione del ciclo di vita.

I microservizi con stato semplificano la progettazione delle applicazioni perché eliminano la necessità di usare code e cache aggiuntive, che invece erano necessarie in passato per soddisfare i requisiti di disponibilità e latenza di applicazioni interamente senza stato. Poiché i servizi con stato hanno disponibilità elevata e bassa latenza, la gestione nell'applicazione è inferiore ai dettagli.

I diagrammi seguenti illustrano le differenze tra la progettazione di un'applicazione senza stato e una che è con stato. Sfruttando i modelli di programmazione [Reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors,](service-fabric-reliable-actors-introduction.md) i servizi con stato riducono la complessità delle applicazioni ottenendo al contempo una velocità effettiva elevata e una bassa latenza.

Ecco un'applicazione di esempio che ![usa servizi senza stato: Applicazione che usa servizi senza statoHere's an example application that uses stateless services: Application that uses stateless services][Image1]

Ecco un'applicazione di esempio che ![usa servizi con stato: applicazione che usa servizi con statoHere's an example application that uses stateful services: Application that uses stateful services][Image2]

## <a name="next-steps"></a>Passaggi successivi

* Iniziare a creare servizi senza stato e con stato con i modelli di programmazione Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) e [Reliable Actors.](service-fabric-reliable-actors-get-started.md)
* Visitare il Centro architettura di Azure per istruzioni sulla [creazione di microservizi in Azure.](https://docs.microsoft.com/azure/architecture/microservices/)
* Passare alle procedure consigliate per [l'applicazione e](service-fabric-best-practices-overview.md) il cluster di Azure Service Fabric per istruzioni sulla progettazione delle applicazioni.

* Vedere anche la pagina relativa alla
  * [Informazioni sui microservizi](service-fabric-overview-microservices.md)
  * [Definire e gestire lo stato del servizio](service-fabric-concepts-state.md)
  * [Disponibilità dei servizi](service-fabric-availability-services.md)
  * [Ridimensionare i servizi](service-fabric-concepts-scalability.md)
  * [Servizi di partizione](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
