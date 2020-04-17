---
title: 'Log in Monitor di Azure : Documenti Microsoft'
description: Descrive i log in Monitoraggio di Azure usati per l'analisi avanzata dei dati di monitoraggio.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 0b288bf6d987b9db682c8d1439879cf6b499f213
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457332"
---
# <a name="logs-in-azure-monitor"></a>Log in Monitoraggio di Azure

> [!NOTE]
> Tutti i dati raccolti da Monitoraggio di Azure rientrano in uno dei due tipi fondamentali, Metriche e Log.All data collected by Azure Monitor fits into one of two fundamental types, Metrics and Logs. In questo articolo vengono descritti i registri. Fare riferimento a [Metriche in Monitoraggio di Azure](data-platform-metrics.md) per una descrizione dettagliata delle metriche e sui dati di monitoraggio raccolti da Monitoraggio di [Azure](data-platform.md) per un confronto tra le due.

I log in Monitoraggio di Azure sono particolarmente utili per eseguire analisi complesse tra dati provenienti da diverse origini. Questo articolo descrive come sono strutturati i log in Monitoraggio di Azure, cosa è possibile fare con i dati e identifica origini dati diverse che archiviano i dati in Logs.This article describes how Logs are structured in Azure Monitor, what you can do with the data, and identifies different data sources that store data in Logs.

> [!NOTE]
> È importante distinguere tra i log di monitoraggio di Azure e le origini dei dati di log in Azure.It's important to distinguish between Azure Monitor Logs and sources of log data in Azure. Ad esempio, gli eventi a livello di sottoscrizione in Azure vengono scritti in un log attività che è possibile visualizzare dal menu Monitoraggio di Azure.For example, subscription level events in Azure are written to an [activity log](platform-logs-overview.md) that you can view from the Azure Monitor menu. La maggior parte delle risorse scriverà le informazioni operative in un [log risorse](platform-logs-overview.md) che è possibile inoltrare a posizioni diverse. Registri di monitoraggio di Azure è una piattaforma di log che raccoglie i log attività e i log delle risorse insieme ad altri dati di monitoraggio per fornire un'analisi approfondita dell'intero set di risorse.

## <a name="what-are-azure-monitor-logs"></a>Che cosa sono i log di monitoraggio di Azure?

I log in Monitoraggio di Azure contengono diversi tipi di dati organizzati in record con set di proprietà diversi per ogni tipo. I log possono contenere valori numerici come Le metriche di Monitoraggio di Azure, ma in genere contengono dati di testo con descrizioni dettagliate. Si differenziano ulteriormente dai dati delle metriche in quanto variano nella loro struttura e spesso non vengono raccolti a intervalli regolari. I dati di telemetria, ad esempio gli eventi e le tracce, vengono archiviati i log di Monitoraggio di Azure oltre ai dati sulle prestazioni in modo che possano essere tutti combinati per l'analisi.

Un tipo comune di voce di log è un evento, che viene raccolto sporadicamente. Gli eventi vengono creati da un'applicazione o un servizio e in genere includono informazioni sufficienti per fornire un contesto completo. Un evento indica, ad esempio, la creazione o la modifica di una risorsa, l'avvio di un nuovo host in risposta all'aumento del traffico o il rilevamento di un errore in un'applicazione.

 Poiché il formato dei dati può variare, le applicazioni possono creare log personalizzati usando la struttura necessaria. I dati delle metriche possono anche essere archiviati nei log per combinarli con altri dati di monitoraggio per le tendenze e altre analisi dei dati.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Cosa è possibile fare con i log di Monitoraggio di Azure?
The following table lists the different ways that you can use Logs in Azure Monitor.


