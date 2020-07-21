---
title: Log in Monitoraggio di Azure | Microsoft Docs
description: Descrive i log in Monitoraggio di Azure usati per l'analisi avanzata dei dati di monitoraggio.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 4e5d4af74ab54479a49963369cb99dbc19fca848
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505278"
---
# <a name="logs-in-azure-monitor"></a>Log in Monitoraggio di Azure

> [!NOTE]
> Tutti i dati raccolti da Monitoraggio di Azure rientrano in uno di due tipi fondamentali, metriche e log. Questo articolo illustra i log. Per una descrizione dettagliata delle metriche, vedere [Metriche in Monitoraggio di Azure](data-platform-metrics.md). Per un confronto tra i due tipi di dati, vedere [Monitoraggio dei dati raccolti da Monitoraggio di Azure](data-platform.md).

I log in Monitoraggio di Azure sono particolarmente utili per l'esecuzione di analisi complesse sui dati provenienti da una vasta gamma di origini. Questo articolo descrive come sono strutturati i log in Monitoraggio di Azure e cosa è possibile fare con i dati e identifica origini dati diverse che archiviano i dati nei log.

> [!NOTE]
> È importante distinguere tra i log di Monitoraggio di Azure e le origini dei dati di log in Azure. Ad esempio, gli eventi a livello di sottoscrizione in Azure vengono scritti in un [log attività](platform-logs-overview.md) che è possibile visualizzare dal menu Monitoraggio di Azure. La maggior parte delle risorse scrive informazioni operative in un [log delle risorse](platform-logs-overview.md) che è possibile inviare a destinazioni diverse. I log di Monitoraggio di Azure sono piattaforma di dati di log che raccoglie log attività e log delle risorse insieme ad altri dati di monitoraggio, per offrire analisi approfondite nell'intero set di risorse.

## <a name="what-are-azure-monitor-logs"></a>Che cosa sono i log di Monitoraggio di Azure?

I log di Monitoraggio di Azure contengono tipi di dati diversi, organizzati in record con set di proprietà diversi per ogni tipo. Possono contenere valori numerici come le metriche di Monitoraggio di Azure, ma in genere contengono dati di testo con descrizioni dettagliate. Differiscono anche dai dati delle metriche perché hanno una struttura variabile e spesso non vengono raccolti a intervalli regolari. I dati di telemetria, come eventi e tracce, vengono archiviati come log di Monitoraggio di Azure insieme ai dati sulle prestazioni in modo da poter essere combinati per l'analisi.

Un tipo di voce di log comune è un evento, che viene raccolto sporadicamente. Gli eventi vengono creati da un'applicazione o da un servizio e in genere includono informazioni sufficienti per offrire da soli un contesto completo. Un evento indica, ad esempio, la creazione o la modifica di una risorsa, l'avvio di un nuovo host in risposta all'aumento del traffico o il rilevamento di un errore in un'applicazione.

 Poiché il formato dei dati può variare, le applicazioni possono creare log personalizzati usando la struttura necessaria. I dati delle metriche possono anche essere archiviati in log ed essere combinati ad altri dati di monitoraggio, per l'analisi delle tendenze e altre analisi dei dati.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Che cosa è possibile fare con i log di Monitoraggio di Azure?
La tabella seguente elenca le diverse modalità per l'uso dei log in Monitoraggio di Azure.


