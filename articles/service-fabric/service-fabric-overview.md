---
title: Panoramica di Service Fabric in Azure | Microsoft Docs
description: "Panoramica di Service Fabric, in cui le applicazioni sono costituite da numerosi microservizi per assicurare scalabilità e resilienza. Service Fabric è una piattaforma di sistemi distribuiti che consente di creare applicazioni scalabili, affidabili e facilmente gestibili per il cloud."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: msfussell
ms.custom: mvc
ms.openlocfilehash: 8ff0d38a679b673b148dd808050eda82060cfe80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-service-fabric"></a>Panoramica di Azure Service Fabric
Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica la disposizione in pacchetti, la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili. Service Fabric fa fronte anche alle principali problematiche correlate allo sviluppo e alla gestione di applicazioni cloud native. Gli sviluppatori e gli amministratori non devono più occuparsi di risolvere complessi problemi di infrastruttura e possono concentrarsi sull'implementazione di carichi di lavoro cruciali e impegnativi, con la certezza di assicurare scalabilità, affidabilità e gestibilità. Service Fabric rappresenta la piattaforma di prossima generazione per la creazione e la gestione di applicazioni cloud di classe enterprise di primo livello eseguite in contenitori.

Questo breve video presenta Service Fabric e i microservizi: <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="applications-composed-of-microservices"></a>Applicazioni costituite da microservizi 
Service Fabric consente di creare e gestire applicazioni scalabili e affidabili costituite da microservizi eseguiti a densità elevata in un pool condiviso di computer, denominato cluster. Offre un runtime sofisticato e leggero per la creazione di microservizi scalabili e distribuiti, con e senza stato, eseguiti in contenitori. Fornisce inoltre una gamma completa di funzionalità di gestione per il provisioning, la distribuzione, il monitoraggio, l'aggiornamento, l'esecuzione di patch e l'eliminazione di applicazioni distribuite, tra cui servizi di contenitori.

Service Fabric viene attualmente usato in numerosi servizi Microsoft, tra cui database SQL di Azure, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Hub eventi di Azure, Hub IoT di Azure, Dynamics 365, Skype for Business e molti servizi di base di Azure.

Service Fabric è stato sviluppato specificamente per la creazione di servizi cloud nativi che inizialmente possono essere di piccole dimensioni, ma che secondo le necessità possono espandersi su vasta scala con centinaia o migliaia di computer.

Gli attuali servizi su scala Internet vengono compilati attraverso microservizi. costituiti ad esempio da gateway di protocollo, profili utente, carrelli acquisti, elaborazione dell'inventario, code e cache. Service Fabric è una piattaforma di microservizi che assegna a ogni microservizio (o contenitore) un nome univoco, con o senza stato.

Service Fabric offre funzionalità complete di gestione del ciclo di vita e del runtime per le applicazioni costituite da questi microservizi. Ospita i microservizi all'interno di contenitori distribuiti e attivati nel cluster di Service Fabric. Il passaggio dalle macchine virtuali ai contenitori consente un incremento di densità considerevole. Analogamente, un altro incremento di densità sostanziale si ottiene passando dai contenitori ai microservizi presenti nei contenitori. Un singolo cluster di database SQL di Azure, ad esempio, è costituito da centinaia di computer che eseguono decine di migliaia di contenitori in cui vengono ospitati complessivamente centinaia di migliaia di database. Ogni database è un microservizio di Service Fabric con stato. 

Per altre informazioni sull'approccio basato su microservizi, leggere [Perché usare un approccio basato su microservizi per la compilazione delle applicazioni?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Orchestrazione e distribuzione di contenitori
Service Fabric è un [agente di orchestrazione dei contenitori](service-fabric-cluster-resource-manager-introduction.md) che distribuisce microservizi in un cluster di computer. I microservizi possono essere distribuiti in molti modi, ad esempio usando i [modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) o [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) oppure distribuendo [un codice a propria scelta](service-fabric-deploy-existing-app.md). Un aspetto importante è che è possibile combinare sia i servizi in processi che i servizi in contenitori nella stessa applicazione. Se si vuole semplicemente [distribuire e gestire i contenitori](service-fabric-containers-overview.md), Service Fabric è la scelta ideale come agente di orchestrazione dei contenitori.

## <a name="any-os-any-cloud"></a>Qualsiasi sistema operativo, qualsiasi ambiente cloud
Service Fabric può essere eseguito ovunque. È possibile creare cluster di Service Fabric in molti ambienti, tra cui Azure o in locale, in Windows Server o su Linux, nonché in altri cloud pubblici. L'ambiente di sviluppo nell'SDK, inoltre, è **identico** all'ambiente di produzione, senza alcun emulatore. In altre parole, i componenti in esecuzione nel cluster di sviluppo locale vengono distribuiti anche nei cluster presenti in altri ambienti.

![Piattaforma Service Fabric][Image1]

Per lo sviluppo in Windows, .NET SDK di Service Fabric è integrato con Visual Studio e PowerShell. Vedere [Preparare l'ambiente di sviluppo in Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started.md). Per lo sviluppo in Linux, Java SDK di Service Fabric è integrato con Eclipse e viene usato Yeoman per generare modelli per Java, .NET CORE e applicazioni contenitore. Vedere [Preparare l'ambiente di sviluppo in Linux](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started.md)

