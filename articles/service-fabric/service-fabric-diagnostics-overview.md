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
ms.date: 04/25/2018
ms.author: srrengar
ms.openlocfilehash: f7fe07500f877cf34626e53361c9c68dd459a5e4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643176"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoraggio e diagnostica in Azure Service Fabric

Questo articolo offre una panoramica del monitoraggio e della diagnostica per Azure Service Fabric. Il monitoraggio e la diagnostica sono essenziali per lo sviluppo, il test e la distribuzione di carichi di lavoro in qualsiasi ambiente cloud. Il monitoraggio, in particolare, consente di tenere traccia delle modalità di utilizzo delle applicazioni e delle risorse e dell'integrità complessiva del cluster. È possibile usare queste informazioni per diagnosticare e correggere eventuali problemi e per evitare che si verifichino nuovi problemi in futuro. 

## <a name="application-monitoring"></a>Monitoraggio delle applicazioni
Il monitoraggio delle applicazioni tiene traccia del modo in cui vengono usati le funzionalità e i componenti dell'applicazione. Con il monitoraggio delle applicazioni è possibile, ad esempio, identificare i problemi che possono incidere sugli utenti. Il monitoraggio delle applicazioni può essere utile negli scenari seguenti:
* Determinare il carico dell'applicazione e il traffico utente. È necessario ridimensionare i servizi per soddisfare le esigenze dell'utente o risolvere un potenziale collo di bottiglia nell'applicazione?
* Identificare possibili problemi con le comunicazioni remote e dei servizi all'interno del cluster
* Capire il modo in cui gli utenti stanno usando l'applicazione: la raccolta dei dati di telemetria nelle applicazioni semplifica lo sviluppo delle funzionalità future e migliora la diagnostica degli errori nelle app
* Monitoraggio delle operazioni eseguite nei contenitori in esecuzione

