---
title: Panoramica di Service Fabric di servizi | Microsoft Docs
description: "Panoramica di Service Fabric, in cui le applicazioni sono costituite da numerosi microservizi per assicurare scalabilità e resilienza. Service Fabric è una piattaforma di sistemi distribuiti che consente di creare applicazioni scalabili, affidabili e facilmente gestibili per il cloud."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/22/2016
ms.author: mfussell
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5bd613b7c6e44ad1aa232adc6649d237999fd53c


---
# <a name="overview-of-azure-service-fabric"></a>Panoramica di Azure Service Fabric
Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Service Fabric fa fronte anche alle principali problematiche correlate allo sviluppo e alla gestione delle applicazioni cloud. Gli sviluppatori e gli amministratori non devono più occuparsi di risolvere complessi problemi di infrastruttura e possono concentrarsi sull'implementazione di carichi di lavoro cruciali e impegnativi, con la certezza di assicurare scalabilità, affidabilità e gestibilità. Service Fabric rappresenta la piattaforma middleware di prossima generazione per la creazione e la gestione di applicazioni cloud di classe enterprise di primo livello.

Questo [breve video](https://aka.ms/servicefabricvideo) presenta Service Fabric e i microservizi.

## <a name="applications-composed-of-microservices"></a>Applicazioni costituite da microservizi
Service Fabric consente di creare e gestire applicazioni scalabili e affidabili costituite da microservizi eseguiti a densità molto elevata in un pool condiviso di computer, chiamato cluster. Offre un sofisticato runtime per la creazione di microservizi scalabili e distribuiti, con e senza stato. Fornisce inoltre una gamma completa di funzionalità di gestione per il provisioning, la distribuzione, il monitoraggio, l'aggiornamento, l'esecuzione di patch e l'eliminazione di applicazioni distribuite.

Perché l'approccio ai microservizi è importante? I due motivi principali sono:

* Consentono di ridimensionare diverse parti dell'applicazione a seconda delle sue esigenze.
* I team di sviluppo possono offrire una maggiore flessibilità nell'implementazione di modifiche e quindi di fornire funzionalità ai clienti più rapidamente e con maggiore frequenza.

Service Fabric viene attualmente usato in numerosi servizi Microsoft, tra cui database SQL di Azure, Azure DocumentDB, Cortana, Microsoft Power BI, Microsoft Intune, Hub eventi di Azure, hub IoT di Azure, Skype for Business e molti servizi di base di Azure.

Service Fabric è stato sviluppato specificamente per la creazione di servizi cloud nativi che inizialmente possono essere di piccole dimensioni, ma che secondo le necessità possono espandersi su vasta scala con centinaia o migliaia di computer.

Gli attuali servizi su scala Internet vengono compilati attraverso microservizi. costituiti ad esempio da gateway di protocollo, profili utente, carrelli acquisti, elaborazione dell'inventario, code e cache. Service Fabric è una piattaforma di microservizi che assegna a ognuno di essi un nome univoco, con o senza stato.

Service Fabric offre funzionalità complete di gestione del ciclo di vita e del runtime per le applicazioni costituite da questi microservizi. Ospita i microservizi all'interno di contenitori distribuiti e attivati nel cluster di Service Fabric. Il passaggio dalle macchine virtuali ai contenitori consente un incremento di densità considerevole. Analogamente, un altro incremento di densità considerevole si ottiene passando dai contenitori ai microservizi. Un singolo cluster di database SQL di Azure, ad esempio, è costituito da centinaia di computer che eseguono decine di migliaia di contenitori in cui vengono ospitati complessivamente centinaia di migliaia di database. Ogni database è un microservizio di Service Fabric con stato. Lo stesso accade per gli altri servizi menzionati in precedenza, motivo per cui il termine *iperscalabilità* viene usato per descrivere le funzionalità di Service Fabric. Se, infatti, i contenitori assicurano una densità elevata, i microservizi garantiscono un'iperscalabilità.

Per altre informazioni sull'approccio basato su microservizi, leggere [Perché usare un approccio ai microservizi per la compilazione di applicazioni?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Orchestrazione e distribuzione di contenitori
Service Fabric è un [agente di orchestrazione](service-fabric-cluster-resource-manager-introduction.md) dei servizi in un cluster di computer. I microservizi possono essere distribuiti in molti modi, che includono dall'uso dei [modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) alla distribuzione di [eseguibili guest](service-fabric-deploy-existing-app.md). Service Fabric può distribuire servizi in immagini del contenitore. Un aspetto importante è che è possibile combinare sia i servizi in processi che i servizi in contenitori nella stessa applicazione. Se si desidera semplicemente [distribuire e gestire le immagini contenitore](service-fabric-containers-overview.md) in un cluster di computer, Service Fabric è la scelta ideale.

## <a name="create-clusters-for-service-fabric-anywhere"></a>Creare i cluster di Service Fabric in qualsiasi ambiente
È possibile creare cluster di Service Fabric in molti ambienti, tra cui Azure o in locale, in Windows Server o su Linux. Inoltre l'ambiente di sviluppo nell'SDK è identico all'ambiente di produzione e non sono coinvolti emulatori. In altre parole, ciò che è in esecuzione nel cluster di sviluppo locale viene distribuito nello stesso cluster in altri ambienti.

Per altre informazioni sulla creazione di cluster in locale, vedere gli articoli relativi alla [creazione di un cluster in Windows Server o Linux](service-fabric-deploy-anywhere.md) o alla creazione di un cluster in Azure [tramite il Portale di Azure](service-fabric-cluster-creation-via-portal.md).

![Piattaforma Service Fabric][Image1]

## <a name="stateless-and-stateful-micrososervices-for-service-fabric"></a>Microservizi con e senza stato per Service Fabric
Service Fabric consente di compilare applicazioni costituite da microservizi. I microservizi senza stato, come i gateway di protocollo, i proxy Web e così via, non mantengono uno stato variabile al di fuori di una richiesta e della relativa risposta fornita dal servizio. I ruoli di lavoro di Servizi cloud di Azure sono un esempio di servizio senza stato. I microservizi con stato, come gli account utente, i database, i dispositivi, i carrelli acquisti e le code, mantengono invece uno stato variabile e autorevole anche all'esterno della richiesta e della relativa risposta. Le attuali applicazioni su scala Internet sono costituite da una combinazione di microservizi con e senza stato.

Perché avere microservizi con stato insieme a quelli senza stato? I due motivi principali sono:

* È possibile compilare servizi OLTP (elaborazione delle transazioni online) ad alta velocità, a bassa latenza e tolleranti agli errori mantenendo il codice e i dati nello stesso computer. Alcuni esempi sono le vetrine interattive, le ricerche, i sistemi Internet delle cose (IoT), i sistemi di trading, l'elaborazione delle carte di credito, i sistemi di rilevamento di frodi e la gestione dei record personali.
* È possibile semplificare la progettazione dell'applicazione. Per i microservizi con stato non è più necessario usare le code e le cache aggiuntive generalmente richieste per soddisfare i requisiti di disponibilità e latenza di un'applicazione interamente senza stato. L'elevata disponibilità e la bassa latenza dei servizi con stato implicano un uso ridotto di componenti mobili da gestire in un'applicazione nel suo insieme.

Per altre informazioni sui modelli di applicazione con Service Fabric, vedere [Scenari di applicazione](service-fabric-application-scenarios.md) e [Scelta di un framework per il modello di programmazione](service-fabric-choose-framework.md) per il servizio.

## <a name="application-lifecycle-management"></a>Gestione del ciclo di vita delle applicazioni
Service Fabric fornisce supporto per la gestione dell'intero ciclo di vita dell'applicazione per le applicazioni cloud. Questo ciclo di vita include lo sviluppo attraverso la distribuzione , la gestione quotidiana e la manutenzione fino all'eventuale ritiro.

Le funzionalità di gestione del ciclo di vita delle applicazioni di Service Fabric consentono agli amministratori delle applicazioni e agli operatori IT di usare semplici flussi di lavoro con interazione limitata per il provisioning, la distribuzione, l'applicazione di patch e il monitoraggio delle applicazioni. Questi flussi di lavoro predefiniti riducono in modo significativo il carico degli operatori IT per mantenere le applicazioni continuamente disponibili.

La maggior parte delle applicazioni è costituita da una combinazione di microservizi con e senza stato e da altri runtime o file eseguibili distribuiti insieme. Grazie all'uso di tipi sicuri nelle applicazioni e di microservizi combinati in pacchetti, Service Fabric consente la distribuzione di più istanze di applicazione. Ogni istanza viene gestita e aggiornata in modo indipendente. Aspetto ancora più importante, Service Fabric può distribuire *qualsiasi* eseguibile o runtime e renderlo affidabile. Ad esempio, Service Fabric distribuisce ASP.NET Core 1, Node.js, macchine virtuali Java, script o qualsiasi elemento costitutivo dell'applicazione.

Per altre informazioni sulla gestione del ciclo di vita delle applicazioni, vedere l'articolo [Ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md). Per altre informazioni su come distribuire il codice, vedere [Distribuire un eseguibile guest](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>Funzionalità principali
Usando Service Fabric è possibile:

* Sviluppare applicazioni altamente scalabili, con funzionalità di riparazione automatica.
* Sviluppare applicazioni costituite da microservizi usando il modello di programmazione di Service Fabric. Oppure è possibile semplicemente ospitare eseguibili guest e altri framework di applicazione di propria scelta, come ASP.NET Core 1 o Node. js.
* Sviluppare microservizi con o senza stato altamente affidabili.
* Tra i contenitori da distribuire e orchestrare figurano i contenitori di Windows e di Docker presenti in un cluster. Questi contenitori possono contenere eseguibili guest o microservizi con/senza stato affidabili. In entrambi i casi è possibile ottenere mapping tra porta del contenitore e porta dell'host, rilevamento del contenitore e failover automatico.
* Semplificare la progettazione dell'applicazione usando microservizi con stato al posto di cache e code.
* Eseguire la distribuzione in Azure o in data center locali che eseguono Windows o Linux senza dover apportare modifiche al codice. Scrivere una sola volta e quindi eseguire la distribuzione ovunque in qualsiasi cluster di Service Fabric.
* Eseguire attività di sviluppo con un approccio di tipo "data center nel proprio computer". L'ambiente di sviluppo locale usa infatti lo stesso codice eseguito nei data center di Azure.
* Distribuire le applicazioni nel giro di pochi secondi.
* Distribuire applicazioni a una densità maggiore rispetto alle macchine virtuali, distribuendo centinaia o migliaia di applicazioni per ogni computer.
* Distribuire versioni diverse della stessa applicazione side-by-side e aggiornarle in modo indipendente.
* Gestire il ciclo di vita delle applicazioni con stato senza tempi di inattività, inclusa l'esecuzione di aggiornamenti di rilievo e non di rilievo.
* Gestire le applicazioni usando le API .NET, Java (Linux), PowerShell, l'interfaccia della riga di comando di Azure (Linux) o l'interfaccia REST.
* Aggiornare i microservizi e applicarvi patch all'interno delle applicazioni in modo indipendente.
* Monitorare e diagnosticare l'integrità delle applicazioni e impostare i criteri per eseguire riparazioni automatiche.
* Aumentare o ridurre il numero di nodi in un cluster e aumentare o ridurre le dimensioni di ogni nodo. Quando si ridimensionano i nodi, si ridimensionano automaticamente anche le applicazioni, che vengono distribuite in base alle risorse disponibili.
* Osservare il sistema di bilanciamento delle risorse con riparazione automatica che orchestra la ridistribuzione delle applicazioni all'interno del cluster. Service Fabric esegue il ripristino dagli errori e ottimizza la distribuzione del carico in base alle risorse disponibili.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni:
  * [Perché usare un approccio ai microservizi per la compilazione di applicazioni](service-fabric-overview-microservices.md)
  * [Panoramica della terminologia](service-fabric-technical-overview.md)
* Configurazione dell' [ambiente di sviluppo](service-fabric-get-started.md)  
* [Panoramica dei modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) per il servizio

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png



<!--HONumber=Nov16_HO3-->


