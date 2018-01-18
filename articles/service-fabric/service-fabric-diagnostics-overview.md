---
title: Panoramica di monitoraggio e diagnostica di Azure Service Fabric | Microsoft Docs
description: Informazioni sul monitoraggio e la diagnostica per i cluster, le applicazioni e i servizi di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: 43a45a31efffcd623e6381049876c3607663ec4f
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoraggio e diagnostica in Azure Service Fabric

Questo articolo fornisce una panoramica del processo di configurazione delle operazioni di monitoraggio e diagnostica per le applicazioni in esecuzione in cluster Service Fabric. Il monitoraggio e la diagnostica sono essenziali per lo sviluppo, il test e la distribuzione di carichi di lavoro in qualsiasi ambiente cloud. Il monitoraggio, in particolare, consente di tenere traccia delle modalità di utilizzo delle applicazioni e delle risorse e dell'integrità complessiva del cluster. È possibile usare queste informazioni per diagnosticare e correggere eventuali problemi nel cluster e per evitare che si verifichino nuovi problemi in futuro. 

Gli obiettivi principali di monitoraggio e diagnostica sono:
* Rilevare e diagnosticare problemi di infrastruttura
* Rilevare problemi nell'applicazione
* Comprendere l'utilizzo delle risorse
* Monitorare le prestazioni delle applicazioni, dei servizi e delle infrastrutture

In un cluster di Service Fabric, i dati di monitoraggio e di diagnostica provengono da tre livelli: applicazione, piattaforma (cluster) e infrastruttura. 
* Il [livello applicazione](service-fabric-diagnostics-event-generation-app.md) include i dati relativi alle prestazioni delle applicazioni ed eventuali registrazioni personalizzate aggiunte. I dati di monitoraggio delle applicazioni devono essere trasmessi dall'applicazione ad Application Insights (AI) tramite AI SDK, EventFlow o un'altra pipeline di propria scelta.
* Il [livello piattaforma](service-fabric-diagnostics-event-generation-infra.md) include gli eventi generati dalle azioni intraprese nel cluster e correlati alla gestione del cluster e alle applicazioni in esecuzione sul cluster. I dati di monitoraggio della piattaforma devono essere inviati a OMS Log Analytics con la Soluzione Analisi Service Fabric per consentire la visualizzazione degli eventi in ingresso. Questi dati, in genere, vengono inviati a un account di archiviazione tramite l'estensione Diagnostica di Azure per Window o Linux, da dove è possibile accedervi tramite OMS Log Analytics. 
* Il livello infrastruttura è incentrato sul [monitoraggio delle prestazioni](service-fabric-diagnostics-event-generation-perf.md) e fa riferimento alle metriche principali e ai contatori delle prestazioni per determinare il carico e l'utilizzo delle risorse. Per acquisire il monitoraggio delle prestazioni è possibile usare un agente. È consigliabile usare l'agente OMS (Microsoft Monitoring) perché, in questo modo, i dati sulle prestazioni vengono trasmessi nella stessa posizione degli eventi della piattaforma ed è possibile correlare le modifiche apportate all'interno di un cluster, con un conseguente miglioramento dell'esperienza di diagnostica. 