|  | Descrizione |
|:---|:---|
| **Analisi** | Usare [Log Analytics](../log-query/get-started-portal.md) nel portale di Azure per scrivere [query di log](../log-query/log-query-overview.md) e analizzare i dati di log in modo interattivo usando il potente motore di analisi Esplora dati.<br>Usare la [console di analisi Application Insights](../log-query/log-query-overview.md) nel portale di Azure per scrivere query di log e analizzare in modo interattivo i dati di log da Application Insights. |
| **Visualizzazione** | Aggiungere i risultati delle query visualizzati come tabelle o grafici in un [dashboard di Azure](../../azure-portal/azure-portal-dashboards.md).<br>Creare una [cartella di lavoro](../platform/workbooks-overview.md) per combinare più set di dati in un report interattivo. <br>Esportazione dei risultati di una query in [Power BI](powerbi.md) per usare diverse visualizzazioni e condividerle con utenti esternamente ad Azure.<br>Esportare i risultati di una query in [Grafana](grafana-plugin.md) per sfruttarne le capacità di creazione dashboard e combinare i risultati con altre origini dati.|
| **Avviso** | Configurazione di una [regola di avviso per il log](alerts-log.md) che invia una notifica o esegue un'[azione automatica](action-groups.md) quando i risultati della query corrispondono a un risultato specifico.<br>Configurare una [regola di avviso della metrica](alerts-metric-logs.md) in determinati log di dati estratti come metriche. |
| **Recupero** | Accedere ai risultati delle query di log da una riga di comando usando l'[interfaccia della riga di comando di Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Accedere ai risultati delle query di log da una riga di comando usando i [cmdlet di PowerShell](/powershell/module/az.operationalinsights).<br>Accedere ai risultati delle query di log da un'applicazione personalizzata usando l'[API REST](https://dev.loganalytics.io/). |
| **Export** | Compilare un flusso di lavoro per recuperare i dati di log e copiarli in una posizione esterna usando [App per la logica](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Come sono strutturati i dati nei log di Monitoraggio di Azure?
I dati raccolti dai log di Monitoraggio di Azure vengono archiviati in un'[area di lavoro Log Analytics](../platform/design-logs-deployment.md). Ogni area di lavoro contiene più tabelle, ognuna delle quali archivia i dati provenienti da una determinata origine. Anche se tutte le tabelle condividono [alcune proprietà comuni](log-standard-properties.md), ognuna dispone di un set univoco di proprietà che dipende dal tipo di dati archiviati. Una nuova area di lavoro ha un set di tabelle standard, e altre tabelle vengono aggiunte man mano dalle diverse soluzioni di monitoraggio e da altri servizi che scrivono nell'area di lavoro.

I dati di log di Application Insights usano lo stesso motore di Log Analytics delle aree di lavoro, ma vengono archiviati separatamente per ogni applicazione monitorata. Ogni applicazione dispone di un set standard di tabelle, per contenere dati come richieste di applicazioni, eccezioni e visualizzazioni pagina.

Le query di log usano i dati di un'area di lavoro Log Analytics o di un'applicazione Application Insights. È possibile usare una [query tra risorse](../log-query/cross-workspace-query.md) per analizzare i dati dell'applicazione insieme ad altri dati di log o per creare query che includono più aree di lavoro o applicazioni.

![Aree di lavoro](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Query di log
I dati dei log di Monitoraggio di Azure vengono recuperati tramite una [query di log](../log-query/log-query-overview.md) scritta con il [linguaggio di query Kusto](../log-query/get-started-queries.md), che consente di recuperare, consolidare e analizzare velocemente i dati raccolti. Usare [Log Analytics](../log-query/log-query-overview.md) per scrivere e testare le query di log nel portale di Azure. Questo strumento consente di elaborare i risultati in modo interattivo o di aggiungerli a un dashboard per visualizzarli insieme ad altri dati.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Aprire [Log Analytics da Application Insights](../log-query/log-query-overview.md) per analizzare i dati di Application Insights.

![Analytics in Application Insights](media/data-platform-logs/app-insights-analytics.png)

È anche possibile recuperare i dati di log usando l'[API Log Analytics](https://dev.loganalytics.io/documentation/overview) e l'[API REST di Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Origini dei log di Monitoraggio di Azure
Monitoraggio di Azure può raccogliere dati di logo da un'ampia gamma di origini sia all'interno di Azure sia da risorse locali. Le tabelle seguenti elencano le diverse origini dati disponibili dalle varie risorse che scrivono dati nei log di Monitoraggio di Azure. Ogni voce contiene un collegamento ai dettagli della configurazione richiesta.

### <a name="azure-tenant-and-subscription"></a>Tenant e sottoscrizione di Azure

| Data | Descrizione |
|:---|:---|
| Log di controllo di Azure Active Directory | Configurati tramite impostazioni di diagnostica per ogni directory. Vedere [Integrare i log di Azure AD con i log di Monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Log attività | Archiviati separatamente per impostazione predefinita; possono essere usati per gli avvisi near real-time. Installare la soluzione Analisi log attività per scrivere nell'area di lavoro Log Analytics. Vedere [Raccogliere e analizzare i log attività di Azure in Log Analytics](./activity-log.md). |

### <a name="azure-resources"></a>Risorse di Azure

| Data | Descrizione |
|:---|:---|
| Diagnostica delle risorse | Configurare le impostazioni di diagnostica da scrivere nei dati di diagnostica, incluse le metriche in un'area di lavoro Log Analytics. Vedere [Trasmettere i log delle risorse di Azure a Log Analytics](./resource-logs.md#send-to-log-analytics-workspace). |
| Soluzioni di monitoraggio | Le soluzioni di monitoraggio scrivono i dati raccolti nella loro area di lavoro Log Analytics. Per un elenco di soluzioni, vedere [Informazioni dettagliate sulla raccolta dati per le soluzioni di gestione in Azure](../monitor-reference.md). Per informazioni dettagliate sull'installazione e l'uso di soluzioni, vedere [Soluzioni di monitoraggio in Monitoraggio di Azure](../insights/solutions.md). |
| Metriche | Inviare metriche della piattaforma per le risorse di Monitoraggio di Azure a un'area di lavoro di Log Analytics per mantenere i dati di log per periodi più lunghi e per eseguire analisi complesse con altri tipi di dati usando il [linguaggio di query Kusto](/azure/kusto/query/). Vedere [Trasmettere i log delle risorse di Azure a Log Analytics](./resource-logs.md#send-to-azure-storage). |
| Archiviazione tabelle di Azure | Raccogliere i dati dalle risorse di archiviazione di Azure in cui alcune risorse di Azure scrivono i dati di monitoraggio. Vedere [Usare l'archiviazione BLOB di Azure per IIS e l'archiviazione tabelle di Azure per gli eventi con Log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Macchine virtuali

| Data | Descrizione |
|:---|:---|
|  Origini dati degli agenti | Le origini dati raccolte dagli agenti [Windows](agent-windows.md) e [Linux](../learn/quick-collect-linux-computer.md) includono eventi, dati sulle prestazioni e log personalizzati. Per l'elenco delle origini dati e informazioni dettagliate sulla configurazione, vedere [Origini dati degli agenti in Monitoraggio di Azure](data-sources.md). |
| Soluzioni di monitoraggio | Le soluzioni di monitoraggio scrivono i dati raccolti dagli agenti nella loro area di lavoro Log Analytics. Per un elenco di soluzioni, vedere [Informazioni dettagliate sulla raccolta dati per le soluzioni di gestione in Azure](../monitor-reference.md). Per informazioni dettagliate sull'installazione e l'uso di soluzioni, vedere [Soluzioni di monitoraggio in Monitoraggio di Azure](../insights/solutions.md). |
| System Center Operations Manager | Connettere il gruppo di gestione di Operations Manager a Monitoraggio di Azure per raccogliere dati di eventi e prestazioni dagli agenti locali nei log. Per informazioni dettagliate su questa configurazione, vedere [Connettere Operations Manager a Log Analytics](om-agents.md). |


### <a name="applications"></a>APPLICAZIONI

| Data | Descrizione |
|:---|:---|
| Richieste ed eccezioni | Dati dettagliati relativi alle richieste e alle eccezioni delle applicazioni sono disponibili nelle tabelle _requests_, _pageViews_ ed _exceptions_. Le chiamate a [componenti esterni](../app/asp-net-dependencies.md) sono disponibili nella tabella _dependencies_. |
| Utilizzo e prestazioni | Le prestazioni dell'applicazione sono disponibili nelle tabelle _requests_, _browserTimings_ e _performanceCounters_. I dati per [metriche personalizzate](../app/api-custom-events-metrics.md#trackevent) sono disponibili nella tabella _customMetrics_.|
| Dati di traccia | I risultati della [traccia distribuita](../app/distributed-tracing.md) sono archiviati nella tabella _traces_. |
| Test della disponibilità | I dati di riepilogo dei [test di disponibilità](../app/monitor-web-app-availability.md) vengono archiviati nella tabella _availabilityResults_. I dati dettagliati di questi test si trovano in una risorsa di archiviazione separata, ed è possibile accedervi da Application Insights nel portale di Azure. |

### <a name="insights"></a>Informazioni dettagliate

| Data | Descrizione |
|:---|:---|
| Monitoraggio di Azure per contenitori | Dati sulle prestazioni e sull'inventario raccolti da [Monitoraggio di Azure per i contenitori](../insights/container-insights-overview.md). Per un elenco delle tabelle, vedere [Informazioni dettagliate sulla raccolta di dati dei contenitori](../insights/container-insights-log-search.md#container-records). |
| Monitoraggio di Azure per le macchine virtuali | Dati di mapping e prestazioni raccolti da [Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md). Per informazioni dettagliate sull'esecuzione di query su questi dati, vedere [Come eseguire query sui log da Monitoraggio di Azure per le macchine virtuali](../insights/vminsights-log-search.md). |

### <a name="custom"></a>Personalizzato 

| Data | Descrizione |
|:---|:---|
| API REST | Scrivere dati in un'area di lavoro Log Analytics da qualsiasi client REST. Per informazioni dettagliate, vedere [Inviare dati di log a Monitoraggio di Azure con l'API di raccolta dati HTTP](data-collector-api.md).
| App per la logica | Scrivere qualsiasi dato in un'area di lavoro Log Analytics da un flusso di lavoro di un'app per la logica con l'azione **Agente di raccolta dati di Azure Log Analytics**. |

### <a name="security"></a>Sicurezza

| Data | Descrizione |
|:---|:---|
| Centro sicurezza di Azure | Il [Centro sicurezza di Azure](../../security-center/index.yml) archivia i dati raccolti in un'area di lavoro Log Analytics in cui possono essere analizzati con altri dati di log. Per informazioni dettagliate sulla configurazione dell'area di lavoro, vedere [Raccolta dati nel Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md). |
| Azure Sentinel | [Azure Sentinel](../../sentinel/index.yml) archivia i dati dalle origini dati in un'area di lavoro Log Analytics. Vedere [Connettere le origini dati](../../sentinel/connect-data-sources.md).  |


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [piattaforma dati Monitoraggio di Azure](data-platform.md).
- Informazioni sulle [metriche in Monitoraggio di Azure](data-platform-metrics.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
