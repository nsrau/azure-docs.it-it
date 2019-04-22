---
title: Panoramica di Monitoraggio di Azure | Microsoft Docs
description: Panoramica dei servizi e delle funzionalità Microsoft che contribuiscono a una strategia di monitoraggio completa per i servizi e le applicazioni di Azure.
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: bwren
ms.openlocfilehash: 0485f8e3b377ce94ec23a4a1a94eb7e189b0232b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784960"
---
# <a name="azure-monitor-overview"></a>Panoramica di Monitoraggio di Azure

Monitoraggio di Azure ottimizza la disponibilità e le prestazioni delle applicazioni in uso offrendo una soluzione completa per raccogliere e analizzare la telemetria e intervenire di conseguenza dal cloud e dagli ambienti locali. È utile per ottenere informazioni sulle prestazioni delle applicazioni e identificare in modo proattivo i problemi delle applicazioni e delle risorse da cui dipendono.

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>Panoramica
Il diagramma seguente offre una panoramica di Monitoraggio di Azure. Al centro del diagramma si trovano gli archivi dati per le metriche e i log, i due tipi fondamentali di dati usati da Monitoraggio di Azure. A sinistra si trovano le [origini dei dati di monitoraggio](platform/data-sources.md) che popolano tali [archivi dati](platform/data-platform.md). A destra ci sono le diverse funzioni svolte da Monitoraggio di Azure con i dati raccolti, come analisi, avviso e streaming a sistemi esterni.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