Per altre informazioni sulla creazione di cluster, vedere gli articoli relativi alla [creazione di un cluster in Windows Server o Linux](service-fabric-deploy-anywhere.md) o alla creazione di un cluster in Azure [tramite il portale di Azure](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microservizi con e senza stato per Service Fabric
Service Fabric consente di compilare applicazioni costituite da microservizi o contenitori. I microservizi senza stato, come i gateway di protocollo, i proxy Web e così via, non mantengono uno stato variabile al di fuori di una richiesta e della relativa risposta fornita dal servizio. I ruoli di lavoro di Servizi cloud di Azure sono un esempio di servizio senza stato. I microservizi con stato, come gli account utente, i database, i dispositivi, i carrelli acquisti e le code, mantengono invece uno stato variabile e autorevole anche all'esterno della richiesta e della relativa risposta. Le attuali applicazioni su scala Internet sono costituite da una combinazione di microservizi con e senza stato. 

Un fattore essenziale per la differenziazione di Service Fabric è la creazione di servizi con stato, mediante i [modelli di programmazione incorporati](service-fabric-choose-framework.md) o mediante i servizi con stato in contenitori. L'articolo [Scenari di applicazione di Service Fabric](service-fabric-application-scenarios.md) descrive gli scenari in cui vengono usati i servizi con stato.


## <a name="application-lifecycle-management"></a>Gestione del ciclo di vita delle applicazioni
Service Fabric offre supporto per la gestione dell'intero ciclo di vita dell'applicazione e per l'integrazione continua e distribuzione continua di applicazioni cloud, inclusi i contenitori. Questo ciclo di vita include lo sviluppo attraverso la distribuzione , la gestione quotidiana e la manutenzione fino all'eventuale ritiro.

Le funzionalità di gestione del ciclo di vita delle applicazioni di Service Fabric consentono agli amministratori delle applicazioni e agli operatori IT di usare semplici flussi di lavoro con interazione limitata per il provisioning, la distribuzione, l'applicazione di patch e il monitoraggio delle applicazioni. Questi flussi di lavoro predefiniti riducono in modo significativo il carico degli operatori IT per mantenere le applicazioni continuamente disponibili.

La maggior parte delle applicazioni è costituita da una combinazione di microservizi con e senza stato, da contenitori e da altri runtime o file eseguibili distribuiti insieme. Grazie all'uso di tipi sicuri nelle applicazioni, Service Fabric consente la distribuzione di più istanze di un'applicazione. Ogni istanza viene gestita e aggiornata in modo indipendente. Aspetto ancora più importante, Service Fabric può distribuire contenitori o file eseguibili di qualsiasi tipo e renderli affidabili. Service Fabric, ad esempio, può distribuire .NET, ASP.NET Core, Node.js, contenitori Windows, contenitori Linux, macchine virtuali Java, script, Angular o qualsiasi altro elemento costitutivo dell'applicazione.

Service Fabric è integrato con vari strumenti di integrazione continua e distribuzione continua, ad esempio [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) e [Octopus Deploy](https://octopus.com/), e può essere usato con qualsiasi altro strumento di integrazione continua e distribuzione continua.

Per altre informazioni sulla gestione del ciclo di vita delle applicazioni, vedere l'articolo [Ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md). Per altre informazioni su come distribuire il codice, vedere [Distribuire un eseguibile guest](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>Funzionalità principali
Usando Service Fabric è possibile:

* Eseguire la distribuzione in Azure o in data center locali che eseguono Windows o Linux senza dover apportare modifiche al codice. Scrivere una sola volta e quindi eseguire la distribuzione ovunque in qualsiasi cluster di Service Fabric.
* Sviluppare applicazioni scalabili costituite da microservizi usando modelli di programmazione di Service Fabric, contenitori o altri tipi di codice.
* Sviluppare microservizi con o senza stato altamente affidabili. Semplificare la progettazione dell'applicazione usando microservizi con stato. 
* Usare il nuovo modello di programmazione Reliable Actors per creare oggetti cloud con stato e codice autonomo.
* Distribuire e orchestrare contenitori, tra cui contenitori Windows e Linux. Service Fabric è un agente di orchestrazione dei contenitori con stato e con funzione di riconoscimento dei dati.
* Distribuire applicazioni in pochi secondi e a densità elevata, con centinaia o migliaia di applicazioni o contenitori per ogni computer.
* Distribuire versioni diverse della stessa applicazione side-by-side e aggiornarle in modo indipendente.
* Gestire il ciclo di vita delle applicazioni senza tempi di inattività, inclusa l'esecuzione di aggiornamenti di rilievo e non di rilievo.
* Aumentare o ridurre il numero di nodi in un cluster. Quando si modifica il numero di nodi, viene automaticamente modificato anche il numero di applicazioni.
* Monitorare e diagnosticare l'integrità delle applicazioni e impostare i criteri per eseguire riparazioni automatiche.
* Osservare il sistema di bilanciamento delle risorse che coordina la ridistribuzione delle applicazioni all'interno del cluster. Service Fabric esegue il ripristino dagli errori e ottimizza la distribuzione del carico in base alle risorse disponibili.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni:
  * [Perché usare un approccio ai microservizi per la compilazione di applicazioni](service-fabric-overview-microservices.md)
  * [Panoramica della terminologia](service-fabric-technical-overview.md)
* Configurazione dell'[ambiente di sviluppo di Windows](service-fabric-get-started.md).  
* Configurazione dell'[ambiente di sviluppo di Linux](service-fabric-get-started-linux.md).
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
