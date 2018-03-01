---
title: Monitoraggio di applicazioni e risorse di Azure | Microsoft Docs
description: "Panoramica dei diversi servizi e funzionalità che contribuiscono a una strategia di monitoraggio completa per le applicazioni e i servizi di Azure."
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
ms.openlocfilehash: 505e92b5fc63f570bc4d0f8899ae977b93850356
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Monitoraggio di applicazioni e risorse di Azure

Il monitoraggio comporta la raccolta e l'analisi dei dati per determinare le prestazioni, l'integrità e la disponibilità dell'applicazione aziendale e delle risorse da cui dipende. Una strategia efficace di monitoraggio consente di conoscere il funzionamento dettagliato dei diversi componenti dell'applicazione e di aumentare il tempo di attività grazie all'invio di notifiche proattive sulle problematiche critiche, per poterle risolvere prima che diventino problemi.

Azure include più servizi che singolarmente eseguono un ruolo o un'attività specifica nell'area di monitoraggio e insieme offrono una soluzione completa per la raccolta, l'analisi e la modifica dei dati di telemetria dall'applicazione e dalle risorse di supporto di Azure sottostanti.  Possono anche essere usati per monitorare le risorse locali critiche in modo da fornire un ambiente di monitoraggio ibrido.   Conoscere gli strumenti e i dati disponibili è il primo passo per sviluppare una strategia di monitoraggio completa per l'applicazione. 

Il diagramma seguente mostra una visualizzazione concettuale dei diversi componenti che interagiscono per garantire il monitoraggio delle risorse di Azure.  Ognuno è descritto nelle sezioni seguenti con collegamenti a informazioni tecniche dettagliate.

![Panoramica del monitoraggio](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Monitoraggio di base
Si tratta del monitoraggio fondamentale necessario nelle risorse di Azure.  Questi servizi richiedono una configurazione minima e raccolgono i dati di telemetria principali sfruttati dai servizi di monitoraggio Premium.    

### <a name="azure-monitor"></a>Monitoraggio di Azure
[Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) abilita il monitoraggio di base per il servizio Azure consentendo la raccolta di [metriche](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) e [log di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).  Il log attività, ad esempio, indica quando nuove risorse vengono create o modificate.  Sono disponibili metriche che forniscono statistiche sulle prestazioni per risorse diverse e anche sul sistema operativo in una macchina virtuale.  È possibile visualizzare questi dati con una delle utilità di esplorazione nel portale di Azure, inviarli a Log Analytics per conoscere le tendenze e ottenere un'analisi dettagliata o creare regole di avviso per ricevere notifiche proattive sulle problematiche critiche.

### <a name="service-health"></a>Integrità del servizio
L'integrità dell'applicazione si basa sui servizi di Azure da cui dipende.  [Integrità dei servizi di Azure](../service-health/service-health-overview.md) identifica eventuali problematiche relative ai servizi di Azure che potrebbero compromettere l'applicazione e consente anche di pianificare una manutenzione programmata.

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) monitora ininterrottamente la configurazione delle risorse e la telemetria relativa all'utilizzo per offrire consigli personalizzati in base alle procedure consigliate.  Seguendo questi consigli, è possibile migliorare le prestazioni, la sicurezza e la disponibilità delle risorse che supportano le applicazioni.


## <a name="premium-monitoring-services"></a>Servizi di monitoraggio Premium
I servizi di Azure seguenti forniscono funzionalità avanzate per la raccolta e l'analisi dei dati di monitoraggio.  Si fondano sul monitoraggio di base, sfruttano le funzionalità comuni in Azure e forniscono analisi avanzate con i dati raccolti per offrire informazioni esclusive sulle applicazioni e sull'infrastruttura.  Presentano i dati nel contesto di determinati scenari destinati a utenti diversi.