Service Fabric supporta numerose opzioni per instrumentare il codice dell'applicazione con le tracce e i dati di telemetria appropriati. È consigliabile usare Application Insights (AI). L'integrazione di AI con Service Fabric include l'uso degli strumenti per Visual Studio e il portale di Azure, oltre a metriche specifiche di Service Fabric, determinando un'esperienza di registrazione completa fin dall'inizio. Nonostante molti log vengano creati e raccolti in modo automatico con AI, si consiglia di aggiungere altre registrazioni personalizzate alle applicazioni per creare un'esperienza di diagnostica più completa. Per altre informazioni su come iniziare a usare Application Insights con Service Fabric, vedere [Analisi e visualizzazione degli eventi con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="platform-cluster-monitoring"></a>Monitoraggio della piattaforma (cluster)
Il monitoraggio del cluster di Service Fabric è fondamentale per avere la certezza che la piattaforma e tutti i carichi di lavoro siano in esecuzione come previsto. Uno dei principali obiettivi di Service Fabric è mantenere le applicazioni resilienti agli errori hardware. Questo obiettivo viene conseguito grazie alla capacità dei servizi di sistema della piattaforma di rilevare eventuali problemi dell'infrastruttura ed eseguire rapidamente il failover dei carichi di lavoro su altri nodi del cluster. In questo caso specifico, tuttavia, cosa accade se sono presenti problemi anche nei servizi di sistema? O se, nel tentativo di spostare un carico di lavoro, vengono violate le regole relative al posizionamento dei servizi? Il monitoraggio del cluster consente di essere sempre aggiornati sulle attività in esecuzione sul cluster, condizione essenziale per diagnostica più facilmente i problemi e correggerli in modo efficace. Di seguito sono elencati alcuni aspetti a cui è necessario prestare particolare attenzione:
* Service Fabric sta realmente funzionando come previsto, in termini di posizionamento delle applicazioni e bilanciamento del carico nel cluster? 
* Le azioni utente effettuate nel cluster vengono confermate ed eseguite come previsto? Questo aspetto è particolarmente importante quando si ridimensiona un cluster.
* Service Fabric sta gestendo i dati e le comunicazioni tra i servizi all'interno del cluster in modo corretto?

Service Fabric offre un set completo di eventi immediatamente disponibili. Tali [eventi di Service Fabric](service-fabric-diagnostics-events.md) sono accessibili tramite le API EventStore o il canale operativo (canale di evento esposto dalla piattaforma). 
* EventStore - EventStore (disponibile su Windows 6.2 e versioni successive, su Linux ancora in corso alla data dell'ultimo aggiornamento dell'articolo) espone questi eventi tramite un set di API (accessibile tramite gli endpoint REST o la libreria client). Altre informazioni su EventStore sono disponibili nella [Panoramica di EventStore](service-fabric-diagnostics-eventstore.md).
* Eventi di canale di Service Fabric - In Windows, sono disponibili da un unico provider ETW con un set di `logLevelKeywordFilters` rilevanti che consente di scegliere tra canali operativi, dati e messaggistica. È così che gli eventi di Service Fabric in uscita vengono separati per essere filtrati in base alle esigenze. In Linux, tutti gli eventi di Service Fabric vengono forniti tramite LTTng e raccolti in una tabella di Archiviazione, da dove possono essere filtrati in base alle esigenze. Questi canali contengono eventi curati e strutturati che consentono di comprendere meglio lo stato del cluster. Il canale di diagnostica è abilitato per impostazione predefinita al momento della creazione di un cluster, nel corso della quale viene creata una tabella di archiviazione di Azure in cui vengono inviati gli eventi generati dai canali, su cui poter eseguire query in futuro. 

Si consiglia di usare EventStore per un'analisi veloce e per ottenere un'idea di snapshot del modo in cui il cluster sta funzionando e se le operazioni vengono eseguite come previsto. Per raccogliere i log e gli eventi che vengono generati dal cluster, è in genere consigliabile usare l'[estensione Diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md), che si integra bene con Analisi Service Fabric, la soluzione specifica per Service Fabric di OMS Log Analytics, che fornisce un dashboard personalizzato per il monitoraggio dei cluster di Service Fabric e consente di eseguire query sugli eventi del cluster e di impostare avvisi. Per altre informazioni, vedere [Analisi di eventi con OMS](service-fabric-diagnostics-event-analysis-oms.md). 

 È possibile leggere altre informazioni sul monitoraggio del cluster nell'articolo [Generazione di eventi e log a livello piattaforma](service-fabric-diagnostics-event-generation-infra.md).

## <a name="performance-monitoring"></a>Monitoraggio delle prestazioni
Il monitoraggio dell'infrastruttura sottostante è un fattore essenziale per conoscere lo stato del cluster e le modalità di utilizzo delle risorse. La misurazione delle prestazioni di sistema dipende da diversi fattori, ognuno dei quali viene misurato in genere tramite un indicatore di prestazioni chiave (KPI). Gli indicatori KPI relativi a Service Fabric, in particolare, possono essere mappati alle metriche che è possibile raccogliere dai nodi del cluster come contatori delle prestazioni.
Gli indicatori KPI possono essere utili per:
* Determinare il carico e l'utilizzo delle risorse ai fini del ridimensionamento del cluster o dell'ottimizzazione dei processi del servizio.
* Anticipare eventuali problemi dell'infrastruttura: molti problemi sono preceduti da improvvise variazioni (cali) delle prestazioni ed è quindi possibile usare indicatori KPI come l'I/O di rete e l'utilizzo della CPU per anticipare e diagnosticare eventuali problemi infrastrutturali.

Un elenco dei contatori delle prestazioni che è possibile raccogliere a livello di infrastruttura è disponibile in [Metriche delle prestazioni](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric fornisce un set di contatori delle prestazioni per i modelli di programmazione Reliable Services e Reliable Actors. Se si usa uno di questi modelli, i contatori delle prestazioni possono fornire indicatori KPI in grado di assicurare che gli attori vengano attivati e disattivati correttamente o che richieste di Reliable Services vengano gestite in modo sufficientemente rapido. Per altre informazioni, vedere [Monitoraggio delle prestazioni per Reliable Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) e [Monitoraggio delle prestazioni per Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Se configurato con l'applicazione in uso, anche Application Insights offre un set di metriche delle prestazioni che è in grado di raccogliere.

Usare l'[agente OMS](service-fabric-diagnostics-oms-agent.md) per raccogliere i contatori delle prestazioni appropriati e visualizzare gli indicatori KPI in OMS Log Analytics.

![Grafico di panoramica della diagnostica](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Monitoraggio dell’integrità
La piattaforma Service Fabric include un modello di integrità che fornisce report di integrità estendibili sullo stato delle entità presenti in un cluster. Ogni nodo, applicazione, servizio, partizione, replica o istanza presenta uno stato di integrità costantemente aggiornabile, che può essere "OK", "Avviso" o "Errore". Lo stato di integrità viene modificato tramite report di integrità generati per ogni entità, sulla base dei problemi presenti nel cluster. Lo stato di integrità delle entità può essere verificato in qualsiasi momento in Service Fabric Explorer (SFX), come illustrato di seguito. In alternativa, è possibile eseguire una query tramite l'API di integrità delle piattaforme. È possibile anche personalizzare i report di integrità e modificare lo stato di integrità di un'entità aggiungendo report di integrità personali o tramite l'API di integrità. Altre informazioni sul modello di integrità sono disponibili in [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md).

![Dashboard dell'integrità di SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

I report di integrità più recenti possono essere visualizzati in SFX, ma ogni report è disponibile anche come evento. Gli eventi di integrità possono essere raccolti tramite il canale operativo (vedere [Aggregazione di eventi con Diagnostica di Azure](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) e archiviati in Log Analytics per consentire l'impostazione di avvisi e l'esecuzione di query in futuro. In questo modo, è possibile anche rilevare eventuali problemi che possono incidere sulla disponibilità dell'applicazione ed è per questo che si consiglia di impostare avvisi per gli scenari di errore appropriati (avvisi personalizzati tramite Log Analytics).

## <a name="other-logging-solutions"></a>Altre soluzioni di registrazione

Anche se le due soluzioni consigliate, [OMS](service-fabric-diagnostics-event-analysis-oms.md) e [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), hanno l'integrazione predefinita con Service Fabric, diversi eventi vengono scritti tramite provider ETW e sono estendibili con altre soluzioni di registrazione. È opportuno prendere in considerazione anche [Elastic Stack](https://www.elastic.co/products), soprattutto se si intende eseguire un cluster in un ambiente offline, [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/) o qualsiasi altra piattaforma preferita. 

Nella scelta della piattaforma si deve considerare l'interfaccia utente e le opzioni di query, la possibilità di visualizzare i dati e creare dashboard facilmente leggibili, oltre agli strumenti aggiuntivi offerti per migliorare il monitoraggio, ad esempio gli avvisi automatici.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla strumentazione delle applicazioni, vedere [Generazione di eventi e log a livello di applicazione](service-fabric-diagnostics-event-generation-app.md).
* Per altre informazioni sul monitoraggio della piattaforma e gli eventi messi a disposizione da Service Fabric, vedere [Generazione di eventi e log a livello piattaforma](service-fabric-diagnostics-event-generation-infra.md).
* Completare i passaggi per configurare AI con [Monitorare e diagnosticare un'applicazione ASP.NET Core in Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Informazioni su come configurare Log Analytics OMS per il monitoraggio dei contenitori: [Monitoraggio e diagnostica per i contenitori di Windows in Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Per suggerimenti generali sul monitoraggio delle risorse di Azure, vedere [Procedure consigliate: monitoraggio e diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
