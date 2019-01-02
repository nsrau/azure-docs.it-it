---
title: Panoramica di monitoraggio e diagnostica di Azure Service Fabric | Microsoft Docs
description: Informazioni sul monitoraggio e la diagnostica per i cluster, le applicazioni e i servizi di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 8d6865349f103278131a02c2385557fb53ee24f5
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720593"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoraggio e diagnostica in Azure Service Fabric

Questo articolo offre una panoramica del monitoraggio e della diagnostica per Azure Service Fabric. Il monitoraggio e la diagnostica sono essenziali per lo sviluppo, il test e la distribuzione di carichi di lavoro in qualsiasi ambiente cloud. Ad esempio, è possibile monitorare le modalità di utilizzo delle applicazioni, le azioni eseguite dalla piattaforma Service Fabric, l'utilizzo delle risorse con i contatori delle prestazioni e l'integrità generale del cluster. È possibile usare queste informazioni per diagnosticare e correggere eventuali problemi e per evitare che si verifichino in futuro. Nelle sezioni seguenti viene descritta brevemente ogni area del monitoraggio di Service Fabric da considerare per i carichi di lavoro di produzione. 

## <a name="application-monitoring"></a>Monitoraggio delle applicazioni
Il monitoraggio delle applicazioni tiene traccia del modo in cui vengono usati le funzionalità e i componenti dell'applicazione. Con il monitoraggio delle applicazioni è possibile, ad esempio, identificare i problemi che possono incidere sugli utenti. La responsabilità del monitoraggio di un'applicazione è dell'utente che l'ha sviluppata insieme ai relativi servizi, in quanto è esclusivo della logica di business dell'applicazione. Il monitoraggio delle applicazioni può essere utile negli scenari seguenti:
* Quanto traffico si riscontra nell'applicazione? È necessario ridimensionare i servizi per soddisfare le esigenze degli utenti o risolvere un potenziale collo di bottiglia nell'applicazione?
* Le chiamate da servizio a servizio vengono eseguite correttamente e monitorate?
* Quali azioni vengono eseguite dagli utenti dell'applicazione? La raccolta dei dati di telemetria può indirizzare lo sviluppo di funzionalità future e migliorare la diagnostica degli errori delle applicazioni.
* L'applicazione genera eccezioni non gestite? 
* Cosa accade all'interno dei servizi eseguiti nei contenitori?

Uno dei lati positivi del monitoraggio delle applicazioni è che gli sviluppatori possono usare gli strumenti e il framework che preferiscono, dato che questa funzionalità è intrinseca al contesto dell'applicazione. Per altre informazioni sulla soluzione di Azure per il monitoraggio delle applicazioni con Monitoraggio di Azure - Application Insights, vedere [Analisi e visualizzazione degli eventi con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
È disponibile anche un'esercitazione su come [configurare il monitoraggio per le applicazioni .NET](service-fabric-tutorial-monitoring-aspnet.md). Questa esercitazione spiega come installare gli strumenti corretti, fornisce un esempio di scrittura dei dati di telemetria personalizzati nell'applicazione e visualizza i dati di diagnostica e telemetria dell'applicazione nel portale di Azure. 


## <a name="platform-cluster-monitoring"></a>Monitoraggio della piattaforma (cluster)
L'utente ha il controllo dei dati di telemetria che provengono dalla sua applicazione, in quanto si occupa personalmente della scrittura del codice. Ma cosa dire dei dati di diagnostica della piattaforma Service Fabric? Uno dei principali obiettivi di Service Fabric è mantenere le applicazioni resilienti agli errori hardware. Questo obiettivo viene conseguito grazie alla capacità dei servizi di sistema della piattaforma di rilevare eventuali problemi dell'infrastruttura ed eseguire rapidamente il failover dei carichi di lavoro su altri nodi del cluster. In questo caso specifico, tuttavia, cosa accade se sono presenti problemi anche nei servizi di sistema? O se, nel tentativo di distribuire o spostare un carico di lavoro, vengono violate le regole relative al posizionamento dei servizi? Service Fabric offre funzionalità di diagnostica per questi e altri problemi per garantire che l'utente sia sempre informato sulle attività che hanno luogo nel cluster. Ecco alcuni scenari di esempio per il monitoraggio del cluster:

Service Fabric offre un set completo di eventi immediatamente disponibili. Tali [eventi di Service Fabric](service-fabric-diagnostics-events.md) sono accessibili tramite EventStore o il canale operativo (canale di evento esposto dalla piattaforma). 
* EventStore - EventStore è una funzionalità offerta dalla piattaforma che fornisce gli eventi della piattaforma Service Fabric disponibili in Service Fabric Explorer e tramite l'API REST. È possibile visualizzare uno snapshot di cosa sta succedendo nel cluster per ogni entità, ad esempio nodo, servizio, applicazione e query in base all'ora dell'evento. Per altre informazioni su EventStore, vedere [Panoramica di EventStore](service-fabric-diagnostics-eventstore.md).    

