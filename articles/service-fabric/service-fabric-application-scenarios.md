---
title: Scenari e progettazione di applicazioni | Microsoft Docs
description: Panoramica delle categorie di applicazioni cloud nell'infrastruttura di servizi. Illustra la progettazione di applicazioni con servizi con e senza stato.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: 6563d6e7c454f44e1a70d725191e56d3f90315c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052593"
---
# <a name="service-fabric-application-scenarios"></a>Scenari di applicazione di Service Fabric
Azure Service Fabric offre una piattaforma flessibile e affidabile in cui è possibile scrivere ed eseguire molti tipi di applicazioni e servizi aziendali. Queste applicazioni e i microservizi possono essere con o senza stato e risultano bilanciato su risorse tra le macchine virtuali per ottimizzare l'efficienza. 

La particolare architettura di Service Fabric consente di eseguire operazioni di analisi dei dati e di calcolo in memoria, transazioni parallele ed elaborazione degli eventi quasi in tempo reale nelle applicazioni. È possibile aumentare o ridurre facilmente il numero di applicazioni a seconda dei requisiti di risorse.

Per indicazioni di progettazione sulla compilazione di applicazioni, leggere [architettura di Microservizi in Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) e [procedure consigliate per la progettazione dell'applicazione usando Service Fabric](service-fabric-best-practices-applications.md).

È consigliabile usare la piattaforma Service Fabric per i tipi di applicazioni seguenti:

* **La raccolta dei dati, elaborazione e IoT**: Service Fabric gestisce su larga scala e con bassa latenza tramite i servizi con stati. Consente di elaborare i dati in milioni di dispositivi in cui vengono collocati i dati per il dispositivo e il calcolo.

    I clienti hanno realizzato servizi IoT usando Service Fabric includono [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [costruzione di libreria di classi Portabile](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), e [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Giochi e basati su sessione applicazioni interattive**: Service Fabric è utile se l'applicazione richiede a bassa latenza letture e scritture, ad esempio in modalità di gioco online o messaggistica istantanea. Service Fabric consente di compilare queste applicazioni interattive con stato senza dover creare appositi archivi o cache. Visita [soluzioni giochi di Azure](https://azure.microsoft.com/solutions/gaming/) per indicazioni di progettazione sulla [usando Service Fabric in servizi per i giochi](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    I clienti che hanno creato giochi servizi includono [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) e [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). I clienti che hanno creato le sessioni interattive di includono [Honeywell con Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Analitica dei dati e l'elaborazione del flusso di lavoro**: Applicazioni che devono elaborare in modo affidabile eventi o flussi di dati sfruttano le operazioni di lettura ottimizzate e le scritture in Service Fabric. Service Fabric supporta anche la pipeline di elaborazione dell'applicazione, in cui i risultati devono essere affidabili e passati alla fase di elaborazione successiva senza alcuna perdita. Queste pipeline includono sistemi transazionali e finanziari, in cui garanzie di coerenza e calcolo dei dati sono essenziali.

    I clienti che hanno creato i servizi del flusso di lavoro di business includono [gruppo Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric), e [Société generale](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Calcolo sui dati**: Service Fabric consente di creare applicazioni con state che eseguire calcoli di grandi quantità di dati. Service Fabric consente la condivisione del percorso di elaborazione (calcolo) e i dati nelle applicazioni. 

   In genere, quando l'applicazione richiede l'accesso ai dati, la latenza di rete associata a un livello di cache o l'archiviazione di dati esterni limita il tempo di calcolo. Servizi di Service Fabric con stato eliminano tale latenza, consentendo più adatte letture e scritture. 
   
   Ad esempio, si consideri un'applicazione che esegue accanto alla selezione delle raccomandazioni in tempo reale per i clienti, con un requisito di tempo di round trip di meno di 100 millisecondi. Le caratteristiche di latenza e prestazioni dei servizi di Service Fabric forniscono un'esperienza reattiva all'utente, confrontato con il modello di implementazione standard di dover recuperare i dati necessari da un archivio remoto. Il sistema è più reattivo perché il calcolo della selezione delle raccomandazioni è con percorso condiviso con i dati e le regole.

    I clienti che hanno creato servizi di calcolo includono [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) e [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Servizi a disponibilità elevata**: Service Fabric fornisce failover veloce tramite la creazione di più repliche secondarie del servizio. In caso di inattività di un nodo, di un processo o di un singolo servizio a causa di un errore hardware o di un'altra anomalia, una delle repliche secondarie viene innalzata di livello a replica primaria con una perdita trascurabile di servizio.

* **Servizi scalabili**: è possibile partizionare i singoli servizi implementando l'aumento del numero di istanze per lo stato nel cluster. Singoli servizi possono anche essere creati e rimossi in tempo reale. È possibile scalare orizzontalmente i servizi da poche istanze su alcuni nodi a migliaia di istanze su molti nodi e quindi il ridimensionamento in base alle esigenze. È possibile usare Service Fabric per compilare questi servizi e gestire i cicli di vita completati.

## <a name="application-design-case-studies"></a>Case study sulla progettazione delle applicazioni
Case study che mostrano come Service Fabric consente di progettare le applicazioni sono pubblicate nella [esperienze dei clienti](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) e [Microservizi in Azure](https://azure.microsoft.com/solutions/microservice-applications/) siti.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Progettazione di applicazioni costituite da microservizi con e senza stati
Creazione di applicazioni con ruoli di lavoro di servizi Cloud di Azure è un esempio di un servizio senza stato. Al contrario, i microservizi con stato mantengono lo stato autorevole oltre alla richiesta e alla relativa risposta. Questa funzionalità fornisce disponibilità elevata e coerenza dello stato attraverso semplici API che forniscono garanzie transazionali supportate dalla replica. 

I servizi con stato in Service Fabric offrono disponibilità elevata a tutti i tipi di applicazioni, non solo database e altri archivi dati. Si tratta di un progresso naturale. Le applicazioni sono già passate dall'uso di database puramente relazionali per la disponibilità elevata a database NoSQL. Ora le applicazioni stesse possono avere un proprio stato "attivo" e i dati possono essere gestiti al loro interno per migliorare ancora le prestazioni senza compromettere l'affidabilità, la coerenza o la disponibilità.

Quando crei applicazioni costituite da microservizi, in genere è una combinazione di App web senza stato (ad esempio, ASP.NET e Node. js) chiamata in servizi di livello intermedio di business con e senza stato. L'App e servizi vengono distribuiti nello stesso cluster di Service Fabric tramite i comandi di distribuzione di Service Fabric. Ognuno di questi servizi è indipendente per quanto riguarda scalabilità, affidabilità e uso di risorse. Questa indipendenza migliora l'agilità e flessibilità per lo sviluppo e gestione del ciclo di vita.

I microservizi con stato semplificano la progettazione delle applicazioni perché eliminano la necessità di usare code e cache aggiuntive, che invece erano necessarie in passato per soddisfare i requisiti di disponibilità e latenza di applicazioni interamente senza stato. Poiché i servizi con stato hanno la disponibilità elevata e bassa latenza, esistono meno dettagli per la gestione dell'applicazione. 

I diagrammi seguenti illustrano le differenze tra la progettazione di un'applicazione che è senza stata e uno con stato. Sfruttando i modelli di programmazione [Reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors](service-fabric-reliable-actors-introduction.md), i servizi con stato riducono la complessità delle applicazioni e allo stesso tempo garantiscono un'elevata velocità effettiva e una bassa latenza.

Ecco un'applicazione di esempio che usa i servizi senza stato: ![Applicazione che usa i servizi senza stato][Image1]

Ecco un'applicazione di esempio che usa i servizi con stato: ![Applicazione che usa i servizi senza stato][Image2]

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [modelli e scenari](service-fabric-patterns-and-scenarios.md).

* Iniziare a creare servizi con e senza stati con Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) e [Reliable Actors](service-fabric-reliable-actors-get-started.md) modelli di programmazione.
* Visita il centro architetture Azure per indicazioni su [creazione di microservizi in Azure](https://docs.microsoft.com/azure/architecture/microservices/).
* Passare a [procedure consigliate per applicazioni di Azure Service Fabric e cluster](service-fabric-best-practices-overview.md) per indicazioni di progettazione dell'applicazione.

* Vedere anche gli argomenti seguenti:
  * [Informazioni sui microservizi](service-fabric-overview-microservices.md)
  * [Definire e gestire lo stato del servizio](service-fabric-concepts-state.md)
  * [Disponibilità dei servizi di Service Fabric](service-fabric-availability-services.md)
  * [Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)
  * [Partizionare i servizi di Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