### <a name="application-insights"></a>Application Insights
[Application Insights](http://azure.microsoft.com/documentation/services/application-insights) permette di monitorare disponibilità, prestazioni e utilizzo dell'applicazione indipendentemente dal fatto che sia ospitata nel cloud o in locale.  Instrumentando l'applicazione per l'uso di Application Insights, è possibile ottenere informazioni approfondite che consentono di identificare e diagnosticare rapidamente gli errori senza attendere che un utente li segnali. Con le informazioni raccolte, è possibile prendere decisioni informate sulla manutenzione dell'applicazione e sui miglioramenti da apportare.  Oltre agli strumenti avanzati per l'interazione con i dati raccolti, Application Insights archivia i dati in un repository comune per sfruttare le funzionalità condivise, ad esempio gli avvisi, i dashboard e le analisi approfondite, con il linguaggio di query di Log Analytics.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) riveste un ruolo centrale nel monitoraggio di Azure, raccogliendo dati da svariate risorse in un unico repository in cui possono essere analizzati con un linguaggio di query avanzato.  Application Insights e il Centro sicurezza di Azure archiviano i dati negli archivi dati di Log Analytics, di cui sfruttano il motore di analisi.  Questa combinazione di dati raccolti da Monitoraggio di Azure, soluzioni di gestione e agenti installati su macchine virtuali nel cloud o in locale permette di avere un quadro completo dell'intero ambiente. 


### <a name="service-map"></a>Elenco dei servizi
[Mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md) fornisce informazioni sull'ambiente IaaS analizzando le macchine virtuali con i diversi processi e dipendenze da altri computer e processi esterni.  Integra eventi, dati sulle prestazioni e soluzioni di gestione in Log Analytics in modo che sia possibile visualizzare questi dati nel contesto di ogni computer e la relazione che hanno con il resto dell'ambiente.  Mapping dei servizi è simile alla [mappa delle applicazioni in Application Insights](../application-insights/app-insights-app-map.md), ma è incentrato sui componenti dell'infrastruttura che supportano le applicazioni.

### <a name="network-watcher"></a>Network Watcher
[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) consente il monitoraggio basato sugli scenari e la diagnostica per diversi scenari di rete in Azure.  Archivia i dati nelle metriche e nella diagnostica di Azure per eseguire ulteriori analisi e usa le soluzioni di monitoraggio di rete seguenti per monitorare vari aspetti della rete:
* [Monitoraggio prestazioni rete](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/): una soluzione di monitoraggio di rete basata su cloud che monitora la connettività tra infrastrutture di cloud pubblico, data center e ambienti locali.
* [Monitoraggio ExpressRoute](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/): una funzionalità di Monitoraggio prestazioni rete che consente di monitorare la connettività end-to-end e le prestazioni nei circuiti ExpressRoute.
* Analisi del traffico: una soluzione basata su cloud, che fornisce visibilità sulle attività di utenti e applicazioni nella rete cloud.
* [Analisi DNS](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-dns): fornisce informazioni relative a operazioni, prestazioni e sicurezza, basate sui server DNS.

### <a name="management-solutions"></a>Soluzioni di gestione
Le [soluzioni di gestione](../log-analytics/log-analytics-add-solutions.md) sono set di logica compressi che forniscono informazioni su una determinata applicazione o servizio.  Si basano su Log Analytics per archiviare e analizzare i dati di monitoraggio raccolti.  Sono disponibili soluzioni di gestione di Microsoft e di alcuni partner, che consentono il monitoraggio di vari servizi di Azure e di terze parti. Tra gli esempi di soluzioni di monitoraggio sono incluse [Monitoraggio contenitori](../log-analytics/log-analytics-containers.md), che consente di visualizzare e gestire gli host di contenitori, e [Analisi SQL di Azure](../log-analytics/log-analytics-azure-sql.md), che raccoglie e visualizza le metriche delle prestazioni per i database SQL di Azure.


## <a name="shared-functionality"></a>Funzionalità condivise
Gli strumenti di Azure seguenti forniscono funzionalità critiche ai servizi di monitoraggio Premium.  Sono condivisi da più servizi e in questo modo consentono di sfruttare funzionalità e configurazioni comuni in più servizi.

### <a name="alerts"></a>Avvisi
Gli [avvisi di Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) notificano in modo proattivo le condizioni critiche e potenzialmente eseguono azioni correttive.  Le regole di avviso possono sfruttare i dati di più origini, inclusi log e metriche. Usano i [gruppi di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md) che contengono set univoci di destinatari e azioni in risposta a un avviso.  A seconda dei requisiti, è possibile fare in modo che gli avvisi avviino azioni esterne usando i webhook e vengano integrati con gli strumenti di gestione dei servizi IT.

### <a name="dashboards"></a>Dashboard
I [dashboard di Azure](../azure-portal/azure-portal-dashboards.md) consentono di combinare tipi diversi di dati in un unico riquadro del portale di Azure e di condividerli con altri utenti di Azure.  È ad esempio possibile creare un dashboard che combina i riquadri che visualizzano un grafico delle metriche, una tabella di log attività, un diagramma utilizzo da Application Insights e l'output di una ricerca log in Log Analytics.

È anche possibile esportare i dati di Log Analytics in [Power BI](https://docs.microsoft.com/power-bi/) per sfruttare le visualizzazioni aggiuntive e per rendere i dati disponibili per altri utenti interni ed esterni all'organizzazione.

### <a name="metrics-explorer"></a>Esplora metriche
Le [metriche](../monitoring-and-diagnostics/monitoring-overview-metrics.md) sono valori numerici generati dalle risorse di Azure che consentono di conoscere il funzionamento e le prestazioni della risorsa. È possibile inviare le metriche a Log Analytics per un'analisi con i dati di altre origini.



### <a name="activity-logs"></a>Log attività
I [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) contengono dati relativi al funzionamento delle risorse di Azure.  Sono incluse informazioni come le modifiche alla configurazione della risorsa, eventi imprevisti di integrità del servizio, raccomandazioni per un utilizzo migliore della risorsa e informazioni relative alle operazioni di scalabilità automatica.  È possibile visualizzare i log per una particolare risorsa nella relativa pagina del portale di Azure o visualizzare i log di più risorse nell'utilità di esplorazione dei log attività.  È anche possibile inviare i log attività a Log Analytics, in modo che possano essere analizzati con i dati raccolti dalle soluzioni di gestione, dagli agenti nelle macchine virtuali e da altre origini.


## <a name="example-scenarios"></a>Scenari di esempio
I seguenti sono esempi generali che illustrano come sfruttare i diversi strumenti di monitoraggio in Azure a seconda degli scenari.

### <a name="monitoring-a-web-application"></a>Monitoraggio di un'applicazione Web
Si consideri un'applicazione Web distribuita in Azure con Servizi app, Archiviazione di Azure e un database SQL.  È possibile iniziare con l'accesso alle [metriche](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e ai [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) per ognuna di queste singole risorse nelle rispettive pagine del portale di Azure.  Saranno incluse informazioni critiche, ad esempio il numero di richieste all'applicazione e il tempo di risposta medio, oltre all'identificazione di eventuali modifiche della configurazione.

È quindi possibile passare a Monitoraggio nel portale per visualizzare le metriche e i log per le diverse risorse tutte insieme.  Stabilendo parametri standard per le metriche, si [creano regole di avviso](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) per ricevere notifiche in modo proattivo quando, ad esempio, il tempo di risposta medio supera una soglia.  Per ottenere una visualizzazione rapida delle prestazioni giornaliere dell'applicazione, si crea un dashboard di Azure per visualizzare i grafici delle metriche che rappresentano gli indicatori KPI critici.

Per eseguire un monitoraggio più approfondito dell'applicazione, [la si configura per Application Insights](../application-insights/quick-monitor-portal.md).  È ora possibile raccogliere dati aggiuntivi che forniscono altre informazioni sul funzionamento e sulle prestazioni dell'applicazione.  Application Insights rileva le relazioni sottostanti tra i componenti dell'app che consentono una rappresentazione visiva tramite la [mappa delle applicazioni](../application-insights/app-insights-app-map.md) associata all'[analisi end-to-end](../application-insights/app-insights-transaction-diagnostics.md) per diagnosticare il componente, la dipendenza o l'eccezione esatta in cui si è verificato un problema.  Si creano [test di disponibilità](../application-insights/app-insights-monitor-web-app-availability.md) per testare in modo proattivo l'applicazione da aree diverse.  Per agevolare gli sviluppatori, si [abilita Profiler](../application-insights/enable-profiler-compute.md) per poter tenere traccia delle richieste e di eventuali eccezioni in una riga di codice specifica.  

Per ottenere maggiore visibilità sui servizi usati nell'applicazione, si aggiunge la [soluzione Analisi SQL](../log-analytics/log-analytics-azure-sql.md) per raccogliere dati aggiuntivi in Log Analytics. Si decide in seguito di trovare la causa radice per cui in alcuni periodi le prestazioni del sito sono scese sotto la soglia.  Si scrive una query usando Log Analytics per correlare i dati di utilizzo e delle prestazioni raccolti da Application Insights con i dati di configurazione e delle prestazioni delle risorse di Azure che supportano l'applicazione.


### <a name="monitoring-virtual-machines"></a>Monitoraggio delle macchine virtuali
In Azure è in esecuzione una combinazione di macchine virtuali Linux e Windows.  Si usa Monitoraggio di Azure per visualizzare i [log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) e le [metriche a livello di host](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e quindi si aggiunge l'[estensione Diagnostica di Azure](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) alle macchine virtuali per raccogliere le metriche dal sistema operativo guest.  Si creano quindi [regole di avviso](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) per ricevere notifiche in modo proattivo quando le metriche di base, ad esempio l'utilizzo del processore e la memoria, superano le soglie.

Per raccogliere altri dettagli sulle macchine virtuali che eseguono un'applicazione aziendale, si [crea un'area di lavoro di Log Analytics e si abilita l'estensione VM](../log-analytics/log-analytics-quick-collect-azurevm.md) in ogni computer.  Si configura la [raccolta di origini dati diverse](../log-analytics/log-analytics-data-sources.md) per l'applicazione e si [creano visualizzazioni](../log-analytics/log-analytics-view-designer.md) per creare report sul funzionamento e sulle prestazioni giornalieri,  quindi si [creano regole di avviso](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) per ricevere notifiche quando si verificano determinati eventi di errore.  Per monitorare ininterrottamente l'integrità dell'agente installato, si aggiunge la [soluzione di gestione Integrità agente](../operations-management-suite/oms-solution-agenthealth.md).

Per ottenere altre informazioni sull'applicazione, si [aggiunge Dependency Agent](../operations-management-suite/operations-management-suite-service-map-configure.md) alle macchine virtuali per aggiungerle a [Mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md),  che individua i processi critici e identifica le connessioni tra i computer con altri servizi.  Dopo la segnalazione di un'interruzione, si usa Mapping dei servizi per eseguire analisi che consentano di identificare i computer specifici in cui si è verificato il problema.  Si crea quindi una [query sui dati di Log Analytics](../log-analytics/log-analytics-log-search-new.md) per identificare il problema in futuro e si crea una regola di avviso per ricevere notifiche in modo proattivo quando la condizione viene rilevata.



## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su:

* [Monitoraggio di Azure in un video tratto dall'evento Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introduzione a Monitoraggio di Azure](monitoring-get-started.md)
* [Diagnostica di Azure](../azure-diagnostics.md), utile per la diagnosi dei problemi in un servizio cloud, una macchina virtuale, un set di scalabilità di macchine virtuali o un'applicazione di Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) , utili per la diagnosi di problemi nell'app Web del servizio app.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) per l'analisi dei dati di monitoraggio raccolti.