* Eventi di canale di Service Fabric - In Windows, sono disponibili da un unico provider ETW con un set di `logLevelKeywordFilters` rilevanti che consente di scegliere tra canali operativi, dati e messaggistica. È così che gli eventi di Service Fabric in uscita vengono separati per essere filtrati in base alle esigenze. In Linux, tutti gli eventi di Service Fabric vengono forniti tramite LTTng e raccolti in una tabella di Archiviazione, da dove possono essere filtrati in base alle esigenze. Questi canali contengono eventi curati e strutturati che consentono di comprendere meglio lo stato del cluster. Il canale di diagnostica è abilitato per impostazione predefinita al momento della creazione di un cluster, nel corso della quale viene creata una tabella di archiviazione di Azure in cui vengono inviati gli eventi generati dai canali, su cui poter eseguire query in futuro. 

Le funzionalità di diagnostica fornite sono costituite da un set completo di eventi predefiniti. Questi [eventi di Service Fabric](service-fabric-diagnostics-events.md) illustrano le azioni eseguite dalla piattaforma su entità diverse, come Nodi, Applicazioni, Servizi, Partizioni e così via. Nell'ultimo scenario riportato sopra, se un nodo diventasse inattivo, la piattaforma genererebbe un evento `NodeDown` e si riceverebbe immediatamente una notifica dallo strumento di monitoraggio scelto. Altri esempi comuni sono `ApplicationUpgradeRollbackStarted` o `PartitionReconfigured` durante un failover. **Gli stessi eventi sono disponibili sia in cluster di Windows che in cluster di Linux.**

Gli eventi vengono inviati tramite canali standard sia su Windows che su Linux e possono essere letti da qualsiasi strumento di monitoraggio che li supporti. La soluzione di monitoraggio di Azure è Log Analytics. L'[integrazione di Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) include un dashboard operativo personalizzato per il cluster e alcune query di esempio da cui è possibile creare avvisi. Per altri concetti di monitoraggio del cluster, vedere [Monitoraggio del cluster e della piattaforma](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Monitoraggio dell’integrità
La piattaforma Service Fabric include un modello di integrità che fornisce report di integrità estendibili sullo stato delle entità presenti in un cluster. Ogni nodo, applicazione, servizio, partizione, replica o istanza presenta uno stato di integrità costantemente aggiornabile, che può essere "OK", "Avviso" o "Errore". Gli eventi di Service Fabric possono essere definiti come operazioni eseguite dal cluster sulle varie entità, mentre l'integrità è uno stato che si applica a ogni entità. Ad ogni transizione dell'integrità di una particolare entità viene generato anche un evento. In questo modo è possibile configurare query e avvisi per gli eventi di integrità nello strumento di monitoraggio scelto, come avviene con qualsiasi altro evento. 

Gli utenti possono inoltre eseguire l'override dell'integrità delle entità. Se l'applicazione è in fase di aggiornamento e non supera i test di convalida, è possibile scrivere nel servizio di monitoraggio dell'integrità di Service Fabric usando l'API Integrità per indicare che l'applicazione non è più integra, e Service Fabric ripristinerà automaticamente lo stato precedente all'aggiornamento. Per altre informazioni sul modello di integrità, vedere [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md).

