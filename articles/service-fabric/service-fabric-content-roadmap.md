---
title: Altre informazioni su Azure Service Fabric | Microsoft Docs
description: Informazioni sui concetti di base e sulle aree principali di Azure Service Fabric. Offre una panoramica approfondita di Service Fabric e della creazione di microservizi.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: ryanwi
ms.openlocfilehash: e9d0691876a417fe8665bed2d712d643a4364120
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Informazioni su Service Fabric
Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili.  Service Fabric ha una struttura molto estesa e richiede un apprendimento completo.  Questo articolo offre un riepilogo di Service Fabric e descrive i concetti di base, i modelli di programmazione, il ciclo di vita dell'applicazione, i test, i cluster e il monitoraggio dell'integrità. Leggere [Panoramica](service-fabric-overview.md) e [Cosa sono i microservizi?](service-fabric-overview-microservices.md) per la presentazione di Service Fabric e per informazioni su come usarlo per creare microservizi. Questo articolo non offre un elenco completo dei contenuti ma include collegamenti ad articoli di panoramica e introduttivi su tutte le aree di Service Fabric. 

## <a name="core-concepts"></a>Concetti principali
Gli articoli [Panoramica della terminologia di Service Fabric](service-fabric-technical-overview.md), [Modellare un'applicazione in Service Fabric](service-fabric-application-model.md) e [Panoramica dei modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) permettono di approfondire altri concetti e descrizioni, ma questo articolo contiene le nozioni di base.

<table><tr><th>Concetti principali</th><th>Fase di progettazione</th><th>Fase di esecuzione</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Fase di progettazione: tipo di applicazione, tipo di servizio, manifesto e pacchetto dell'applicazione, manifesto e pacchetto del servizio
Il tipo di applicazione è il nome o la versione assegnata a una raccolta di tipi di servizio. Questo viene definito in un file *ApplicationManifest.xml* incorporato in una directory del pacchetto dell'applicazione. I file nella directory del pacchetto dell'applicazione vengono copiati nell'archivio immagini del cluster di Service Fabric. Da questo tipo di applicazione è quindi possibile creare un'applicazione denominata che viene eseguita all'interno del cluster. 

Il tipo di servizio è il nome o la versione assegnata ai pacchetti di codice, ai pacchetti di dati e ai pacchetti di configurazione del servizio. Questo viene definito in un file ServiceManifest.xml incorporato in una directory del pacchetto del servizio. Un file *ApplicationManifest.xml* del pacchetto dell'applicazione fa quindi riferimento alla directory del pacchetto del servizio. All'interno del cluster, dopo aver creato un'applicazione denominata, è possibile creare un servizio denominato da uno dei tipi di servizio del tipo di applicazione. Un tipo di servizio è descritto dal relativo file *ServiceManifest.xml*. Il tipo di servizio è composto dalle impostazioni di configurazione del servizio del codice eseguibile, caricate in fase di esecuzione, e dai dati statici utilizzati dal servizio.

![Tipi di applicazioni di Service Fabric e tipi di servizio][cluster-imagestore-apptypes]

Il pacchetto dell'applicazione è una directory del disco contenente il file *ApplicationManifest.xml* del tipo di applicazione, che fa riferimento ai pacchetti del servizio per ogni tipo di servizio che costituisce il tipo di applicazione. Ad esempio, un pacchetto dell'applicazione per il tipo di applicazione posta elettronica può contenere un pacchetto del servizio di accodamento, un pacchetto del servizio front-end e un pacchetto del servizio di database. I file nella directory del pacchetto dell'applicazione vengono copiati nell'archivio immagini del cluster di Service Fabric. 

Il pacchetto del servizio è una directory del disco contenente il file *ServiceManifest.xml* del tipo di servizio, che fa riferimento al codice, ai dati statici e ai pacchetti di configurazione del tipo di servizio. Il file *ApplicationManifest.xml* del tipo di applicazione fa riferimento ai file presenti nella directory del pacchetto del servizio. Ad esempio, un pacchetto del servizio può fare riferimento al codice, ai dati statici e ai pacchetti di configurazione che costituiscono un servizio di database.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Fase di esecuzione: cluster e nodi, applicazioni denominate, servizi denominati, partizioni e repliche
Un [cluster di Service Fabric](service-fabric-deploy-anywhere.md) è un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi. I cluster possono supportare migliaia di macchine.

Un computer o una macchina virtuale che fa parte di un cluster viene chiamato nodo. A ogni nodo viene assegnato un nome (stringa). I nodi presentano delle caratteristiche, ad esempio le proprietà di posizionamento. In ogni computer o macchina virtuale è disponibile un servizio di avvio automatico di Windows, `FabricHost.exe`, che viene eseguito all'avvio e che a sua volta avvia due eseguibili: `Fabric.exe` e `FabricGateway.exe`. Questi due eseguibili costituiscono il nodo. Negli scenari di sviluppo o test è possibile ospitare più nodi in un singolo PC o una singola VM eseguendo più istanze di `Fabric.exe` e `FabricGateway.exe`.

Un'applicazione denominata è una raccolta di servizi denominati che esegue una o più funzioni specifiche. Un servizio esegue una funzione completa e autonoma (avvio ed esecuzione indipendenti da altri servizi) ed è costituito da codice, configurazione e dati. Al termine della copia di un pacchetto dell'applicazione nell'archivio immagini, è possibile creare un'istanza dell'applicazione all'interno del cluster specificando il tipo di applicazione del pacchetto dell'applicazione, usando nome e versione corrispondenti. A ogni istanza del tipo di applicazione viene assegnato un nome URI simile a *fabric:/MyNamedApp*. All'interno di un cluster è possibile creare più applicazioni denominate da un singolo tipo di applicazione. È anche possibile creare applicazioni denominate da tipi di applicazione diversi. L'amministrazione e il controllo delle versioni sono gestiti in modo indipendente per ogni applicazione.

Dopo aver creato un'applicazione denominata, è possibile creare un'istanza di uno dei relativi tipi di servizio, ovvero un servizio denominato, all'interno del cluster. A tale scopo è necessario specificare il tipo di servizio, usando nome e versione corrispondenti. A ogni istanza del tipo di servizio viene assegnato un nome URI con un ambito definito dall'URI della relativa applicazione denominata. Se, ad esempio, si crea un servizio denominato "MyDatabase" all'interno di un'applicazione denominata "MyNamedApp", l'URI corrispondente sarà simile al seguente: *fabric:/MyNamedApp/MyDatabase*. All'interno di un'applicazione denominata è possibile creare uno o più servizi denominati. Ogni servizio denominato può avere uno schema di partizione e numeri di istanze/repliche specifici. 

Sono disponibili due tipi di servizi: con e senza stato. I servizi senza stato archiviano lo stato permanente in un servizio di archiviazione esterno, ad esempio Archiviazione di Azure, database SQL di Azure o Azure Cosmos DB. Usare un servizio senza stato nei casi in cui il servizio non prevede alcun tipo di archivio permanente. Un servizio con stato usa Service Fabric per gestire lo stato del servizio tramite i modelli di programmazione Reliable Collections o Reliable Actors. 

Quando si crea un servizio denominato, è necessario specificare uno schema di partizione. I servizi con grandi quantità di stato suddividono i dati tra partizioni. Ogni partizione è responsabile di una parte dello stato completo del servizio, che verrà distribuito tra i nodi del cluster.  

Il diagramma seguente illustra la relazione tra applicazioni e istanze di servizi, partizioni e repliche.

![Partizioni e repliche in un servizio][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partizionamento, scalabilità e disponibilità
Il [partizionamento](service-fabric-concepts-partitioning.md) non è una procedura specifica di Service Fabric. Una forma molto conosciuta di partizionamento è il partizionamento dei dati o partizionamento orizzontale. I servizi con stato con grandi quantità di stato suddividono i dati tra partizioni. Ogni partizione è responsabile di una parte dello stato completo del servizio. 

Le repliche di ogni partizione vengono distribuite tra i nodi del cluster, il che consente la [scalabilità](service-fabric-concepts-scalability.md) dello stato del servizio denominato. Quando i dati richiedono uno spazio maggiore, le partizioni crescono e Service Fabric ribilancia le partizioni tra i nodi per usare in modo efficiente le risorse hardware. Se si aggiungono nuovi nodi al cluster, Service Fabric ribilancerà le repliche di partizione sul nuovo e maggiore numero di nodi. Le prestazioni complessive dell'applicazione migliorano e la concorrenza per l'accesso alla memoria si riduce. Se i nodi del cluster non vengono usati in modo efficiente, è possibile ridurre il numero di nodi del cluster. Service Fabric ribilancia nuovamente le repliche di partizione sul nuovo e minore numero di nodi, per usare al meglio l'hardware in ogni nodo.

All'interno di una partizione, per i servizi denominati senza stato sono presenti istanze mentre per i servizi denominati con stato sono presenti repliche. In genere i servizi denominati senza stato avranno sempre una sola partizione, dal momento che non hanno uno stato interno. Le istanze della partizione garantiscono la [disponibilità](service-fabric-availability-services.md). Se un'istanza presenta un errore, le altre continuano a funzionare normalmente e Service Fabric creerà una nuova istanza. I servizi denominati con stato gestiscono il proprio stato all'interno delle repliche e ogni partizione contiene un set di repliche dedicato. Le operazioni di lettura e scrittura vengono eseguite in una replica (denominata replica primaria). I cambiamenti di stato dovuti a operazioni di scrittura vengono replicati in altre repliche (denominate repliche secondarie attive). Se una replica presenta un errore, Service Fabric ne crea una nuova da quelle esistenti.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microservizi con e senza stato per Service Fabric
Service Fabric consente di compilare applicazioni costituite da microservizi o contenitori. I microservizi senza stato, come i gateway di protocollo, i proxy Web e così via, non mantengono uno stato variabile al di fuori di una richiesta e della relativa risposta fornita dal servizio. I ruoli di lavoro di Servizi cloud di Azure sono un esempio di servizio senza stato. I microservizi con stato, come gli account utente, i database, i dispositivi, i carrelli acquisti e le code, mantengono invece uno stato variabile e autorevole anche all'esterno della richiesta e della relativa risposta. Le attuali applicazioni su scala Internet sono costituite da una combinazione di microservizi con e senza stato. 

Un fattore essenziale per la differenziazione di Service Fabric è la creazione di servizi con stato, mediante i [modelli di programmazione incorporati](service-fabric-choose-framework.md) o mediante i servizi con stato in contenitori. L'articolo [Scenari di applicazione di Service Fabric](service-fabric-application-scenarios.md) descrive gli scenari in cui vengono usati i servizi con stato.

Perché avere microservizi con stato insieme a quelli senza stato? I due motivi principali sono:

* È possibile compilare servizi OLTP (elaborazione delle transazioni online) ad alta velocità, a bassa latenza e tolleranti agli errori mantenendo il codice e i dati nello stesso computer. Alcuni esempi sono le vetrine interattive, le ricerche, i sistemi Internet delle cose (IoT), i sistemi di trading, l'elaborazione delle carte di credito, i sistemi di rilevamento di frodi e la gestione dei record personali.
* È possibile semplificare la progettazione dell'applicazione. Per i microservizi con stato non è più necessario usare le code e le cache aggiuntive generalmente richieste per soddisfare i requisiti di disponibilità e latenza di un'applicazione interamente senza stato. L'elevata disponibilità e la bassa latenza dei servizi con stato implicano un uso ridotto di componenti mobili da gestire in un'applicazione nel suo insieme.

## <a name="supported-programming-models"></a>Modelli di programmazione supportati
Service Fabric offre diversi modi per scrivere e gestire i servizi. I servizi possono usare le API di Service Fabric per sfruttare appieno le funzionalità della piattaforma e i framework applicazione. I servizi possono essere anche qualsiasi programma eseguibile compilato scritto in qualsiasi linguaggio e ospitato in un cluster di Service Fabric. Per altre informazioni, vedere l'articolo relativo ai [modelli di programmazione supportati](service-fabric-choose-framework.md).

### <a name="containers"></a>Contenitori
Per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. Service Fabric può anche distribuire servizi in [contenitori](service-fabric-containers-overview.md). Un aspetto importante è che è possibile combinare servizi in processi e servizi in contenitori nella stessa applicazione. Service Fabric supporta la distribuzione di contenitori di Linux e contenitori di Windows in Windows Server 2016. È possibile distribuire applicazioni esistenti, servizi senza stato o servizi con stato in contenitori. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) è un framework leggero per la scrittura di servizi che si integrano con la piattaforma di Service Fabric sfruttandone il set completo di funzionalità. Reliable Services può essere senza stato, come la maggior parte delle piattaforme di servizio, ad esempio server Web o ruoli di lavoro in Servizi cloud di Azure, in cui lo stato viene mantenuto in una soluzione esterna, ad esempio il database di Azure o archiviazione tabelle di Azure. Reliable Services può essere anche con stato e lo stato viene mantenuto direttamente nel servizio stesso tramite Reliable Collections. Lo stato viene reso [altamente disponibile](service-fabric-availability-services.md) tramite la replica e distribuito tramite [partizionamento](service-fabric-concepts-partitioning.md), il tutto gestito automaticamente da Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
[Reliable Actor](service-fabric-reliable-actors-introduction.md), basato su Reliable Services, è un framework applicazione che implementa il criterio Actor virtuale, basato a sua volta sul modello di progettazione dell'attore. Il framework Reliable Actor usa unità di calcolo e di stato indipendenti con esecuzione a thread singolo chiamate attori. Il framework Reliable Actors offre la comunicazione integrata per gli attori nonché configurazioni di mantenimento dello stato e scalabilità preimpostate.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric è integrato con [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) come modello di programmazione di prima classe per la creazione di applicazioni Web e API.  ASP.NET Core può essere usato in due modi diversi in Service Fabric:

- Ospitato come eseguibile guest. Questa modalità viene usata principalmente per eseguire applicazioni ASP.NET Core esistenti in Service Fabric senza apportare modifiche al codice.
- Eseguito in un servizio Reliable Services. Questa modalità offre una migliore integrazione con il runtime di Service Fabric e consente servizi ASP.NET Core con stato.

### <a name="guest-executables"></a>Eseguibili guest
Un [eseguibile guest](service-fabric-guest-executables-introduction.md) è un eseguibile arbitrario esistente, scritto in un qualsiasi linguaggio, ospitato in un cluster di Service Fabric insieme ad altri servizi. Gli eseguibili guest non si integrano direttamente con le API di Service Fabric. Tuttavia sfruttano le funzionalità offerte dalla piattaforma, quali report di integrità e caricamento personalizzati e individuabilità dei servizi mediante chiamate ad API REST. Gli eseguibili guest dispongono anche di supporto completo del ciclo di vita dell'applicazione. 

## <a name="application-lifecycle"></a>Ciclo di vita dell'applicazione
Analogamente ad altre piattaforme, un'applicazione su Service Fabric in genere passa attraverso le fasi seguenti: progettazione, sviluppo, test, distribuzione, aggiornamento, manutenzione e rimozione. Service Fabric di Azure offre un supporto di prima categoria per l'intero ciclo di vita delle applicazioni cloud, dallo sviluppo alla distribuzione, alla gestione giornaliera, alla manutenzione e infine alla rimozione delle autorizzazioni. Il modello di servizio abilita diversi ruoli per la partecipazione indipendente al ciclo di vita delle applicazioni. [Ciclo di vita dell'applicazione di Service Fabric](service-fabric-application-lifecycle.md) offre una panoramica delle interfacce API e del modo in cui vengono usate dai diversi ruoli nelle fasi del ciclo di vita di un'applicazione di Service Fabric. 

L'intero ciclo di vita dell'app può essere gestito tramite i [cmdlet di PowerShell](/powershell/module/ServiceFabric/), i [comandi dell'interfaccia della riga di comando](service-fabric-sfctl.md), le [API C#](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), le [API Java](/java/api/system.fabric._application_management_client) e le [API REST](/rest/api/servicefabric/). È possibile anche configurare pipeline di distribuzione/integrazione continua con strumenti quali [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) o [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

Il video di Microsoft Virtual Academy seguente illustra come gestire il ciclo di vita dell'applicazione:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-applications-and-services"></a>Testare applicazioni e servizi
Per creare servizi realmente estesi a livello di cloud, è fondamentale verificare che i servizi e le applicazioni siano in grado di resistere agli errori reali. Il servizio di analisi degli errori è progettato per testare servizi basati su Service Fabric. Con il [servizio di analisi degli errori](service-fabric-testability-overview.md) è possibile causare errori significativi ed eseguire scenari di test completi delle applicazioni. Tali errori e scenari verificano e convalidano i numerosi stati e le transizioni sperimentate da un servizio per la relativa durata, il tutto in modo controllato, sicuro e coerente.

Le [azioni](service-fabric-testability-actions.md) vengono usate su un servizio a scopo di test mediante singoli errori. Uno sviluppatore di servizi può utilizzarle come blocchi predefiniti per scrivere scenari complicati. Esempi di errori simulati sono:

* Riavviare un nodo per simulare un qualsiasi numero di situazioni in cui un computer o una VM viene riavviata.
* Spostare una replica del servizio con stato per simulare il bilanciamento del carico, il failover o l'aggiornamento dell'applicazione.
* Richiamare la perdita del quorum in un servizio con stato per creare una situazione in cui le operazioni di scrittura non possono continuare perché il numero delle repliche di "backup" o "secondarie" non è sufficiente per accettare nuovi dati.
* Richiamare la perdita di dati in un servizio con stato per creare una situazione in cui tutto lo stato in memoria sia completamente cancellato.

Gli [scenari](service-fabric-testability-scenarios.md) sono operazioni complesse costituite da una o più azioni. Il servizio di analisi degli errori offre due scenari completi incorporati:

* Lo [scenario Chaos](service-fabric-controlled-chaos.md) simula in tutto il cluster continui errori interfoliati, normali e anomali, per lunghi periodi di tempo.
* Lo [scenario di failover](service-fabric-testability-scenarios.md#failover-test) è una versione dello scenario di test chaos destinata a una specifica partizione del servizio che non ha effetti sugli altri servizi.

## <a name="clusters"></a>Cluster
Un [cluster di Service Fabric](service-fabric-deploy-anywhere.md) è un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi. I cluster possono supportare migliaia di macchine. Un computer o una VM che fa parte di un cluster è chiamato nodo cluster. A ogni nodo viene assegnato un nome (stringa). I nodi presentano delle caratteristiche, ad esempio le proprietà di posizionamento. In ogni computer o VM è disponibile un servizio di avvio automatico, `FabricHost.exe`, che viene eseguito all'avvio e che a sua volta avvia due eseguibili: Fabric.exe e FabricGateway.exe. Questi due eseguibili costituiscono il nodo. Negli scenari di test è possibile ospitare più nodi in un singolo PC o una singola macchina virtuale eseguendo più istanze di `Fabric.exe` e `FabricGateway.exe`.

È possibile creare cluster di Service Fabric su qualsiasi macchina virtuale o computer che esegue Windows Server o Linux. È quindi possibile distribuire ed eseguire applicazioni di Service Fabric in qualsiasi ambiente in cui è presente un set di computer Windows Server o Linux interconnessi, in locale, in Microsoft Azure o con qualsiasi provider di cloud.

Il video seguente di Microsoft Virtual Academy descrive i cluster di Service Fabric:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Cluster in Azure
L'esecuzione dei cluster di Service Fabric in Azure offre l'integrazione con altre funzionalità e servizi; rendendo più facili e affidabili le operazioni e la gestione del cluster. Un cluster è una risorsa di Azure Resource Manager, pertanto è possibile modellare i cluster come qualsiasi altra risorsa in Azure. Resource Manager fornisce inoltre una gestione semplificata di tutte le risorse utilizzate dal cluster come una singola unità. I cluster in Azure sono integrati con la diagnostica di Azure e Log Analytics. Poiché i tipi di nodo del cluster sono [set di scalabilità di macchina virtuale](/azure/virtual-machine-scale-sets/index), la funzionalità di scalabilità automatica è incorporata.

È possibile creare un cluster in Azure tramite il [portale di Azure](service-fabric-cluster-creation-via-portal.md), da un [modello](service-fabric-cluster-creation-via-arm.md) o da [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Service Fabric su Linux consente di compilare, distribuire e gestire applicazioni a disponibilità e scalabilità elevata in tale ambiente proprio come in Windows. I framework di Service Fabric (Reliable Services e Reliable Actors) sono disponibili in Java su Linux in aggiunta a C# (.NET Core). È anche possibile compilare [servizi eseguibili guest](service-fabric-guest-executables-introduction.md) con qualsiasi linguaggio o framework. È supportata anche l'orchestrazione di contenitori Docker. che possono eseguire eseguibili guest o servizi nativi di Service Fabric, che usano i framework di Service Fabric. Per altre informazioni, vedere [Service Fabric in Linux](service-fabric-deploy-anywhere.md).

Alcune funzionalità sono supportate in Windows, ma non in Linux. Per altre informazioni, vedere [Differenze tra Service Fabric in Linux e in Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Cluster autonomi
Service Fabric offre un pacchetto di installazione per creare cluster autonomi di Service Fabric in locale o con qualsiasi provider di cloud. I cluster autonomi offrono la libertà di ospitare un cluster ovunque si voglia. Se i dati sono soggetti a vincoli normativi o di conformità o si vuole mantenere i dati locali, è possibile ospitare il proprio cluster e le applicazioni. Le applicazioni Service Fabric possono essere eseguite in più ambienti di hosting senza modifiche, pertanto le competenze di creazione di applicazioni sono trasferibili da un ambiente di hosting all'altro. 

[Creare il primo cluster autonomo di Service Fabric](service-fabric-get-started-standalone-cluster.md)

I cluster Linux autonomi non sono ancora supportati.

### <a name="cluster-security"></a>Sicurezza del cluster
Per impedire a utenti non autorizzati di connettersi ai cluster, è necessario proteggerli, in particolare quando sono in esecuzione carichi di lavoro di produzione. La creazione di cluster non protetti, anche se possibile, consente a utenti anonimi di connettersi a un cluster che espone gli endpoint di gestione a Internet pubblico. Non è possibile abilitare la sicurezza in un cluster non protetto in un secondo momento: la protezione del cluster viene abilitata al momento della creazione del cluster stesso.

Gli scenari di sicurezza del cluster sono:
* Sicurezza da nodo a nodo
* Sicurezza da client a nodo
* Controllo degli accessi in base al ruolo

Per altre informazioni, vedere [Proteggere un cluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Ridimensionamento
Se si aggiungono nuovi nodi al cluster, Service Fabric ribilancerà le repliche e istanze di partizione sul nuovo e maggiore numero di nodi. Le prestazioni complessive dell'applicazione migliorano e la contesa per l'accesso alla memoria si riduce. Se i nodi del cluster non vengono usati in modo efficiente, è possibile ridurre il numero di nodi del cluster. Service Fabric ribilancia nuovamente le repliche e le istanze di partizione sul nuovo e minore numero di nodi, per usare al meglio l'hardware in ogni nodo. È possibile scalare i cluster in Azure [manualmente](service-fabric-cluster-scale-up-down.md) o [a livello di codice](service-fabric-cluster-programmatic-scaling.md). I cluster autonomi possono essere scalati [manualmente](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Aggiornamenti dei cluster
Periodicamente vengono rilasciate nuove versioni del runtime di Service Fabric. Eseguire gli aggiornamenti del runtime o dell'infrastruttura nel cluster in modo che sia sempre in esecuzione una [versione supportata](service-fabric-support.md). Oltre agli aggiornamenti dell'infrastruttura, è possibile aggiornare anche la configurazione del cluster, ad esempio i certificati o le porte delle applicazioni.

Un cluster di Service Fabric è una risorsa di proprietà dell'utente parzialmente gestita da Microsoft. Microsoft è responsabile dell'applicazione delle patch al sistema operativo sottostante e dell'implementazione degli aggiornamenti dell'infrastruttura sul cluster. È possibile configurare il cluster per ricevere gli aggiornamenti automatici dell'infrastruttura quando Microsoft rilascia una nuova versione oppure scegliere di selezionare la versione supportata dell'infrastruttura che si vuole. Gli aggiornamenti di infrastruttura e configurazione possono essere configurati tramite il portale di Azure o Resource Manager. Per altre informazioni, vedere [Aggiornare un cluster di Service Fabric](service-fabric-cluster-upgrade.md). 

Un cluster autonomo è una risorsa che si possiede interamente. Perciò si è responsabili dell'applicazione delle patch al sistema operativo sottostante e dell'avvio degli aggiornamenti dell'infrastruttura. Se il cluster può connettersi a [https://www.microsoft.com/download](https://www.microsoft.com/download), è possibile configurare il cluster in modo che scarichi ed esegua automaticamente il provisioning del nuovo pacchetto di runtime di Service Fabric. Quindi si avvierà l'aggiornamento. Se il cluster non può accedere a [https://www.microsoft.com/download](https://www.microsoft.com/download), è possibile scaricare manualmente il nuovo pacchetto di runtime da un computer connesso a Internet e quindi avviare l'aggiornamento. Per altre informazioni, vedere [Aggiornare il cluster autonomo di Azure Service Fabric in Windows Server](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitoraggio dell’integrità
In Service Fabric è disponibile un [modello di integrità](service-fabric-health-introduction.md) progettato per contrassegnare condizioni di non integrità di cluster e applicazioni in entità specifiche, come i nodi cluster e le repliche dei servizi. Il modello di integrità usa i reporter di integrità, costituiti da watchdog e componenti di sistema. Lo scopo è semplificare e velocizzare la diagnosi e la risoluzione dei problemi. Gli autori del servizio devono pensare in anticipo all'integrità e a come [progettare i report sull'integrità](service-fabric-report-health.md#design-health-reporting). È necessario segnalare tutte le condizioni che possono influire sull'integrità, soprattutto se aiutano a risalire alla causa dei problemi. Le informazioni sull'integrità consentono di risparmiare tempo ed energie per il debug e l'analisi quando il servizio sarà in esecuzione su vasta scala in produzione.

I generatori di report di Service Fabric eseguono il monitoraggio di condizioni di particolare interesse. Generano report su tali condizioni in base alla visualizzazione locale. L'[archivio integrità](service-fabric-health-introduction.md#health-store) aggrega i dati sull'integrità inviati da tutti i reporter per determinare se le entità sono complessivamente integre. Il modello è concepito per essere completo, flessibile e facile da usare. La qualità dei report sull'integrità determina il livello di accuratezza della visualizzazione dell'integrità del cluster. I falsi positivi che visualizzano erroneamente problemi di non integrità possono influire negativamente sugli aggiornamenti o su altri servizi che usano i dati di integrità, come ad esempio i servizi di ripristino e i meccanismi di avviso. Pertanto, è necessario creare report che segnalino le condizioni a cui si è interessati nel miglior modo possibile.

I report possono essere creati da:
* La replica o istanza del servizio di Service Fabric monitorato.
* Watchdog interni distribuiti come servizio di Service Fabric, ad esempio un servizio senza stato di Service Fabric che monitora le condizioni e genera i report. Il watchdog può essere distribuito su tutti i nodi o è possibile creare un'affinità con il servizio monitorato.
* Watchdog interni eseguiti sui nodi di Service Fabric ma non implementati come servizi di Service Fabric.
* Watchdog esterni che interrogano la risorsa dall'esterno del cluster di Service Fabric, ad esempio un servizio di monitoraggio come Gomez.

I componenti di Azure Service Fabric forniscono report sull'integrità predefiniti su tutte le entità del cluster. I [report sull'integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) forniscono la visibilità delle funzionalità del cluster e dell'applicazione e contrassegnano i problemi riscontrati a livello di integrità. Per le applicazioni e i servizi, i report sull'integrità del sistema verificano che le entità siano implementate e si comportino correttamente dal punto di vista del runtime di Service Fabric. I report non forniscono il monitoraggio dell'integrità della logica di business del servizio o il rilevamento dei processi bloccati. Per aggiungere informazioni di integrità specifiche della logica del servizio, [implementare report sull'integrità personalizzati](service-fabric-report-health.md) nei servizi.

Service Fabric offre diversi modi per [visualizzare i report sull'integrità](service-fabric-view-entities-aggregated-health.md) aggregati nell'archivio di integrità:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) o altri strumenti di visualizzazione.
* Query di integrità (tramite [PowerShell](/powershell/module/ServiceFabric/), l'[interfaccia della riga di comando](service-fabric-sfctl.md), le [API FabricClient C#](/dotnet/api/system.fabric.fabricclient.healthclient) e le [API FabricClient Java](/java/api/system.fabric._health_client) o l'[API REST](/rest/api/servicefabric)).
* Query generali che restituiscono un elenco di entità per le quali l'integrità costituisce una proprietà (tramite PowerShell, l'interfaccia della riga di comando, API o REST).

Il video seguente di Microsoft Virtual Academy descrive i concetti del modello di integrità di Service Fabric e il suo uso: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="monitoring-and-diagnostics"></a>Monitoraggio e diagnostica
Il [monitoraggio e la diagnostica](service-fabric-diagnostics-overview.md) sono essenziali per lo sviluppo, il test e la distribuzione di applicazioni e servizi in qualsiasi ambiente. Le soluzioni di Service Fabric funzionano meglio quando si pianifica e si implementa il monitoraggio e la diagnostica che consentono di verificare che le applicazioni e i servizi funzionino come previsto in un ambiente di sviluppo locale o in fase di produzione.

Gli obiettivi principali di monitoraggio e diagnostica sono:

- Rilevare e diagnosticare i problemi di hardware e infrastruttura
- Rilevare i problemi di software e app, ridurre i tempi di inattività del servizio
- Comprendere l'utilizzo delle risorse e supportare le decisioni operative
- Ottimizzare le prestazioni delle applicazioni, dei servizi e delle infrastrutture
- Generare informazioni aziendali dettagliate e identificare le aree di miglioramento
 
Il flusso di lavoro generale di monitoraggio e diagnostica è costituito da tre passaggi:

1. Generazione di eventi: sono inclusi eventi, ovvero log, tracce, eventi personalizzati, sia a livello di infrastruttura, cioè cluster, che a livello di piattaforma e applicazione o servizio
2. Aggregazione di eventi: gli eventi generati devono essere raccolti e aggregati prima di poter essere visualizzati
3. Analisi: gli eventi devono essere visualizzati e devono essere accessibili in un formato, per consentirne l'analisi e la visualizzazione in base alle esigenze specifiche

Sono disponibili molti prodotti che coprono le tre aree. Gli utenti sono liberi di scegliere tecnologie diverse per ognuno di essi. Per altre informazioni leggere [Monitoraggio e diagnostica in Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come creare un [cluster in Azure](service-fabric-cluster-creation-via-portal.md) o un [cluster autonomo in Windows](service-fabric-cluster-creation-for-windows-server.md).
* Provare a creare un servizio con il modello di programmazione[Reliable Services](service-fabric-reliable-services-quick-start.md) o [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Informazioni su come [eseguire la migrazione da Servizi cloud](service-fabric-cloud-services-migration-differences.md).
* Informazioni su come [monitorare e diagnosticare i servizi](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Informazioni su come [testare app e servizi](service-fabric-testability-overview.md).
* Informazioni su come [gestire e orchestrare le risorse del cluster](service-fabric-cluster-resource-manager-introduction.md).
* Esaminare gli [esempi di Service Fabric](http://aka.ms/servicefabricsamples).
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md).
* Leggere il [blog del team](https://blogs.msdn.microsoft.com/azureservicefabric/) per articoli e annunci.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
