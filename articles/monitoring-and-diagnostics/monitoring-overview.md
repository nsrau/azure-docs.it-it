---
title: Monitoraggio di applicazioni e risorse di Azure | Microsoft Docs
description: "Panoramica dei servizi e delle funzionalità Microsoft che contribuiscono a una strategia di monitoraggio completa per i servizi e le applicazioni di Azure."
author: robb
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: robb,bwren
ms.openlocfilehash: d8da175a551f7c589c313b2289b2a0209dbd2b56
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Monitoraggio di applicazioni e risorse di Azure

Il monitoraggio è l'azione di raccolta e analisi dei dati per determinare le prestazioni, l'integrità e la disponibilità dell'applicazione aziendale e delle risorse da cui dipende. Una strategia di monitoraggio efficace aiuta a comprendere il funzionamento dettagliato dei componenti dell'applicazione. Contribuisce anche ad aumentare il tempo di attività tramite notifiche proattive degli aspetti critici, in modo da poterli risolvere prima che diventino effettivi problemi.

Azure include più servizi che singolarmente eseguono un'attività o un ruolo specifico nell'area di monitoraggio. Insieme, questi servizi offrono una soluzione completa per la raccolta, l'analisi e l'utilizzo dei dati di telemetria dall'applicazione e dalle risorse di supporto di Azure sottostanti. Possono anche essere usati per monitorare le risorse locali critiche in modo da fornire un ambiente di monitoraggio ibrido. Conoscere gli strumenti e i dati disponibili è il primo passo per sviluppare una strategia di monitoraggio completa per l'applicazione. 

Il diagramma seguente mostra una visualizzazione concettuale dei componenti che interagiscono per garantire il monitoraggio delle risorse di Azure. Le sezioni seguenti descrivono questi componenti e forniscono collegamenti a informazioni tecniche dettagliate.