![Dashboard dell'integrità di SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Watchdog
In genere, un watchdog è un servizio separato che può controllare l'integrità e il carico tra servizi, eseguire il ping degli endpoint e creare report di integrità per qualsiasi elemento del cluster. Questo consente di evitare errori che non verrebbero rilevati visualizzando un singolo servizio. Anche i watchdog sono utili per ospitare il codice che esegue azioni correttive senza l'intervento dell'utente, ad esempio, la cancellazione dei file di log nell'archiviazione a determinati intervalli di tempo. È possibile trovare un'implementazione di esempio del servizio watchdog [qui](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitoraggio dell'infrastruttura (prestazioni)
Ora che conosciamo le funzionalità di diagnostica disponibili nell'applicazione e nella piattaforma, come possiamo stabilire se l'hardware funziona nel modo previsto? Il monitoraggio dell'infrastruttura sottostante è un fattore essenziale per conoscere lo stato del cluster e le modalità di utilizzo delle risorse. La misurazione delle prestazioni del sistema dipende da numerosi fattori che possono essere soggettivi a seconda dei carichi di lavoro. Questi fattori vengono in genere misurati tramite contatori delle prestazioni. Questi contatori delle prestazioni possono avere varie origini, ad esempio il sistema operativo, .NET Framework o la piattaforma Service Fabric stessa. Ecco alcuni scenari in cui possono essere utili.

* L'hardware viene utilizzato in modo efficiente? Si può scegliere di usare l'hardware al 90% della CPU o al 10%. Risulta utile quando occorre ridimensionare il cluster o ottimizzare i processi dell'applicazione.
* È possibile prevedere i problemi dell'infrastruttura in modo proattivo? Molti problemi sono preceduti da improvvise variazioni (cali) delle prestazioni, quindi è possibile usare contatori delle prestazioni come l'I/O di rete e l'utilizzo della CPU per anticipare e diagnosticare eventuali problemi in modo proattivo.

Un elenco dei contatori delle prestazioni che è possibile raccogliere a livello di infrastruttura è disponibile in [Metriche delle prestazioni](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric fornisce anche un set di contatori delle prestazioni per i modelli di programmazione Reliable Services e Reliable Actors. Se si usa uno di questi modelli, i contatori delle prestazioni possono fornire informazioni in grado di assicurare che gli attori vengano attivati e disattivati correttamente o che le richieste di Reliable Services vengano gestite in modo sufficientemente rapido. Per altre informazioni, vedere [Monitoraggio delle prestazioni per Reliable Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) e [Monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

La soluzione di Monitoraggio di Azure che raccoglie queste informazioni è Log Analytics, allo stesso modo del monitoraggio a livello di piattaforma. Usare l'[agente di Log Analytics](service-fabric-diagnostics-oms-agent.md) per raccogliere i contatori delle prestazioni appropriati e visualizzarli in Log Analytics.

## <a name="recommended-setup"></a>Configurazione consigliata
Completata la descrizione di ogni area di monitoraggio, con i relativi scenari di esempio, ecco un riepilogo degli strumenti di monitoraggio di Azure e della configurazione necessari per monitorare tutte queste aree. 

* Monitoraggio delle applicazioni con [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Monitoraggio dei cluster con l'[agente di Diagnostica](service-fabric-diagnostics-event-aggregation-wad.md) e [Log Analytics](service-fabric-diagnostics-oms-setup.md)
* Monitoraggio dell'infrastruttura con [Log Analytics](service-fabric-diagnostics-oms-agent.md)

È anche possibile usare e modificare il modello ARM di esempio disponibile [qui](service-fabric-diagnostics-oms-setup.md#deploy-log-analytics-with-azure-resource-manager) per automatizzare la distribuzione di tutte le risorse e gli agenti necessari. 

## <a name="other-logging-solutions"></a>Altre soluzioni di registrazione

Anche se le due soluzioni consigliate, [Azure Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) e [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), hanno l'integrazione predefinita con Service Fabric, diversi eventi vengono scritti tramite provider ETW e sono estendibili con altre soluzioni di registrazione. È opportuno prendere in considerazione anche [Elastic Stack](https://www.elastic.co/products), soprattutto se si intende eseguire un cluster in un ambiente offline, [Dynatrace](https://www.dynatrace.com/) o qualsiasi altra piattaforma preferita. Un elenco di partner integrati è disponibile [qui](service-fabric-diagnostics-partners.md).

Nella scelta della piattaforma si deve considerare la familiarità con l'interfaccia utente, le funzionalità di query, le visualizzazioni personalizzate e i dashboard disponibili e gli strumenti aggiuntivi offerti per migliorare l'esperienza di monitoraggio. 

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla strumentazione delle applicazioni, vedere [Generazione di eventi e log a livello di applicazione](service-fabric-diagnostics-event-generation-app.md).
* Completare i passaggi per configurare Application Insights per l'applicazione in [Monitorare e diagnosticare un'applicazione ASP.NET Core in Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Per altre informazioni sul monitoraggio della piattaforma e gli eventi messi a disposizione da Service Fabric, vedere [Generazione di eventi e log a livello piattaforma](service-fabric-diagnostics-event-generation-infra.md).
* Configurare l'integrazione di Log Analytics con Service Fabric in [Configurare Log Analytics per un cluster](service-fabric-diagnostics-oms-setup.md)
* Per informazioni su come configurare Log Analytics per il monitoraggio dei contenitori, vedere [Esercitazione: Monitorare i contenitori di Windows in Service Fabric usando Log Analytics](service-fabric-tutorial-monitoring-wincontainers.md).
* Vedere gli esempi di problemi di diagnostica e le soluzioni con Service Fabric negli [ scenari comuni di diagnosi](service-fabric-diagnostics-common-scenarios.md)
* Per informazioni sugli altri prodotti di diagnostica che si integrano con Service Fabric, vedere l'articolo sui [partner di diagnostica di Service Fabric](service-fabric-diagnostics-partners.md)
* Per suggerimenti generali sul monitoraggio delle risorse di Azure, vedere [Procedure consigliate: monitoraggio e diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 