![Panoramica di Monitoraggio di Azure](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>Piattaforma di monitoraggio dei dati
Tutti i dati raccolti da Monitoraggio di Azure rientrano in uno di due tipi fondamentali, [metriche e log](platform/data-platform.md). Le [metriche](platform/data-platform-metrics.md) sono valori numerici che descrivono alcuni aspetti di un sistema in un particolare momento. Sono elementi leggeri in grado di supportare scenari praticamente in tempo reale. I [log](platform/data-platform-logs.md) contengono diversi tipi di dati organizzati in record con diversi set di proprietà per ogni tipo. I dati di telemetria, come eventi e tracce, vengono archiviati come log insieme ai dati sulle prestazioni in modo da poter essere combinati per l'analisi.

Per molte risorse di Azure, i dati raccolti da Monitoraggio di Azure sono visualizzati a destra nella pagina Panoramica nel portale di Azure. Osservando una qualsiasi macchina virtuale, ad esempio, si vedono diversi grafici che mostrano le metriche delle prestazioni. Fare clic su uno dei grafici per aprire i dati in [Esplora metriche](platform/metrics-charts.md) nel portale di Azure, che consente di rappresentare in un grafico i valori di più metriche nel tempo.  È possibile visualizzare i grafici in modo interattivo o aggiungerli a un dashboard per visualizzarli con altre visualizzazioni.

![Metriche](media/overview/metrics.png)

I dati di log raccolti da Monitoraggio di Azure possono essere analizzati con [query](log-query/log-query-overview.md) per recuperare, consolidare e analizzare rapidamente i dati raccolti.  È possibile creare e testare le query che utilizzano [Log Analitica](log-query/portals.md) nel portale di Azure e quindi analizzare direttamente i dati usando questi strumenti o salvare le query per l'uso con [visualizzazioni](visualizations.md) o [avviso le regole](platform/alerts-overview.md).

Monitoraggio di Azure usa una versione del [linguaggio di query Kusto](/azure/kusto/query/), usato da Esplora dati di Azure e adatto a query semplici nei log, ma che include anche funzionalità avanzate come le aggregazioni, i join e le analisi intelligenti. È possibile apprendere rapidamente il linguaggio di query usando le [numerose lezioni](log-query/get-started-queries.md) disponibili.  Indicazioni specifiche sono disponibili per gli utenti che hanno già familiarità con [SQL](log-query/sql-cheatsheet.md) e [Splunk](log-query/splunk-cheatsheet.md).

![Log](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Quali dati vengono raccolti da Monitoraggio di Azure?
Monitoraggio di Azure può raccogliere dati da diverse origini. Il monitoraggio dei dati per le applicazioni avviene mediante livelli che vanno dall'applicazione, al sistema operativo e ai servizi su cui si basa, fino alla piattaforma stessa. Monitoraggio di Azure raccoglie i dati da ciascuno dei livelli seguenti:

- **Dati di monitoraggio dell'applicazione**: dati relativi alle prestazioni e alle funzionalità del codice scritto indipendentemente dalla piattaforma.
- **Dati di monitoraggio del sistema operativo guest:** dati relativi al sistema operativo in cui viene eseguita l'applicazione. L'applicazione può essere eseguita in Azure, un altro cloud o in locale. 
- **Dati di monitoraggio delle risorse di Azure:** dati relativi al funzionamento di una risorsa di Azure.
- **Dati di monitoraggio della sottoscrizione di Azure**: dati relativi al funzionamento e alla gestione di una sottoscrizione di Azure e dati relativi all'integrità e al funzionamento di Azure stesso. 
- **Dati di monitoraggio del tenant di Azure**: dati relativi al funzionamento dei servizi di Azure a livello di tenant, ad esempio Azure Active Directory.

Non appena si crea una sottoscrizione di Azure e si inizia ad aggiungere risorse quali macchine virtuali e app Web, Monitoraggio di Azure avvia la raccolta dei dati.  I [log attività](platform/activity-logs-overview.md) registrano quando vengono create o modificate le risorse. Le [metriche](platform/data-platform.md) indicano le prestazioni della risorsa e quali risorse utilizza. 

Per estendere i dati raccolti all'impiego effettivo delle risorse [abilitare la diagnostica](platform/diagnostic-logs-overview.md) e [aggiungere un agente](platform/agent-windows.md) per calcolare le risorse. In questo modo vengono raccolti i dati di telemetria per il funzionamento interno della risorsa ed è possibile configurare diverse [origini dati](platform/agent-data-sources.md) per raccogliere log e metriche dal sistema operativo guest Windows o Linux. 

[Aggiungere un pacchetto di strumentazione all'applicazione](app/azure-web-apps.md) per abilitare Application Insights per raccogliere informazioni dettagliate sull'applicazione tra cui visualizzazioni pagina, richieste di applicazioni ed eccezioni. Verificare ulteriormente la disponibilità dell'applicazione configurando un [test di disponibilità](app/monitor-web-app-availability.md) per simulare il traffico utente.

### <a name="custom-sources"></a>Origini personalizzate
Monitoraggio di Azure può raccogliere dati di log da qualsiasi client REST usando l'[API dell'agente di raccolta dati](platform/data-collector-api.md). In questo modo è possibile creare scenari di monitoraggio personalizzati ed estendere il monitoraggio alle risorse che non espongono dati di telemetria tramite altre origini.



## <a name="insights"></a>Informazioni dettagliate
I dati di monitoraggio sono utili solo se possono aumentare la visibilità del funzionamento dell'ambiente di elaborazione. Monitoraggio di Azure include diverse funzionalità e vari strumenti che offrono preziose informazioni dettagliate sulle applicazioni e su altre risorse da cui dipendono. Le [soluzioni di monitoraggio](insights/solutions.md) e le funzionalità quali [Application Insights](app/app-insights-overview.md) e [Monitoraggio di Azure per contenitori](insights/container-insights-overview.md) offrono informazioni approfondite su diversi aspetti dell'applicazione e su specifici servizi di Azure. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) monitora disponibilità, prestazioni e utilizzo delle applicazioni Web indipendentemente dal fatto che siano ospitate nel cloud o in locale. Utilizza la piattaforma avanzata di analisi dei dati di Monitoraggio di Azure per fornire informazioni molto dettagliate sulle operazioni dell'applicazione e diagnosticare gli errori senza attendere che un utente li segnali. Application Insights include punti di connessione a una gamma di strumenti di sviluppo e si integra con Visual Studio per supportare i processi DevOps.

![Informazioni sull'app](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori
[Monitoraggio di Azure per contenitori](insights/container-insights-overview.md) è una funzionalità progettata per monitorare le prestazioni dei carichi di lavoro dei contenitori distribuiti nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Offre la visibilità delle prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. Vengono raccolti anche i log dei contenitori.  Dopo aver abilitato il monitoraggio dai cluster di Kubernetes, le metriche e i log vengono raccolti automaticamente tramite una versione dell'agente di Log Analytics per Linux inclusa in un contenitore.

![Integrità dei contenitori](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Monitoraggio di Azure per le macchine virtuali
[Monitoraggio di Azure per le macchine virtuali](insights/vminsights-overview.md) monitora le macchine virtuali di Azure su larga scala analizzando le prestazioni e l'integrità delle macchine virtuali Windows e Linux, inclusi i relativi processi e le dipendenze interconnesse ad altre risorse e processi esterni. La soluzione include il supporto per il monitoraggio delle prestazioni e delle dipendenze dell'applicazione per le macchine virtuali ospitate in locale o in un altro provider di servizi cloud.  


![Informazioni dettagliate macchina virtuale](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Soluzioni di monitoraggio
Le [soluzioni di monitoraggio](insights/solutions.md) in Monitoraggio di Azure sono set di logica compressi che forniscono informazioni su una determinata applicazione o servizio. Includono logica per la raccolta di dati di monitoraggio per l'applicazione o il servizio, [query](log-query/log-query-overview.md) per analizzare i dati, e [visualizzazioni](../log-analytics/log-analytics-view-designer.md) per esaminarli. Sono disponibili soluzioni di monitoraggio [di Microsoft](insights/solutions-inventory.md) e di alcuni partner, che permettono il monitoraggio di vari servizi di Azure e altre applicazioni.

![Soluzioni di monitoraggio](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Risposta alle situazioni critiche
Oltre a consentire l'analisi interattiva dei dati di monitoraggio, una soluzione di monitoraggio efficace deve essere in grado di rispondere in modo proattivo alle condizioni critiche identificate nei dati raccolti. La risposta può consistere nell'invio di un SMS o di un messaggio di posta elettronica a un amministratore responsabile dell'analisi di un problema. In alternativa è possibile avviare un processo automatizzato che tenti di risolvere una condizione di errore.


### <a name="alerts"></a>Avvisi
Gli [avvisi di Monitoraggio di Azure](platform/alerts-overview.md) inviano notifiche proattive sulle condizioni critiche e tentano di eseguire azioni correttive. Le regole di avviso basate sulle metriche forniscono avvisi praticamente in tempo reale basati su valori numerici, mentre le regole basate sui log consentono una logica complessa tra dati provenienti da più origini.

Le regole di avviso in Monitoraggio di Azure utilizzano i [gruppi di azioni](platform/action-groups.md) che contengono set univoci di destinatari e azioni che possono essere condivise tra più regole. In base alle esigenze, i gruppi di azioni possono eseguire azioni come l'uso di webhook per far sì che gli avvisi avviino azioni esterne o si integrino con gli strumenti di gestione dei servizi IT.

![Avvisi](media/overview/alerts.png)

### <a name="autoscale"></a>Autoscale
Il ridimensionamento automatico offre la possibilità di avere la quantità corretta di risorse in esecuzione per gestire il carico dell'applicazione. Consente di creare regole che utilizzano le metriche raccolte da Monitoraggio di Azure per determinare quando aggiungere automaticamente le risorse per gestire gli aumenti del carico e anche risparmiare denaro rimuovendo le risorse inattive. Specificare un numero minimo e massimo di istanze e la logica per determinare quando aumentare o ridurre le risorse.

![Autoscale](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualizzazione dei dati di monitoraggio
Le [visualizzazioni](visualizations.md), ad esempio tabelle e grafici, sono strumenti efficaci per riepilogare i dati di monitoraggio e presentarli a diverse tipologie di destinatari. Monitoraggio di Azure ha funzionalità proprie per visualizzare i dati di monitoraggio e utilizza altri servizi di Azure per la pubblicazione dei dati a diversi destinatari.

### <a name="dashboards"></a>Dashboard
I [dashboard di Azure](../azure-portal/azure-portal-dashboards.md) consentono di combinare tipi diversi di dati, tra cui metriche e log, in un unico riquadro del [portale di Azure](https://portal.azure.com). È possibile condividere il dashboard con altri utenti di Azure. È possibile aggiungere gli elementi di Monitoraggio di Azure a un dashboard di Azure oltre all'output di un grafico di metrica o query di log. È ad esempio possibile creare un dashboard che combina i riquadri che visualizzano un grafico delle metriche, una tabella di log attività, un diagramma utilizzo da Application Insights e l'output di una query di log.

![dashboard](media/overview/dashboard.png)

### <a name="views"></a>Visualizzazioni
Le [visualizzazioni](../log-analytics/log-analytics-view-designer.md) presentano visivamente i dati di log in Monitoraggio di Azure.  Ogni visualizzazione include un riquadro singolo che esegue il drill-down a una combinazione di visualizzazioni quali grafici a barre o a linee, oltre ad elenchi di riepilogo dei dati critici.  Le soluzioni di monitoraggio includono visualizzazioni di riepilogo dei dati per un'applicazione specifica ed è possibile creare visualizzazioni personalizzate per presentare i dati da qualsiasi query di log. Come altri elementi di Monitoraggio di Azure, è possibile aggiungere le viste ai dashboard di Azure.

![Visualizza](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) è un servizio di analisi aziendale che fornisce visualizzazioni interattive per un'ampia gamma di origini dati e un metodo efficace per rendere i dati disponibili ad altri utenti all'interno e all'esterno dell'organizzazione. È possibile configurare per Power BI per [importare automaticamente i dati di log da Monitoraggio di Azure](../log-analytics/log-analytics-powerbi.md) per sfruttare i vantaggi di queste visualizzazioni aggiuntive.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrare ed esportare i dati
Spesso si ha l'esigenza di integrare Monitoraggio di Azure con altri sistemi e compilare soluzioni personalizzate che utilizzano i dati di monitoraggio. Altri servizi di Azure funzionano con Monitoraggio di Azure per fornire questa integrazione.

### <a name="event-hub"></a>Hub eventi
[Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs) è una piattaforma di streaming e un servizio di inserimento di eventi che può trasformare e archiviare i dati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch o archiviazione. Usare gli hub eventi per [flusso di dati di monitoraggio di Azure](platform/stream-monitoring-data-event-hubs.md) collaborare SIEM e gli strumenti di monitoraggio.


### <a name="logic-apps"></a>App per la logica
[App per la logica](https://azure.microsoft.com/services/logic-apps) è un servizio che consente di automatizzare le attività e i processi aziendali tramite flussi di lavoro che si integrano con diversi sistemi e servizi. Sono disponibili attività di lettura e scrittura di log e metriche in Monitoraggio di Azure, che consente di creare flussi di lavoro integrati con una varietà di altri sistemi.


### <a name="api"></a>API
Sono disponibili varie API di lettura e scrittura di metriche e log in e da Monitoraggio di Azure oltre all'accesso agli avvisi generati. È anche possibile configurare e recuperare gli avvisi. Ciò offre possibilità praticamente illimitate per la compilazione di soluzioni personalizzate integrate con Monitoraggio di Azure.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su:

* [Metriche e log](platform/data-platform.md) per i dati raccolti da Monitoraggio di Azure.
* [Origini dati](platform/data-sources.md) per come i diversi componenti dell'applicazione inviano i dati di telemetria.
* [Query di log](log-query/log-query-overview.md) per l'analisi dei dati raccolti.