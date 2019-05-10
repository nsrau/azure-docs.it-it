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
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228494"
---
# <a name="service-fabric-application-scenarios"></a>Scenari di applicazione di Service Fabric
Azure Service Fabric offre una piattaforma flessibile e affidabile che consente di scrivere ed eseguire molti tipi di applicazioni e servizi aziendali. Le applicazioni e i microservizi possono essere con o senza stato e implementano il bilanciamento delle risorse tra le macchine virtuali per ottimizzare l'efficienza. La particolare architettura di Service Fabric consente di eseguire operazioni di analisi dei dati e di calcolo in memoria, transazioni parallele ed elaborazione degli eventi quasi in tempo reale nelle applicazioni. È possibile aumentare o ridurre facilmente il numero di applicazioni a seconda dei requisiti di risorse.

Per indicazioni di progettazione sulla compilazione di applicazioni, leggere [architettura di microservizi in Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) e [procedure consigliate per la progettazione dell'applicazione usando Service Fabric](service-fabric-best-practices-applications.md)

La piattaforma Service Fabric è ideale per i tipi di applicazioni seguenti:

* **La raccolta dei dati, elaborazione e IoT**: Poiché Service Fabric gestisce su larga scala e con bassa latenza tramite i servizi con stati, è ideale per l'elaborazione dati in milioni di dispositivi in cui vengono collocati i dati per il dispositivo e il calcolo.

    I clienti che hanno creato i servizi IoT usando Service Fabric includono [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [costruzione di libreria di classi Portabile](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), e [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Giochi e basati su sessione applicazioni interattive**: Service Fabric è ideale se l'applicazione richiede letture a bassa latenza e scritture, ad esempio in modalità di gioco online o messaggistica istantanea. Service Fabric consente di compilare queste applicazioni interattive con stato senza dover creare appositi archivi o cache. Visita [soluzioni giochi di Azure](https://azure.microsoft.com/solutions/gaming/) per indicazioni di progettazione su [usando Service Fabric in servizi per i giochi](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    I clienti che hanno creato giochi servizi includono [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) e [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). I clienti che hanno creato le sessioni interattive di includono [Honeywell con Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Analitica dei dati e l'elaborazione del flusso di lavoro**: Applicazioni che devono elaborare in modo affidabile eventi o flussi di dati inoltre vantaggio dalla ottimizzato letture e scritture in Service Fabric. Inoltre, Service Fabric supporta la pipeline di elaborazione dell'applicazione, in cui i risultati devono essere affidabili e passati alla fase di elaborazione successiva senza alcuna perdita. Sono inclusi sistemi transazionali e finanziari, in cui le garanzie di coerenza e calcolo dei dati sono essenziali.

    I clienti che hanno creato i servizi del flusso di lavoro di business includono [gruppo Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) e [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **Calcolo sui dati**: Service Fabric consente di compilare le applicazioni con stato con utilizzo intensivo di dati calcoli. Service Fabric consente la condivisione percorso dati e di elaborazione (calcolo) nelle applicazioni. In genere, quando l'applicazione richiede l'accesso ai dati, la latenza di rete associata a un livello di cache o l'archiviazione di dati esterni limita il tempo di calcolo. Con servizi di Service Fabric con stato, la latenza viene eliminata, l'abilitazione di più con ottimizzazione per la lettura e scrittura. Ad esempio, si consideri un'applicazione che esegue accanto alla selezione delle raccomandazioni in tempo reale per i clienti, con un requisito di tempo di round trip di meno di 100 millisecondi. Le caratteristiche di latenza e prestazioni dei servizi di Service Fabric (dove il calcolo della selezione delle raccomandazioni è con percorso condiviso con i dati e le regole) offre un'esperienza reattiva all'utente confrontato con il modello di implementazione standard la necessità di recuperare i dati necessari da un archivio remoto.

    I clienti che hanno creato servizi di calcolo includono [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) e [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Servizi a disponibilità elevata**: Service Fabric fornisce failover veloce tramite la creazione di più repliche secondarie del servizio. In caso di inattività di un nodo, di un processo o di un singolo servizio a causa di un errore hardware o di un'altra anomalia, una delle repliche secondarie viene innalzata di livello a replica primaria con una perdita trascurabile di servizio.

* **Servizi scalabili**: è possibile partizionare i singoli servizi implementando l'aumento del numero di istanze per lo stato nel cluster. Inoltre, i singoli servizi possono essere creati e rimossi in tempo reale. I servizi possono essere rapidamente e facilmente scalato orizzontalmente da poche istanze su alcuni nodi a migliaia di istanze su molti nodi e quindi ridotti nuovamente, a seconda delle esigenze di risorse. È possibile usare Service Fabric per compilare questi servizi e gestire i cicli di vita completati.

## <a name="application-design-case-studies"></a>Case study sulla progettazione delle applicazioni
Sono pubblicati numerosi case study sul modo in cui Service Fabric consente di progettare le applicazioni in [esperienze dei clienti](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) e il [sito delle soluzioni di microservizi](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Progettare applicazioni costituite da microservizi con e senza stato
La compilazione di applicazioni con ruoli di lavoro del servizio cloud di Azure è un esempio di servizio senza stato. Al contrario, i microservizi con stato mantengono lo stato autorevole oltre alla richiesta e alla relativa risposta. Questa funzionalità fornisce disponibilità elevata e coerenza dello stato attraverso semplici API che forniscono garanzie transazionali supportate dalla replica. I servizi con stato di Service Fabric portano la disponibilità elevata a tutti i tipi di applicazioni, non solo a database e altri archivi di dati. Si tratta di un progresso naturale. Le applicazioni sono già passate dall'uso di database puramente relazionali per la disponibilità elevata a database NoSQL. Ora le applicazioni stesse possono avere un proprio stato "attivo" e i dati possono essere gestiti al loro interno per migliorare ancora le prestazioni senza compromettere l'affidabilità, la coerenza o la disponibilità.

Quando si compilano applicazioni costituite da microservizi, si ha in genere una combinazione di app Web senza stato (ASP.NET, Node.js e così via) che chiamano servizi aziendali di livello intermedio con e senza stato, tutti distribuiti nello stesso cluster di Service Fabric con i comandi di distribuzione di Service Fabric. Ognuno di questi servizi è indipendente per quanto riguarda scalabilità, affidabilità e dell'utilizzo delle risorse, migliora notevolmente la flessibilità e la flessibilità di sviluppo e gestione del ciclo di vita.

I microservizi con stato semplificano la progettazione delle applicazioni perché eliminano la necessità di usare code e cache aggiuntive, che invece erano necessarie in passato per soddisfare i requisiti di disponibilità e latenza di applicazioni interamente senza stato. Poiché i servizi con stato hanno naturalmente la disponibilità elevata e bassa latenza, esistono meno componenti variabili per la gestione dell'applicazione nel suo complesso. I diagrammi seguenti illustrano le differenze tra la progettazione di un'applicazione senza stato e la progettazione di un'applicazione con stato. Sfruttando i modelli di programmazione [Reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors](service-fabric-reliable-actors-introduction.md), i servizi con stato riducono la complessità delle applicazioni e allo stesso tempo garantiscono un'elevata velocità effettiva e una bassa latenza.

## <a name="an-application-built-using-stateless-services"></a>Applicazione creata con servizi senza stato
![Applicazione che usa un servizio senza stato][Image1]

## <a name="an-application-built-using-stateful-services"></a>Applicazione creata con servizi con stato
![Applicazione che usa un servizio senza stato][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

* Leggere informazioni sui [casi di studio dei clienti](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)
* Altre informazioni su [modelli e scenari](service-fabric-patterns-and-scenarios.md)

* Per iniziare a creare servizi con e senza stato, usare i modelli di programmazione [Reliable Services](service-fabric-reliable-services-quick-start.md) e [Reliable Actors](service-fabric-reliable-actors-get-started.md) di Service Fabric.
* Visita il centro architetture Azure per indicazioni su [creazione di microservizi in Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* Passare a [procedure consigliate per applicazioni di Azure Service Fabric e cluster](service-fabric-best-practices-overview.md) per indicazioni di progettazione dell'applicazione.

* Vedere anche gli argomenti seguenti:
  * [Informazioni sui microservizi](service-fabric-overview-microservices.md)
  * [Definire e gestire lo stato del servizio](service-fabric-concepts-state.md)
  * [Disponibilità dei servizi di Service Fabric](service-fabric-availability-services.md)
  * [Scalabilità dei servizi di Service Fabric](service-fabric-concepts-scalability.md)
  * [Partizionare i servizi di Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
