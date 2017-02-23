---
title: Informazioni su Azure Service Fabric | Documentazione Microsoft
description: "Panoramica e guida introduttiva di Service Fabric, in cui le applicazioni sono costituite da numerosi microservizi per assicurare scalabilità e resilienza."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/09/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 6c2464b2f4d16f70c2841faf18e2246c8125b60f
ms.openlocfilehash: 9421b8545715def823a4bdafd27c261e159fbbab


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Informazioni su Service Fabric
Questo percorso di apprendimento permette di muovere i primi passi nello sviluppo di applicazioni scalabili, affidabili e di facile gestione in Service Fabric.

## <a name="the-five-minute-overview"></a>Panoramica rapida
Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili e che permette di affrontare le sfide significative poste dallo sviluppo e dalla gestione di applicazioni cloud. Consente agli sviluppatori e agli amministratori di non dover più occuparsi della risoluzione di complessi problemi di infrastruttura, ma di concentrarsi sull'implementazione di carichi di lavoro cruciali e impegnativi, consapevoli di quanto siano anche scalabili, affidabili e gestibili. Service Fabric rappresenta la piattaforma middleware di prossima generazione per la creazione e la gestione di applicazioni cloud di classe enterprise di primo livello.  

Questo breve video di Channel9 presenta Service Fabric e i microservizi:<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>Panoramica dettagliata
Service Fabric consente di creare e gestire applicazioni scalabili e affidabili costituite da microservizi eseguiti a densità molto elevata in un pool condiviso di computer, chiamato cluster. Offre un sofisticato runtime per la creazione di microservizi scalabili e distribuiti, con e senza stato. Fornisce inoltre una gamma completa di funzionalità di gestione per il provisioning, la distribuzione, il monitoraggio, l'aggiornamento, l'esecuzione di patch e l'eliminazione di applicazioni distribuite.  Per altre informazioni, vedere la [Panoramica di Service Fabric](service-fabric-overview.md).

Perché usare un approccio di progettazione di microservizi? Tutte le applicazioni si evolvono con il trascorrere del tempo. Le applicazioni che hanno maggior successo si evolvono diventando utili per le persone. Quanto si conoscono i requisiti attuali e come si svilupperanno in futuro? In alcuni casi, il rilascio di una semplice app come prototipo è un fattore determinante, sapendo che l'applicazione può essere riprogettata in un secondo momento. D'altra parte, quando le aziende parlano di creazione per il cloud prevedono crescita e utilizzo. Il problema è che la crescita e la scalabilità sono imprevedibili. Sarebbe auspicabile avere la possibilità di creare prototipi rapidamente, sapendo che l'app può essere ridimensionata per rispondere a un utilizzo e a una crescita imprevedibili.  La [panoramica sui microservizi](service-fabric-overview-microservices.md) illustra come l'approccio di progettazione di microservizi risolve questi problemi e come creare microservizi che è possibile ridimensionare, testare, distribuire e gestire in modo indipendente.

Service Fabric offre una piattaforma flessibile e affidabile che consente di scrivere ed eseguire molti tipi di applicazioni e servizi aziendali.  Permette anche di eseguire tutte le applicazioni esistenti, scritte in qualsiasi linguaggio.  Le applicazioni e i microservizi possono essere con o senza stato e implementano il bilanciamento delle risorse tra le macchine virtuali per ottimizzare l'efficienza. La particolare architettura di Service Fabric consente di eseguire operazioni di analisi dei dati e di calcolo in memoria, transazioni parallele ed elaborazione degli eventi quasi in tempo reale nelle applicazioni. È possibile aumentare o ridurre facilmente il numero di applicazioni a seconda dei requisiti di risorse. Per informazioni sulle categorie di applicazioni e servizi che è possibile creare e su casi di studio sui clienti, vedere [Scenari di applicazione di Service Fabric](service-fabric-application-scenarios.md).

Questo video più lungo di Microsoft Virtual Academy descrive i concetti di base di Service Fabric:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>Introduzione e creazione della prima app 
Gli strumenti e gli SDK di Service Fabric permettono di sviluppare app in ambienti Windows, Linux o MacOS e distribuirle in cluster in esecuzione in Windows o Linux.  Le indicazioni riportate di seguito permettono di distribuire un'app in pochi minuti.  Dopo aver eseguito la prima applicazione è possibile scaricare ed eseguire alcune [app di esempio](http://aka.ms/servicefabricsamples).