![Grafico di panoramica della diagnostica](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>Scenari di monitoraggio

Questa sezione illustra i principali scenari relativi al monitoraggio di un cluster di Service Fabric: applicazione, cluster, prestazioni e monitoraggio dello stato. Per ogni scenario vengono descritti l'intento e l'approccio complessivo. Altre informazioni su questi e altri suggerimenti generali sul monitoraggio delle risorse di Azure sono disponibili in [Indicazioni di monitoraggio e di diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 

Questi scenari sono associati in modo generico ai tre livelli di dati di monitoraggio e diagnostica illustrati in precedenza. Per consentire una corretta gestione di ogni scenario, ad esempio, è necessario avere a disposizione i dati di monitoraggio e diagnostica provenienti dal livello corrispondente. Fa eccezione lo scenario di monitoraggio dello stato, poiché fa riferimento all'intero cluster, con tutti gli elementi in esecuzione al suo interno.

## <a name="application-monitoring"></a>Monitoraggio delle applicazioni
Il monitoraggio delle applicazioni tiene traccia del modo in cui vengono usate le funzionalità e i componenti di un'applicazione compilata. Con il monitoraggio delle applicazioni è possibile, ad esempio, identificare i problemi che possono incidere sugli utenti. Il monitoraggio delle applicazioni può essere utile per:
* Determinare il carico dell'applicazione e il traffico utente. È necessario ridimensionare i servizi per soddisfare le esigenze dell'utente o risolvere un potenziale collo di bottiglia nell'applicazione?
* Identificare possibili problemi con le comunicazioni remote e dei servizi all'interno del cluster
* Capire il modo in cui gli utenti stanno usando l'applicazione: la strumentazione delle applicazioni semplifica lo sviluppo delle funzionalità future e migliora la diagnostica degli errori nelle app

Per il monitoraggio al livello applicazione in Service Fabric è consigliabile usare Application Insights (AI). L'integrazione di AI con Service Fabric comprende l'utilizzo degli strumenti per Visual Studio e il portale di Azure e la conoscenza del contesto del servizio di Service Fabric e delle comunicazioni remote nel dashboard di AI e nella mappa delle applicazioni, determinando un'esperienza di registrazione completa fin dall'inizio. Sebbene molti log vengano creati e raccolti in modo automatico quando si usa AI, si consiglia di aggiungere altre registrazioni personalizzate alle applicazioni, da visualizzare in AI insieme ai dati forniti di serie, in modo da creare un'esperienza di diagnostica più completa per la gestione dei problemi futuri. Altre informazioni sull'utilizzo di AI con Service Fabric sono disponibili in [Analisi e visualizzazione degli eventi con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md). 

Per un'introduzione alla strumentazione del codice per monitorare le applicazioni, vedere [Generazione di eventi e log a livello di applicazione](service-fabric-diagnostics-event-generation-app.md). 

### <a name="monitoring-application-availability"></a>Monitoraggio della disponibilità delle applicazioni
È possibile che tutti gli elementi del cluster funzionino apparentemente come previsto e risultino perfettamente integri, ma in realtà le applicazioni non sono accessibili o raggiungibili. Sebbene non siano molte le condizioni in cui si può verificare questa situazione, è importante conoscere i casi in cui può verificarsi in modo da poter contenere il problema appena possibile. Il monitoraggio della disponibilità è strettamente correlato al monitoraggio della disponibilità dei componenti del sistema e consente di comprendere il tempo di attività complessivo del sistema. In un cluster si concentra l'attenzione sulla disponibilità dal punto di vista dell'applicazione: la piattaforma deve garantire che gli scenari relativi alla gestione del ciclo di vita dell'applicazione non provochino tempi di inattività. Alcuni problemi del cluster, tuttavia, possono compromettere i tempi di attività dell'applicazione e, in questo caso, il proprietario dell'applicazione desidera venirne subito a conoscenza. Per questo motivo, insieme a tutte le altre applicazioni, è consigliabile distribuire nel cluster anche un watchdog, con l'incarico di controllare gli endpoint dell'applicazione appropriati a intervalli regolari e specificare se sono accessibili. Per compiere questa operazione è possibile anche usare un servizio esterno che esegue il ping dell'endpoint e restituisce un report in base all'intervallo di tempo specificato.

## <a name="cluster-monitoring"></a>Monitoraggio del cluster
Il monitoraggio del cluster di Service Fabric è fondamentale per avere la certezza che la piattaforma e tutti i carichi di lavoro presenti al suo interno siano in esecuzione come previsto. Uno dei principali obiettivi di Service Fabric è mantenere le applicazioni in esecuzione anche in caso di errori hardware. Questo risultato viene conseguito grazie alla capacità dei servizi di sistema della piattaforma di rilevare eventuali problemi dell'infrastruttura ed eseguire rapidamente il failover dei carichi di lavoro su altri nodi del cluster. In questo caso specifico, tuttavia, cosa accade se sono presenti problemi anche nei servizi di sistema? O se, nel tentativo di spostare un carico di lavoro, vengono violate le regole relative al posizionamento dei servizi? Il monitoraggio del cluster consente di essere sempre aggiornati sulle attività in esecuzione sul cluster, condizione essenziale per diagnostica più facilmente i problemi e correggerli in modo efficace. Di seguito sono elencati alcuni aspetti a cui è necessario prestare particolare attenzione:
* Service Fabric sta realmente funzionando come previsto, in termini di posizionamento delle applicazioni e bilanciamento del carico nel cluster? 
* Vengono intraprese azioni per modificare la configurazione del cluster che viene riconosciuto e su cui si agisce come previsto? Questo aspetto è particolarmente importante quando si ridimensiona un cluster.
* Service Fabric sta gestendo i dati e le comunicazioni tra i servizi all'interno del cluster in modo corretto?

Tramite i canali operativi e di dati e messaggistica, Service Fabric offre un set completo di eventi predefiniti. In Windows, questi eventi vengono forniti sotto forma di un unico provider ETW con un set di filtri `logLevelKeywordFilters` rilevanti, che consentono di scegliere tra canali diversi. In Linux, tutti gli eventi della piattaforma vengono forniti tramite LTTng e raccolti in una tabella, da dove possono essere filtrati in base alle esigenze. 

Questi canali contengono eventi curati e strutturati che consentono di comprendere meglio lo stato del cluster. Il canale di diagnostica è abilitato per impostazione predefinita al momento della creazione di un cluster, nel corso della quale viene definita una tabella di archiviazione di Azure in cui vengono inviati gli eventi generati dai canali, su cui poter eseguire query in futuro. È possibile leggere altre informazioni sul monitoraggio del cluster nell'articolo [Generazione di eventi e log a livello piattaforma](service-fabric-diagnostics-event-generation-infra.md). Per il monitoraggio del cluster è consigliabile usare OMS Log Analytics, poiché offre Analisi Service Fabric, una soluzione specifica per Service Fabric che fornisce un dashboard personalizzato per il monitoraggio dei cluster di Service Fabric e consente di eseguire query sugli eventi del cluster e di impostare avvisi. Per altre informazioni, vedere [Analisi di eventi con OMS](service-fabric-diagnostics-event-analysis-oms.md). 

### <a name="watchdogs"></a>Watchdog
In genere, un watchdog è un servizio separato che può controllare l'integrità e il carico tra servizi, eseguire il ping degli endpoint e creare report di integrità per qualsiasi elemento del cluster. Questo consente di evitare errori che non verrebbero rilevati visualizzando un singolo servizio. Anche i watchdog sono utili per ospitare il codice che esegue azioni correttive senza l'intervento dell'utente, ad esempio, la cancellazione dei file di log nell'archiviazione a determinati intervalli di tempo. È possibile trovare un'implementazione di esempio del servizio watchdog [qui](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="performance-monitoring"></a>Monitoraggio delle prestazioni
Il monitoraggio dell'infrastruttura sottostante è un fattore essenziale per conoscere lo stato del cluster e le modalità di utilizzo delle risorse. La misurazione delle prestazioni di sistema dipende da diversi fattori, ognuno dei quali viene misurato in genere tramite un indicatore di prestazioni chiave (KPI). Gli indicatori KPI relativi a Service Fabric, in particolare, possono essere mappati alle metriche che è possibile raccogliere dai nodi del cluster come contatori delle prestazioni.
Gli indicatori KPI possono essere utili per:
* Determinare il carico e l'utilizzo delle risorse ai fini del ridimensionamento del cluster o dell'ottimizzazione dei processi del servizio
* Anticipare eventuali problemi dell'infrastruttura: molti problemi sono preceduti da improvvise variazioni (cali) delle prestazioni ed è quindi possibile usare indicatori KPI come l'I/O di rete e l'utilizzo della CPU per anticipare e diagnosticare eventuali problemi infrastrutturali

Un elenco dei contatori delle prestazioni che è possibile raccogliere a livello di infrastruttura è disponibile in [Metriche delle prestazioni](service-fabric-diagnostics-event-generation-perf.md). 

Per il monitoraggio delle prestazioni al livello applicazione, Service Fabric fornisce un set di contatori delle prestazioni per i modelli di programmazione Reliable Services e Reliable Actors. Se si usa uno di questi modelli, i contatori delle prestazioni possono fornire indicatori KPI in grado di assicurare che gli attori vengano attivati e disattivati correttamente o che richieste di Reliable Services vengano gestite in modo sufficientemente rapido. Per altre informazioni su questi argomenti, vedere [Monitoraggio delle prestazioni per Reliable Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) e [Monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Se configurato con l'applicazione in uso, anche Application Insights offre un set di metriche delle prestazioni che è in grado di raccogliere.

Usare l'agente OMS per raccogliere i contatori delle prestazioni appropriati e visualizzare gli indicatori KPI in OMS Log Analytics. Per raccogliere e archiviare le metriche per l'analisi è possibile usare anche l'estensione Diagnostica di Azure (o qualsiasi altro agente simile). 

## <a name="health-monitoring"></a>Monitoraggio dell’integrità
La piattaforma Service Fabric include un modello di integrità che fornisce report di integrità estendibili sullo stato delle entità presenti in un cluster. Ogni nodo, applicazione, servizio, partizione, replica o istanza presenta uno stato di integrità costantemente aggiornabile, che può essere "OK", "Avviso" o "Errore". Lo stato di integrità viene modificato tramite report di integrità generati per ogni entità, sulla base dei problemi presenti nel cluster. Lo stato di integrità delle entità può essere verificato in qualsiasi momento in Service Fabric Explorer (SFX), come illustrato di seguito. In alternativa, è possibile eseguire una query tramite l'API di integrità delle piattaforme. È possibile anche personalizzare i report di integrità e modificare lo stato di integrità di un'entità aggiungendo report di integrità personali o tramite l'API di integrità. Altre informazioni sul modello di integrità sono disponibili in [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md).

![Dashboard dell'integrità di SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

I report di integrità più recenti possono essere visualizzati in SFX, ma ogni report è disponibile anche come evento. Gli eventi di integrità possono essere raccolti tramite il canale operativo (vedere [Aggregazione di eventi con Diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md#collect-health-and-load-events)) e archiviati in OMS Log Analytics per consentire l'impostazione di avvisi e l'esecuzione di query in futuro. In questo modo, è possibile anche rilevare eventuali problemi che possono incidere sulla disponibilità dell'applicazione ed è per questo che si consiglia di impostare avvisi per gli scenari di errore appropriati (avvisi personalizzati tramite OMS).

## <a name="monitoring-workflow"></a>Flusso di lavoro di monitoraggio 

Il flusso di lavoro generale di monitoraggio e diagnostica è costituito da tre passaggi:

1. **Generazione di eventi**: sono inclusi eventi, ovvero log, tracce ed eventi personalizzati, a tutti i tre livelli: infrastruttura, piattaforma (cluster) e applicazione
2. **Aggregazione di eventi**: questa fase è, in pratica, una pipeline per gli eventi destinati a essere raccolti in uno strumento in cui è possibile analizzarli, come l'estensione Diagnostica di Azure o EventFlow
3. **Analisi**: gli eventi devono essere accessibili in uno strumento per poter essere analizzati e poter quindi visualizzarli, eseguire query, impostare avvisi e così via.

Sono disponibili molti prodotti che coprono le tre aree. Gli utenti sono liberi di scegliere tecnologie diverse per ognuno di essi. Per disporre di una soluzione di monitoraggio end-to-end per il cluster, è importante assicurarsi che queste tecnologie funzionino in combinazione.

## <a name="event-generation"></a>Generazione di eventi

Il primo passaggio del flusso di lavoro di monitoraggio e diagnostica è l'impostazione dei processi di creazione e generazione dei dati di eventi e log. Gli eventi, i log e i contatori delle prestazioni vengono generati su tutti i tre livelli: il livello applicazione (qualsiasi strumentazione aggiunta alle app e ai servizi distribuiti nel cluster), il livello piattaforma (gli eventi generati dal cluster in base alle operazioni di Service Fabric) e il livello infrastruttura (metriche delle prestazioni generate da ogni nodo). I dati di diagnostica raccolti in ognuno di questi livelli possono essere personalizzati, anche se Service Fabric prevede alcune strumentazioni predefinite. 

Per comprendere ciò che viene offerto e come aggiungere altri strumenti, leggere altre informazioni su [eventi a livello piattaforma](service-fabric-diagnostics-event-generation-infra.md) ed [eventi a livello di applicazione](service-fabric-diagnostics-event-generation-app.md). La principale decisione da prendere, in questo caso, è come instrumentare l'applicazione. Per le applicazioni .NET è consigliabile usare ILogger, ma è possibile anche provare EventSource, Serilog e altre librerie simili. Per Java, è consigliabile usare Log4j. Oltre a queste, sono disponibili molte altre opzioni che è possibile usare in base alla natura dell'applicazione. È possibile usare l'opzione che si ritiene più adatta al proprio caso d'uso specifico o quella con cui si ha maggiore familiarità. 

Dopo aver deciso quale provider di log usare, è necessario assicurarsi che i log vengono aggregati e archiviati correttamente.

## <a name="event-aggregation"></a>Aggregazione di eventi

Per raccogliere i log e gli eventi generati dalle applicazioni e dal cluster, in genere è consigliabile usare l'[estensione Diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md) (simile alla raccolta di log basata su agenti) o [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (una raccolta di log nel processo). Se si usa Application Insights e un ambiente di sviluppo .NET o Java, è consigliabile usare Application Insights SDK piuttosto che EventFlow.

Sebbene sia possibile eseguire un processo di questo tipo usando solo una di queste opzioni, nella maggior parte dei casi combinando l'agente dell'estensione Diagnostica di Azure con una pipeline di raccolta nel processo (AI SDK o EventFlow) si ottiene un flusso di lavoro di monitoraggio più affidabile e completo. L'agente dell'estensione Diagnostica di Azure sarà il percorso per gli eventi a livello piattaforma, mentre AI SDK o EventFlow (la raccolta nel processo) sarà il percorso per i log a livello applicazione. 

Nel caso in cui si preferisca usare una sola opzione, è importante tenere presente i punti chiave seguenti.
* Nei servizi di Service Fabric è possibile raccogliere log di applicazioni tramite l'estensione Diagnostica di Azure se il set di origini e destinazioni dei log non cambia spesso e se il mapping tra le origini e le relative destinazioni è semplice. Questo perché la configurazione di Diagnostica di Azure avviene a livello di Resource Manager, pertanto per apportare modifiche importanti alla configurazione è necessario l'aggiornamento dell'intero cluster. Ecco perché spesso risulta meno efficiente rispetto all'uso di AI SDK o EventFlow.
* L'uso di EventFlow consente di inviare i log dei servizi direttamente a una piattaforma di analisi e visualizzazione e/o di archiviazione. Per eseguire questa operazione è possibile usare anche altre librerie, come ILogger, Serilog e così via, ma EventFlow ha il vantaggio di essere stato appositamente progettato per la raccolta di log nel processo e per supportare i servizi di Service Fabric. Questo comporta diversi vantaggi in termini di facilità di distribuzione e configurazione e una maggiore flessibilità nel supporto di più librerie di registrazione e strumenti di analisi. 

## <a name="event-analysis"></a>Analisi di eventi

Per quanto riguarda l'analisi e la visualizzazione dei dati di monitoraggio e diagnostica, nel mercato esistono diverse piattaforme molto funzionali. Le due piattaforme consigliate sono [OMS](service-fabric-diagnostics-event-analysis-oms.md) e [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), poiché si integrano bene con Service Fabric, ma è opportuno prendere in considerazione anche [Elastic Stack](https://www.elastic.co/products), soprattutto se si intende eseguire un cluster in un ambiente non in linea, [Splunk](https://www.splunk.com/) o qualsiasi altra piattaforma preferita. 

Nella scelta della piattaforma si deve considerare l'interfaccia utente e le opzioni di query, la possibilità di visualizzare i dati e creare dashboard facilmente leggibili, oltre agli strumenti aggiuntivi offerti per migliorare il monitoraggio, ad esempio gli avvisi automatici.

Oltre alla piattaforma scelta, quando si configura un cluster di Service Fabric come una risorsa di Azure, è anche possibile accedere al servizio di monitoraggio pronto all'uso di Azure per i computer.

### <a name="azure-monitor"></a>Monitoraggio di Azure

È possibile usare [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md) per monitorare molte risorse di Azure in cui viene creato un cluster di Service Fabric. Un set di metriche per il [set di scalabilità di macchine virtuali](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) e per le singole [macchine virtuali](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) viene raccolto automaticamente e visualizzato nel portale di Azure. Per visualizzare le informazioni raccolte, nel portale di Azure selezionare il gruppo di risorse che contiene il cluster di Service Fabric. Selezionare quindi il set di scalabilità di macchine virtuali da visualizzare. Nella sezione **Monitoraggio**, nel pannello di navigazione a sinistra selezionare **Metriche** per visualizzare un grafico dei valori.

![Visualizzazione del portale di Azure dei dati di metrica raccolti](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Per personalizzare i grafici, seguire le istruzioni disponibili in [Metriche in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). È anche possibile creare avvisi in base a queste metriche, come illustrato in [Creare avvisi in Monitoraggio di Azure per servizi di Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). 

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul monitoraggio della piattaforma e gli eventi messi a disposizione da Service Fabric, vedere [Generazione di eventi e log a livello piattaforma](service-fabric-diagnostics-event-generation-infra.md)
* Per un'introduzione alla strumentazione delle applicazioni, vedere [Generazione di eventi e log a livello di applicazione](service-fabric-diagnostics-event-generation-app.md)
* Completare l'esercitazione [Monitorare e diagnosticare un'applicazione ASP.NET Core in Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)