![Panoramica del monitoraggio](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Monitoraggio di base
Il monitoraggio di base offre monitoraggio essenziale di base delle diverse risorse di Azure. Questi servizi richiedono una configurazione minima e raccolgono i dati di telemetria principali usati dai servizi di monitoraggio Premium.    

### <a name="azure-monitor"></a>Monitoraggio di Azure
[Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) permette il monitoraggio di base per il servizio di Azure consentendo la raccolta di [metriche](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) e [log di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Il log attività, ad esempio, indica quando vengono create nuove risorse o quando vengono modificate risorse esistenti. 

Sono disponibili metriche che forniscono statistiche sulle prestazioni per diverse risorse, nonché per il sistema operativo all'interno di una macchina virtuale. È possibile visualizzare questi dati con una delle utilità di esplorazione nel portale di Azure, inviarli ad Azure Log Analytics per identificare le tendenze e ottenere un'analisi dettagliata o creare regole di avviso per ricevere notifiche proattive sui problemi critici.

### <a name="service-health"></a>Integrità del servizio
L'integrità dell'applicazione si basa sui servizi di Azure da cui dipende. [Integrità dei servizi di Azure](../service-health/service-health-overview.md) identifica eventuali problematiche relative ai servizi di Azure che possono influire negativamente sull'applicazione. Integrità dei servizi permette anche di definire una manutenzione pianificata.

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) monitora costantemente la configurazione delle risorse e i dati di telemetria relativi all'utilizzo. Fornisce quindi consigli personalizzati in base alle procedure consigliate. Seguendo questi consigli, è possibile migliorare le prestazioni, la sicurezza e la disponibilità delle risorse che supportano le applicazioni.


## <a name="premium-monitoring-services"></a>Servizi di monitoraggio Premium
I servizi di Azure seguenti forniscono funzionalità avanzate per la raccolta e l'analisi dei dati di monitoraggio. Questi servizi sono basati sul monitoraggio di base e sfruttano alcune funzionalità comuni di Azure. Offrono potente analisi con i dati raccolti per fornire informazioni esclusive sulle applicazioni e sull'infrastruttura. Presentano i dati nel contesto di determinati scenari destinati a utenti diversi.

### <a name="application-insights"></a>Application Insights
È possibile usare [Azure Application Insights](http://azure.microsoft.com/documentation/services/application-insights) per monitorare disponibilità, prestazioni e utilizzo dell'applicazione, indipendentemente dal fatto che sia ospitata nel cloud o in locale. 

La strumentazione dell'applicazione insieme ad Application Insights permette di ottenere informazioni più approfondite. È quindi possibile identificare e diagnosticare rapidamente gli errori senza attendere che vengano segnalati da un utente. Con le informazioni raccolte, è possibile prendere decisioni informate sulla manutenzione dell'applicazione e sui miglioramenti da apportare. 

Application Insights include strumenti estensivi per l'interazione con i dati raccolti dal servizio stesso. Application Insights archivia questi dati in un repository comune. Il servizio può trarre vantaggio da funzionalità condivise come avvisi, dashboard e analisi approfondite con il linguaggio di query di Log Analytics.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) ha un ruolo centrale nel monitoraggio di Azure attraverso la raccolta di dati da un'ampia gamma di risorse in un unico repository. È quindi possibile analizzare i dati usando un potente linguaggio di query. 

Application Insights e il Centro sicurezza di Azure archiviano i dati negli archivi dati di Log Analytics, di cui usano il motore di analisi. I dati sono quelli raccolti da Monitoraggio di Azure, dalle soluzioni di gestione e dagli agenti installati su macchine virtuali nel cloud o in locale. Questa funzionalità condivisa permette di ottenere il quadro completo dell'ambiente. 


### <a name="service-map"></a>Elenco dei servizi
[Mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md) fornisce informazioni sull'ambiente IaaS analizzando le macchine virtuali con i diversi processi e dipendenze da altri computer e processi esterni. Integra eventi, dati sulle prestazioni e soluzioni di gestione in Log Analytics. È quindi possibile visualizzare questi dati nel contesto di ogni computer e in relazione al resto dell'ambiente. 

Mapping dei servizi è simile a [Mappa delle applicazioni in Application Insights](../application-insights/app-insights-app-map.md). Il servizio è incentrato sui componenti dell'infrastruttura che supportano le applicazioni.

### <a name="network-watcher"></a>Network Watcher
[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) consente il monitoraggio basato sugli scenari e la diagnostica per diversi scenari di rete in Azure. Archivia i dati in metriche e diagnostica di Azure per l'ulteriore analisi. Si integra con le soluzioni seguenti per il monitoraggio di diversi aspetti della rete:
* [Monitoraggio prestazioni rete](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/): soluzione di monitoraggio di rete basata sul cloud che monitora la connettività tra cloud pubblici, data center e ambienti locali.
* [Monitoraggio ExpressRoute](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/): funzionalità di Monitoraggio prestazioni rete che monitora le prestazioni e la connettività end-to-end nei circuiti ExpressRoute di Azure.
* Analisi del traffico: soluzione basata sul cloud che fornisce visibilità delle attività di utenti e applicazioni nella rete cloud.
* [Analisi DNS](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-dns): soluzione che fornisce informazioni dettagliate relative a operazioni, prestazioni e sicurezza, basate sui server DNS.

### <a name="management-solutions"></a>Soluzioni di gestione
Le [soluzioni di gestione](../log-analytics/log-analytics-add-solutions.md) sono set di logica compressi che forniscono informazioni su una determinata applicazione o servizio. Si basano su Log Analytics per archiviare e analizzare i dati di monitoraggio che raccolgono. 

Sono disponibili soluzioni di gestione di Microsoft e di alcuni partner, che permettono il monitoraggio di vari servizi di Azure e di terze parti. Alcuni esempi includono le soluzioni di monitoraggio seguenti:
* [Monitoraggio contenitori](../log-analytics/log-analytics-containers.md): semplifica la visualizzazione e la gestione degli host dei contenitori.
* [Analisi SQL di Azure](../log-analytics/log-analytics-azure-sql.md): raccoglie e visualizza le metriche delle prestazioni per i database SQL di Azure.


## <a name="shared-functionality"></a>Funzionalità condivise
Gli strumenti di Azure seguenti forniscono funzionalità critiche ai servizi di monitoraggio Premium. Poiché sono condivisi da più servizi, permettono di sfruttare funzionalità e configurazioni comuni di più servizi.

### <a name="alerts"></a>Avvisi
Gli [avvisi di Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) inviano notifiche proattive sulle condizioni critiche e possono eseguire azioni correttive. Le regole di avviso possono usare dati di più origini, tra cui metriche e log. Usano i [gruppi di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md), che contengono set univoci di destinatari e azioni in risposta a un avviso. A seconda dei requisiti, è possibile fare in modo che gli avvisi avviino azioni esterne tramite webhook e si integrino con gli strumenti di gestione dei servizi IT.

### <a name="dashboards"></a>Dashboard
È possibile usare i [dashboard di Azure](../azure-portal/azure-portal-dashboards.md) per combinare tipi diversi di dati in un unico riquadro nel portale di Azure. È quindi possibile condividere il dashboard con altri utenti di Azure. 

Ad esempio, è possibile creare un dashboard che combini gli elementi seguenti:
- Riquadri per la visualizzazione di un grafico delle metriche
- Un tabella dei log attività
- Un grafico di utilizzo di Application Insights
- L'output di una ricerca log in Log Analytics

È anche possibile esportare dati di Log Analytics in [Power BI](https://docs.microsoft.com/power-bi/). In questo strumento è possibile sfruttare alcune visualizzazioni aggiuntive. È anche possibile rendere i dati disponibili ad altri all'interno e all'esterno dell'organizzazione.

### <a name="metrics-explorer"></a>Esplora metriche
Le [metriche](../monitoring-and-diagnostics/monitoring-overview-metrics.md) sono valori numerici generati da una risorsa di Azure che consentono di identificare il funzionamento e le prestazioni della risorsa. Tramite Esplora metriche è possibile inviare le metriche a Log Analytics per l'analisi con i dati di altre origini.



### <a name="activity-log"></a>Log attività
I [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) forniscono dati relativi al funzionamento di una risorsa di Azure. Sono incluse le informazioni seguenti:
- Modifiche di configurazione apportate alla risorsa.
- Imprevisti di integrità dei servizi.
- Consigli su come utilizzare al meglio la risorsa.
- Informazioni relative alle operazioni di scalabilità automatica. 

È possibile visualizzare i log per una determinata risorsa nella relativa pagina del portale di Azure. In alternativa, è possibile visualizzare i log di più risorse nell'utilità di esplorazione dei log attività. 

È anche possibile inviare log attività a Log Analytics. Qui è possibile analizzare i log usando i dati raccolti da soluzioni di gestione, agenti su macchine virtuali e altre origini.


## <a name="example-scenarios"></a>Scenari di esempio
Di seguito vengono forniti alcuni esempi generali del possibile uso di strumenti di monitoraggio diversi in Azure a seconda degli scenari.

### <a name="monitoring-a-web-application"></a>Monitoraggio di un'applicazione Web
Si consideri un'applicazione Web distribuita in Azure tramite Servizio app di Azure, Archiviazione di Azure e un database SQL. È possibile iniziare accedendo alle [metriche](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e ai [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) per queste risorse nelle rispettive pagine del portale di Azure. Si cercheranno le informazioni critiche, ad esempio il numero di richieste all'applicazione e il tempo di risposta medio. È anche possibile identificare tutte le modifiche di configurazione.

È quindi possibile passare a Monitoraggio nel portale per visualizzare le metriche e i log per le diverse risorse insieme. Determinando i parametri standard per le metriche, è possibile [creare regole di avviso](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md). Queste regole possono inviare notifiche proattive nei casi in cui, ad esempio, il tempo di risposta medio supera una soglia specifica. Per ottenere una visualizzazione rapida delle prestazioni giornaliere dell'applicazione, è possibile creare un dashboard di Azure per visualizzare i grafici delle metriche che rappresentano gli indicatori KPI critici.

Per eseguire un monitoraggio più approfondito dell'applicazione, [la si configura per Application Insights](../application-insights/quick-monitor-portal.md). È ora possibile raccogliere dati aggiuntivi che forniscono altre informazioni sul funzionamento e sulle prestazioni dell'applicazione. Application Insights rileva le relazioni sottostanti tra i componenti dell'app. Il servizio permette la rappresentazione visiva tramite [Mappa delle applicazioni](../application-insights/app-insights-app-map.md), insieme alla [traccia end-to-end](../application-insights/app-insights-transaction-diagnostics.md) per diagnosticare la dipendenza, l'eccezione o il componente specifico in cui si è verificato un problema. 

Si creano [test di disponibilità](../application-insights/app-insights-monitor-web-app-availability.md) per testare in modo proattivo l'applicazione da aree diverse. Per agevolare gli sviluppatori, si [abilita Profiler](../application-insights/enable-profiler-compute.md) per poter tenere traccia delle richieste e di eventuali eccezioni in una riga di codice specifica. Per ottenere ulteriore visibilità sui servizi usati nell'applicazione, è possibile aggiungere la [soluzione Analisi SQL](../log-analytics/log-analytics-azure-sql.md) per raccogliere dati aggiuntivi in Log Analytics. 

Dopo un certo periodo di tempo, è possibile scegliere di indagare sulla causa principale dei casi in cui le prestazioni nel sito scendono sotto una soglia specifica. È possibile scrivere una query tramite Log Analytics. Questa query aiuta a correlare i dati sull'utilizzo e sulle prestazioni raccolti da Application Insights ai dati di configurazione e sulle prestazioni delle risorse di Azure che supportano l'applicazione.


### <a name="monitoring-virtual-machines"></a>Monitoraggio delle macchine virtuali
In Azure è possibile eseguire una combinazione di macchine virtuali Windows e Linux. Usare Monitoraggio di Azure per visualizzare i [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) e le [metriche a livello di host](../monitoring-and-diagnostics/monitoring-overview-metrics.md). È possibile aggiungere l'[estensione Diagnostica di Azure](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) alle macchine virtuali per raccogliere le metriche dal sistema operativo guest. È quindi possibile creare [regole di avviso](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) per ricevere notifiche proattive dei casi in cui le metriche di base, come quelle sull'utilizzo del processore e sulla memoria, superano le soglie.

Per raccogliere altri dettagli sulle macchine virtuali che eseguono un'applicazione aziendale, si [crea un'area di lavoro di Log Analytics e si abilita l'estensione VM](../log-analytics/log-analytics-quick-collect-azurevm.md) in ogni computer. È possibile configurare la [raccolta di origini dati diverse](../log-analytics/log-analytics-data-sources.md) per l'applicazione e [creare visualizzazioni](../log-analytics/log-analytics-view-designer.md) per ottenere segnalazioni sul funzionamento e sulle prestazioni di ogni giorno. quindi si [creano regole di avviso](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) per ricevere notifiche quando si verificano determinati eventi di errore. 

Per monitorare ininterrottamente l'integrità dell'agente installato, è possibile aggiungere la [soluzione di gestione Integrità agente](../operations-management-suite/oms-solution-agenthealth.md). Per ottenere altre informazioni sull'applicazione, è possibile [aggiungere Dependency Agent](../operations-management-suite/operations-management-suite-service-map-configure.md) alle macchine virtuali per aggiungerle a [Mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md), Mapping dei servizi individua i processi critici e identifica le connessioni tra i computer con altri servizi. 

Dopo la segnalazione di un'interruzione, si usa Mapping dei servizi per eseguire analisi che consentano di identificare i computer specifici in cui si è verificato il problema. È quindi possibile creare una [query sui dati di Log Analytics](../log-analytics/log-analytics-log-search-new.md) per identificare il problema in futuro. È inoltre possibile creare una regola di avviso per ottenere notifiche proattive quando viene rilevata la condizione.



## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su:

* [Monitoraggio di Azure in un video tratto da Ignite 2016](https://myignite.microsoft.com/videos/4977).
* [Introduzione a Monitoraggio di Azure](monitoring-get-started.md).
* [Diagnostica di Azure](../azure-diagnostics.md), utile per la diagnosi dei problemi in un servizio cloud, una macchina virtuale, un set di scalabilità di macchine virtuali o un'applicazione di Azure Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/), utile per la diagnosi di problemi nell'app Web del servizio app.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) per l'analisi dei dati di monitoraggio raccolti.