### <a name="on-windows"></a>In Windows
Service Fabric SDK include un componente aggiuntivo per Visual Studio che fornisce i modelli e gli strumenti per creare, eseguire il debug e distribuire applicazioni di Service Fabric. Questi argomenti illustrano il processo di creazione della prima applicazione in Visual Studio e la sua esecuzione nel computer di sviluppo.

[Configurare l'ambiente di sviluppo](service-fabric-get-started.md)
[Creare la prima app (C#)](service-fabric-create-your-first-application-in-visual-studio.md)

Questa [esercitazione pratica](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) completa permette di acquisire familiarità con il flusso di sviluppo end-to-end di Service Fabric.  È possibile creare un servizio senza stato, configurare i report di integrità e di monitoraggio ed eseguire un aggiornamento dell'applicazione. 

Il video di Channel9 seguente illustra il processo di creazione di un'app C# in Visual Studio:  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>In Linux
Service Fabric mette a disposizione SDK per la compilazione di servizi su Linux in .NET Core e Java. Questi argomenti illustrano il processo di creazione della prima applicazione Java o C# in Linux e la sua esecuzione nel computer di sviluppo.
[Configurare l'ambiente di sviluppo](service-fabric-get-started-linux.md)
[Creare la prima app (Java)](service-fabric-create-your-first-linux-application-with-java.md)
[Creare la prima app (C#)](service-fabric-create-your-first-linux-application-with-csharp.md)

Il video di Microsoft Virtual Academy seguente illustra il processo di creazione di un'app Java in Linux:  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>In MacOS
È possibile compilare applicazioni di Service Fabric in MacOS X per l'esecuzione in cluster Linux. Questi articoli descrivono come configurare il computer Mac per lo sviluppo e illustrano il processo di creazione della prima applicazione Java in MacOS e la sua esecuzione in una macchina virtuale Ubuntu.
[Configurare l'ambiente di sviluppo](service-fabric-get-started-mac.md)
[Creare la prima app (Java)](service-fabric-create-your-first-linux-application-with-java.md)

## <a name="core-concepts"></a>Concetti principali
Gli articoli [Panoramica della terminologia di Service Fabric](service-fabric-technical-overview.md), [Modellare un'applicazione in Service Fabric](service-fabric-application-model.md) e [Panoramica dei modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) permettono di approfondire altri concetti e descrizioni, ma questo articolo contiene le nozioni di base.

<table><tr><th>Concetti principali</th><th>Fase di progettazione</th><th>Fase di esecuzione</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-app-type-service-type-app-package-and-manifest-service-package-and-manifest"></a>Fase di progettazione: tipo di app, tipo di servizio, manifesto e pacchetto dell'app, manifesto e pacchetto del servizio
Il tipo di applicazione è il nome o la versione assegnata a una raccolta di tipi di servizio. È definito in un file ApplicationManifest.xml incorporato in una directory del pacchetto dell'applicazione, che viene copiato nell'archivio immagini del cluster di Service Fabric. È quindi possibile creare un'applicazione denominata da questo tipo di applicazione, che viene eseguita all'interno del cluster. 

Il tipo di servizio è il nome o la versione assegnata ai pacchetti di codice, ai pacchetti di dati e ai pacchetti di configurazione del servizio. È definito in un file ServiceManifest.xml incorporato in una directory del pacchetto del servizio a cui a sua volta fa riferimento un file ApplicationManifest.xml del pacchetto dell'applicazione. All'interno del cluster, dopo aver creato un'applicazione denominata, è possibile creare un servizio denominato da uno dei tipi di servizio del tipo di applicazione. Il tipo di servizio è descritto dal relativo file ServiceManifest.xml ed è composto dalle impostazioni di configurazione del servizio codice eseguibile, caricate in fase di esecuzione, e dai dati statici utilizzati dal servizio.

![Tipi di applicazioni di Service Fabric e tipi di servizio][cluster-imagestore-apptypes]

Il pacchetto dell'applicazione è una directory del disco contenente il file ApplicationManifest.xml del tipo di applicazione, che fa riferimento ai pacchetti del servizio per ogni tipo di servizio che costituisce il tipo di applicazione. Ad esempio, un pacchetto dell'applicazione per il tipo di applicazione posta elettronica può contenere un pacchetto del servizio di accodamento, un pacchetto del servizio front-end e un pacchetto del servizio di database. I file nella directory del pacchetto dell'applicazione vengono copiati nell'archivio immagini del cluster di Service Fabric. 

Il pacchetto del servizio è una directory del disco contenente il file ServiceManifest.xml del tipo di servizio, che fa riferimento al codice, ai dati statici e ai pacchetti di configurazione del tipo di servizio. Il file ApplicationManifest.xml del tipo di applicazione fa riferimento ai file nella directory del pacchetto del servizio. Ad esempio, un pacchetto del servizio può fare riferimento al codice, ai dati statici e ai pacchetti di configurazione che costituiscono un servizio di database.

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>Fase di esecuzione: cluster e nodi, app denominate, servizi denominati, partizioni e repliche
Un [cluster di Service Fabric](service-fabric-deploy-anywhere.md) è un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi. I cluster possono supportare migliaia di macchine.

Un computer o una macchina virtuale che fa parte di un cluster viene chiamato nodo. A ogni nodo viene assegnato un nome (stringa). I nodi presentano delle caratteristiche, ad esempio le proprietà di posizionamento. In ogni computer o macchina virtuale è disponibile un servizio di avvio automatico di Windows, FabricHost.exe, che viene eseguito all'avvio e che a sua volta avvia due eseguibili: Fabric.exe e FabricGateway.exe. Questi due eseguibili costituiscono il nodo. Negli scenari di sviluppo o di test è possibile ospitare più nodi in un singolo PC o una singola macchina virtuale eseguendo più istanze di Fabric.exe e FabricGateway.exe.

Un'applicazione denominata è una raccolta di servizi denominati che esegue una o più funzioni specifiche. Un servizio esegue una funzione completa e autonoma (avvio ed esecuzione indipendenti da altri servizi) ed è costituito da codice, configurazione e dati. Al termine della copia di un pacchetto dell'applicazione nell'archivio immagini, è possibile creare un'istanza dell'applicazione all'interno del cluster specificando il tipo di applicazione del pacchetto dell'applicazione, usando nome e versione corrispondenti. A ogni istanza del tipo di applicazione viene assegnato un nome URI simile al seguente: *fabric:/MyNamedApp*. All'interno di un cluster è possibile creare più applicazioni denominate da un singolo tipo di applicazione. È anche possibile creare applicazioni denominate da tipi di applicazione diversi. L'amministrazione e il controllo delle versioni sono gestiti in modo indipendente per ogni applicazione.

Dopo aver creato un'applicazione denominata, è possibile creare un'istanza di uno dei relativi tipi di servizio, ovvero un servizio denominato, all'interno del cluster. A tale scopo è necessario specificare il tipo di servizio, usando nome e versione corrispondenti. A ogni istanza del tipo di servizio viene assegnato un nome URI con un ambito definito dall'URI della relativa applicazione denominata. Se, ad esempio, si crea un servizio denominato "MyDatabase" all'interno di un'applicazione denominata "MyNamedApp", l'URI corrispondente sarà simile al seguente: *fabric:/MyNamedApp/MyDatabase*. All'interno di un'applicazione denominata è possibile creare uno o più servizi denominati. Ogni servizio denominato può avere uno schema di partizione e numeri di istanze/repliche specifici. 

Sono disponibili due tipi di servizi: con e senza stato.  Lo stato permanente di un servizio senza stato è archiviato in una risorsa di archiviazione esterna, ad esempio Archiviazione di Azure, database SQL di Azure o Azure DocumentDB. Usare un servizio senza stato nei casi in cui il servizio non prevede alcun tipo di archivio permanente. Un servizio con stato usa Service Fabric per gestire lo stato del servizio tramite i modelli di programmazione Reliable Collections o Reliable Actors.  

Quando si crea un servizio denominato, è necessario specificare uno schema di partizione. I servizi con grandi quantità di stato suddividono i dati tra partizioni che li distribuiscono nei vari nodi del cluster. In questo modo è possibile ridimensionare lo stato del servizio denominato. All'interno di una partizione, per i servizi denominati senza stato sono presenti istanze mentre per i servizi denominati con stato sono presenti repliche. In genere i servizi denominati senza stato avranno sempre una sola partizione, dal momento che non hanno uno stato interno. Le istanze della partizione garantiscono la disponibilità. Se un'istanza presenta un errore, le altre continuano a funzionare normalmente e Service Fabric creerà una nuova istanza. I servizi denominati con stato gestiscono il proprio stato all'interno delle repliche e ogni partizione contiene un set di repliche dedicato con tutti gli stati sincronizzati. Se una replica presenta un errore, Service Fabric ne crea una nuova da quelle esistenti.

Il diagramma seguente illustra la relazione tra applicazioni e istanze di servizi, partizioni e repliche.

![Partizioni e repliche in un servizio][cluster-application-instances]

## <a name="supported-programming-models"></a>Modelli di programmazione supportati
Service Fabric offre diversi modi per scrivere e gestire i servizi. È possibile far sì che i servizi usino le API di Service Fabric per sfruttare appieno le funzionalità della piattaforma e il framework delle applicazioni oppure i servizi possono consistere semplicemente in qualsiasi programma eseguibile compilato scritto in un qualsiasi linguaggio e ospitato in un cluster di Service Fabric. Per altre informazioni, vedere l'articolo relativo ai [modelli di programmazione supportati](service-fabric-choose-framework.md).

### <a name="guest-executables"></a>Eseguibili guest
Un [eseguibile guest](service-fabric-deploy-existing-app.md) è un eseguibile arbitrario, scritto in un qualsiasi linguaggio, che consente di ospitare le applicazioni esistenti in un cluster di Service Fabric insieme ad altri servizi. Gli eseguibili guest, tuttavia, non si integrano direttamente con le API di Service Fabric e non possono quindi usare il set completo di funzionalità offerte dalla piattaforma, ad esempio la personalizzazione dei report su integrità e carico, la registrazione degli endpoint di servizio e il calcolo con stato.

### <a name="containers"></a>Contenitori
Per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. Service Fabric può anche distribuire servizi in [immagini contenitore](service-fabric-containers-overview.md). Un aspetto importante è che è possibile combinare servizi in processi e servizi in contenitori nella stessa applicazione.  Service Fabric attualmente supporta la distribuzione di contenitori Docker in Linux e di contenitori Windows Server in Windows Server 2016. Nel modello applicativo di Service Fabric un contenitore rappresenta un host applicazione in cui sono inserite più repliche dei servizi.  È possibile distribuire applicazioni esistenti, servizi senza stato o servizi con stato in un contenitore usando Service Fabric.  

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) è un framework leggero per la scrittura di servizi che si integrano con la piattaforma di Service Fabric sfruttandone il set completo di funzionalità. Reliable Services può essere senza stato, come la maggior parte delle piattaforme di servizio, ad esempio server Web o ruoli di lavoro in Servizi cloud di Azure, in cui lo stato viene mantenuto in una soluzione esterna, ad esempio il database di Azure o l'archivio tabelle di Azure. Reliable Services può essere anche con stato e lo stato viene mantenuto direttamente nel servizio stesso tramite Reliable Collections. Lo stato viene reso altamente disponibile tramite la replica e distribuito tramite partizionamento, il tutto gestito automaticamente da Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
[Reliable Actor](service-fabric-reliable-actors-introduction.md), basato su Reliable Services, è un framework applicazione che implementa il criterio Actor virtuale, basato a sua volta sul modello di progettazione dell'attore. Il framework Reliable Actor usa unità di calcolo e di stato indipendenti con esecuzione a thread singolo chiamate attori. Il framework Reliable Actors offre la comunicazione incorporata per gli attori e configurazioni di mantenimento dello stato e scalabilità preimpostate.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come creare un [cluster in Azure](service-fabric-cluster-creation-via-portal.md) o un [cluster autonomo in Windows](service-fabric-cluster-creation-for-windows-server.md).
* Provare a creare un servizio con il modello di programmazione[Reliable Services](service-fabric-reliable-services-quick-start.md) o [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Informazioni sul [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md).
* Informazioni su come [controllare l'integrità dell'applicazione e del cluster](service-fabric-health-introduction.md).
* Informazioni su come [monitorare e diagnosticare i servizi](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md).


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png



<!--HONumber=Feb17_HO3-->