|  |  |
|:---|:---|
| Analisi | Usare [Log Analytics](../log-query/get-started-portal.md) nel portale di Azure per scrivere query di [log](../log-query/log-query-overview.md) e analizzare in modo interattivo i dati di log usando il potente motore di analisi di Esplora dati.<br>Usare la console di analisi di Application Insights nel portale di Azure per scrivere query di log e analizzare in modo interattivo i dati di log da Application Insights.Use the [Application Insights analytics console](../app/analytics.md) in the Azure portal to write log queries and interactively analyze log data from Application Insights. |
| Visualizzazione | Aggiungere i risultati delle query di cui viene eseguito il rendering come tabelle o grafici in un dashboard di [Azure.](../../azure-portal/azure-portal-dashboards.md)<br>Creare una [cartella di lavoro](../app/usage-workbooks.md) da combinare con più set di dati in un report interattivo. <br>Esportazione dei risultati di una query in [Power BI](powerbi.md) per usare diverse visualizzazioni e condividerle con utenti esternamente ad Azure.<br>Esportare i risultati di una query in [Grafana](grafana-plugin.md) per sfruttare il dashboard e combinarle con altre origini dati.|
| Avviso | Configurazione di una [regola di avviso per il log](alerts-log.md) che invia una notifica o esegue un'[azione automatica](action-groups.md) quando i risultati della query corrispondono a un risultato specifico.<br>Configurare una regola di [avviso delle metriche](alerts-metric-logs.md) in determinati log dei dati di log estratti come metriche. |
| Recupero | Accedere ai risultati delle query di log da una riga di comando usando [l'interfaccia della riga di comando](/cli/azure/ext/log-analytics/monitor/log-analytics)di Azure.<br>Accedere ai risultati delle query del log da una riga di comando utilizzando i cmdlet di [PowerShell.](https://docs.microsoft.com/powershell/module/az.operationalinsights)<br>Accedere ai risultati delle query del log da un'applicazione personalizzata usando [l'API REST.](https://dev.loganalytics.io/) |
| Esportazione | Creare un flusso di lavoro per recuperare i dati di log e copiarli in una posizione esterna utilizzando [App per](~/articles/logic-apps/index.yml)la logica . |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Come vengono strutturati i dati nei log di monitoraggio di Azure?
I dati raccolti dai log di monitoraggio di Azure vengono archiviati in un'area di lavoro di [Log Analytics.](../platform/design-logs-deployment.md) Ogni area di lavoro contiene più tabelle, ognuna delle quali archivia i dati da una determinata origine. Sebbene tutte le tabelle condividano [alcune proprietà comuni,](log-standard-properties.md)ognuna dispone di un set univoco di proprietà a seconda del tipo di dati archiviati. Una nuova area di lavoro diverrà un set standard di tabelle e altre tabelle verranno aggiunte da diverse soluzioni di monitoraggio e altri servizi che scrivono nell'area di lavoro.

I dati di log di Application Insights usano lo stesso motore di Log Analytics delle aree di lavoro, ma vengono archiviati separatamente per ogni applicazione monitorata. Ogni applicazione dispone di un set standard di tabelle per contenere dati quali richieste di applicazioni, eccezioni e visualizzazioni di pagina.

Le query di log utilizzeranno i dati di un'area di lavoro di Log Analytics o di un'applicazione Application Insights.Log queries will either use data from a Log Analytics workspace or an Application Insights application. È possibile utilizzare una [query tra risorse](../log-query/cross-workspace-query.md) per analizzare i dati dell'applicazione insieme ad altri dati di log o per creare query, incluse più aree di lavoro o applicazioni.

![Aree di lavoro](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Query di log
I dati nei log di Monitoraggio di Azure vengono recuperati tramite una [query di log](../log-query/log-query-overview.md) scritta con il linguaggio di query [Kusto,](../log-query/get-started-queries.md)che consente di recuperare, consolidare e analizzare rapidamente i dati raccolti. Usare Log Analytics per scrivere e testare le query di log nel portale di Azure.Use [Log Analytics](../log-query/portals.md) to write and test log queries in the Azure portal. Consente di lavorare con i risultati in modo interattivo o di aggiungerli a una dashboard per visualizzarli con altre visualizzazioni.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Aprire Log Analytics da Application Insights per analizzare i dati di Application Insights.Open [Log Analytics from Application Insights](../app/analytics.md) to analyze Application Insights data.

![Analytics in Application Insights](media/data-platform-logs/app-insights-analytics.png)

È anche possibile recuperare i dati del log usando [l'API Di Slog Analytics](https://dev.loganalytics.io/documentation/overview) e l'API REST di Application [Insights.](https://dev.applicationinsights.io/documentation/overview)


## <a name="sources-of-azure-monitor-logs"></a>Origini dei log di Monitoraggio di AzureSources of Azure Monitor Logs
Monitoraggio di Azure può raccogliere dati di logo da un'ampia gamma di origini sia all'interno di Azure sia da risorse locali. The following tables list the different data sources available from different resources that write data to Azure Monitor Logs. Ognuno ha un link ai dettagli su qualsiasi configurazione richiesta.

### <a name="azure-tenant-and-subscription"></a>Tenant e sottoscrizione di AzureAzure tenant and subscription

| Data | Descrizione |
|:---|:---|
| Log di controllo di Azure Active DirectoryAzure Active Directory audit logs | Configurato tramite le impostazioni di diagnostica per ogni directory. Vedere Integrare i log di Azure AD con i log di [Monitoraggio di Azure.](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  |
| Log attività | Memorizzato separatamente per impostazione predefinita e può essere utilizzato per avvisi quasi in tempo reale. Installare la soluzione Di analisi del log attività per scrivere nell'area di lavoro di Log Analytics.Install Activity log Analytics solution to write to Log Analytics workspace. Vedere [Raccogliere e analizzare i log delle attività](activity-log-collect.md)di Azure in Log Analytics. |

### <a name="azure-resources"></a>Risorse di Azure

| Data | Descrizione |
|:---|:---|
| Diagnostica delle risorse | Configurare le impostazioni di diagnostica per scrivere nei dati di diagnostica, incluse le metriche in un'area di lavoro di Log Analytics.Configure Diagnostic settings to write to diagnostic data, including metrics to a Log Analytics workspace. Vedere Trasmettere i log delle risorse di [Azure a Log Analytics.](resource-logs-collect-workspace.md) |
| Soluzioni di monitoraggio | Le soluzioni di monitoraggio scrivono i dati raccolti nell'area di lavoro di Log Analytics.Monitoring solutions write data they collect to their Log Analytics workspace. Per un elenco delle soluzioni, vedere Dettagli della raccolta dei [dati per le soluzioni](../insights/solutions-inventory.md) di gestione in Azure.See Data collection details for management solutions in Azure for a list of solutions. Per informazioni dettagliate sull'installazione e l'uso delle soluzioni, vedere [Monitoraggio delle soluzioni in Monitoraggio di Azure.See Monitoring solutions in Azure Monitor](../insights/solutions.md) for details on installing and using solutions. |
| Metriche | Inviare metriche della piattaforma per le risorse di Monitoraggio di Azure in un'area di lavoro di Log Analytics per conservare i dati di log per periodi più lunghi ed eseguire analisi complesse con altri tipi di dati usando il linguaggio di [query Kusto](/azure/kusto/query/). Vedere Trasmettere i log delle risorse di [Azure a Log Analytics.](resource-logs-collect-storage.md) |
| Archiviazione tabelle di Azure | Raccogliere dati dall'archiviazione di Azure in cui alcune risorse di Azure scrivono i dati di monitoraggio. Vedere [Usare l'archiviazione BLOB](diagnostics-extension-logs.md)di Azure per IIS e archiviazione tabelle di Azure per gli eventi con Log Analytics. |

### <a name="virtual-machines"></a>Macchine virtuali

| Data | Descrizione |
|:---|:---|
|  Origini dati degli agenti | Le origini dati raccolte dagli agenti [Windows](agent-windows.md) e [Linux](../learn/quick-collect-linux-computer.md) includono eventi, dati sulle prestazioni e log personalizzati. Per un elenco di origini dati e dettagli sulla configurazione, [vedere Origini dati agente in Monitoraggio di Azure.See Agent data sources in Azure Monitor](data-sources.md) for a list of data sources and details on configuration. |
| Soluzioni di monitoraggio | Le soluzioni di monitoraggio scrivono i dati raccolti dagli agenti nell'area di lavoro di Log Analytics.Monitoring solutions write data they collect from agents to their Log Analytics workspace. Per un elenco delle soluzioni, vedere Dettagli della raccolta dei [dati per le soluzioni](../insights/solutions-inventory.md) di gestione in Azure.See Data collection details for management solutions in Azure for a list of solutions. Per informazioni dettagliate sull'installazione e l'uso delle soluzioni, vedere [Monitoraggio delle soluzioni in Monitoraggio di Azure.See Monitoring solutions in Azure Monitor](../insights/solutions.md) for details on installing and using solutions. |
| System Center Operations Manager | Connettere il gruppo di gestione di Operations Manager a Monitoraggio di Azure per raccogliere i dati relativi a eventi e prestazioni dagli agenti locali nei log. Per informazioni dettagliate su questa configurazione, vedere Connect Operations Manager to Log Analytics .See [Connect Operations Manager to Log Analytics](om-agents.md) per informazioni dettagliate su questa configurazione. |


### <a name="applications"></a>APPLICAZIONI

| Data | Descrizione |
|:---|:---|
| Richieste ed eccezioni | I dati dettagliati sulle richieste e le eccezioni dell'applicazione si trovano nelle tabelle _requests,_ _pageViews_ed _exceptions._ Le chiamate a [componenti esterni](../app/asp-net-dependencies.md) sono presenti nella tabella _delle dipendenze._ |
| Utilizzo e prestazioni | Le prestazioni per l'applicazione sono disponibili nelle tabelle _requests_, _browserTimings_ e _PerformanceCounters._ I dati per [le metriche personalizzate](../app/api-custom-events-metrics.md#trackevent) sono riportati nella tabella _customMetrics.Data_ for custom metrics is in the customMetrics table.|
| Traccia redati | I risultati [dell'analisi distribuita](../app/distributed-tracing.md) vengono archiviati nella tabella _delle tracce._ |
| Test della disponibilità | I dati di riepilogo dei test di disponibilità vengono archiviati nella tabella _availabilityResults.Summary_ data from availability [tests](../app/monitor-web-app-availability.md) is stored in the availabilityResults table. I dati dettagliati di questi test sono in un archivio separato e vi si accede da Application Insights nel portale di Azure.Detailed data from these tests are in separate storage and accessed from Application Insights in the Azure portal. |

### <a name="insights"></a>Informazioni dettagliate

| Data | Descrizione |
|:---|:---|
| Monitoraggio di Azure per contenitori | Dati di inventario e prestazioni raccolti da [Monitoraggio di Azure per i contenitori.](../insights/container-insights-overview.md) Per un elenco delle tabelle, vedere Dettagli raccolta [dati contenitore.](../insights/container-insights-log-search.md#container-records) |
| Monitoraggio di Azure per le macchine virtuali | Eseguire il mapping e le prestazioni dei dati raccolti da [Monitoraggio di Azure per le macchine virtuali.](../insights/vminsights-overview.md) Per informazioni dettagliate sull'esecuzione di query su questi dati, vedere [Come eseguire query sui log da Monitoraggio di Azure per le macchine virtuali.](../insights/vminsights-log-search.md) |

### <a name="custom"></a>Personalizzato 

| Data | Descrizione |
|:---|:---|
| API REST | Scrivere i dati in un'area di lavoro di Log Analytics da qualsiasi client REST. Per informazioni [dettagliate, vedere Inviare dati di log ad Azure Monitor con l'API agente](data-collector-api.md) di raccolta dati HTTP.
| App per la logica | Scrivere i dati in un'area di lavoro di Log Analytics da un flusso di lavoro dell'app per la logica con l'azione Raccolta dati di Analisi log di Azure.Write any data to a Log Analytics workspace from a Logic App workflow with the **Azure Log Analytics Data Collector** action. |

### <a name="security"></a>Sicurezza

| Data | Descrizione |
|:---|:---|
| Centro sicurezza di Azure | [Il Centro sicurezza](/azure/security-center/) di Azure archivia i dati raccolti in un'area di lavoro di Log Analytics in cui possono essere analizzati con altri dati di log. Per informazioni dettagliate sulla configurazione dell'area di lavoro, vedere [Raccolta dei dati nel Centro sicurezza di Azure.See Data collection in Azure Security Center](../../security-center/security-center-enable-data-collection.md) for details on workspace configuration. |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) archivia i dati dalle origini dati in un'area di lavoro di Log Analytics.Azure Sentinel stores data from data sources into a Log Analytics workspace. Consultate [Connettere origini dati.](/azure/sentinel/connect-data-sources)  |


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla piattaforma dati di Monitoraggio di Azure .Learn more about the [Azure Monitor data platform](data-platform.md).
- Informazioni sulle [metriche in Monitoraggio di Azure](data-platform-metrics.md).Learn .
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
