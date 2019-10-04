---
title: Log in monitoraggio di Azure | Microsoft Docs
description: Descrive i log in monitoraggio di Azure che vengono usati per l'analisi avanzata dei dati di monitoraggio.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 9aed19c88517868c2e8cb860dc01d01b7a7c3127
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262072"
---
# <a name="logs-in-azure-monitor"></a>Log in Monitoraggio di Azure

> [!NOTE]
> Tutti i dati raccolti da monitoraggio di Azure si integrano in uno dei due tipi fondamentali, metriche e log. Questo articolo descrive i log. Per una descrizione dettagliata delle metriche e per il [monitoraggio dei dati raccolti da monitoraggio di Azure](data-platform.md) per un confronto tra i due, vedere [metriche in monitoraggio di Azure](data-platform-metrics.md) .

I log in monitoraggio di Azure sono particolarmente utili per l'esecuzione di analisi complesse tra i dati provenienti da un'ampia gamma di origini. Questo articolo descrive come sono strutturati i log in monitoraggio di Azure, cosa è possibile fare con i dati e identifica origini dati diverse che archiviano i dati nei log.

> [!NOTE]
> È importante distinguere tra i log di monitoraggio di Azure e le origini dei dati di log in Azure. Gli eventi a livello di sottoscrizione in Azure, ad esempio, vengono scritti in un [log attività](activity-logs-overview.md) che è possibile visualizzare dal menu monitoraggio di Azure. La maggior parte delle risorse scriverà le informazioni operative in un [log di diagnostica](resource-logs-overview.md) che è possibile trasmettere in posizioni diverse. Log di monitoraggio di Azure è una piattaforma di dati di log che raccoglie log attività e log di diagnostica insieme ad altri dati di monitoraggio per offrire un'analisi approfondita nell'intero set di risorse.

## <a name="what-are-azure-monitor-logs"></a>Che cosa sono i log di monitoraggio di Azure?

I log in monitoraggio di Azure contengono tipi diversi di dati organizzati in record con diversi set di proprietà per ogni tipo. I log possono contenere valori numerici come le metriche di monitoraggio di Azure, ma in genere contengono dati di testo con descrizioni dettagliate. Si differenziano ulteriormente dai dati delle metriche in quanto variano in base alla struttura e spesso non vengono raccolti a intervalli regolari. I dati di telemetria, ad esempio eventi e tracce, vengono archiviati nei log di monitoraggio di Azure oltre ai dati sulle prestazioni, in modo che possano essere combinati per l'analisi.

Un tipo comune di voce di log è un evento, che viene raccolto sporadicamente. Gli eventi vengono creati da un'applicazione o da un servizio e in genere includono informazioni sufficienti per fornire un contesto completo autonomamente. Un evento indica, ad esempio, la creazione o la modifica di una risorsa, l'avvio di un nuovo host in risposta all'aumento del traffico o il rilevamento di un errore in un'applicazione.

 Poiché il formato dei dati può variare, le applicazioni possono creare log personalizzati usando la struttura necessaria. I dati delle metriche possono anche essere archiviati nei log per combinarli con altri dati di monitoraggio per le tendenze e altre analisi dei dati.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Che cosa è possibile fare con i log di monitoraggio di Azure?
La tabella seguente elenca i diversi modi in cui è possibile usare i log in monitoraggio di Azure.


|  |  |
|:---|:---|
| Analyze | Usare [log Analytics](../log-query/get-started-portal.md) nel portale di Azure per scrivere [query di log](../log-query/log-query-overview.md) e analizzare i dati di log in modo interattivo usando il motore di analisi Esplora dati potente.<br>Usare la [console Application Insights Analytics](../app/analytics.md) nel portale di Azure per scrivere query di log e analizzare i dati di log in modo interattivo da Application Insights. |
| Visualizza | Aggiungere i risultati della query con rendering come tabelle o grafici in un [dashboard di Azure](../../azure-portal/azure-portal-dashboards.md).<br>Creare una [cartella di lavoro](../app/usage-workbooks.md) per combinare più set di dati in un report interattivo. <br>Esportazione dei risultati di una query in [Power BI](powerbi.md) per usare diverse visualizzazioni e condividerle con utenti esternamente ad Azure.<br>Esportare i risultati di una query in [Grafana](grafana-plugin.md) per sfruttare il dashboard e combinarli con altre origini dati.|
| Avviso | Configurazione di una [regola di avviso per il log](alerts-log.md) che invia una notifica o esegue un'[azione automatica](action-groups.md) quando i risultati della query corrispondono a un risultato specifico.<br>Configurare una [regola di avviso](alerts-metric-logs.md) per la metrica per determinati log di dati di log estratti come metriche. |
| Recupera | Accedere ai risultati delle query di log da una riga di comando usando l' [interfaccia](/cli/azure/ext/log-analytics/monitor/log-analytics)della riga di comando<br>Accedere ai risultati delle query di log da una riga di comando usando i [cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Accedere ai risultati delle query di log da un'applicazione personalizzata usando l' [API REST](https://dev.loganalytics.io/). |
| Esporta | Compilare un flusso di lavoro per recuperare i dati di log e copiarli in una posizione esterna usando app per la [logica](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Come sono strutturati i dati nei log di monitoraggio di Azure?
I dati raccolti dai log di monitoraggio di Azure vengono archiviati in un' [area di lavoro log Analytics](../platform/design-logs-deployment.md). Ogni area di lavoro contiene più tabelle, ognuna delle quali archivia i dati di una determinata origine. Anche se tutte le tabelle condividono [alcune proprietà comuni](log-standard-properties.md), ciascuna di esse dispone di un set univoco di proprietà a seconda del tipo di dati archiviati. Una nuova area di lavoro avrà un set di tabelle standard e più tabelle verranno aggiunte da diverse soluzioni di monitoraggio e da altri servizi che scrivono nell'area di lavoro.

I dati di log da Application Insights utilizzano lo stesso motore di Log Analytics delle aree di lavoro, ma vengono archiviati separatamente per ogni applicazione monitorata. Ogni applicazione dispone di un set standard di tabelle che contengono dati quali richieste di applicazioni, eccezioni e visualizzazioni pagina.

Le query di log utilizzeranno i dati di un'area di lavoro Log Analytics o un'applicazione Application Insights. È possibile usare una [query tra risorse](../log-query/cross-workspace-query.md) per analizzare i dati dell'applicazione insieme ad altri dati di log o per creare query che includono più aree di lavoro o applicazioni.

![Aree di lavoro](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Query di log
I dati nei log di monitoraggio di Azure vengono recuperati usando una [query di log](../log-query/log-query-overview.md) scritta con il [linguaggio di query kusto](../log-query/get-started-queries.md), che consente di recuperare, consolidare e analizzare rapidamente i dati raccolti. Usare [log Analytics](../log-query/portals.md) per scrivere e testare le query di Log nel portale di Azure. Consente di usare i risultati in modo interattivo o di aggiungerli a un dashboard per visualizzarli con altre visualizzazioni.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Aprire [log Analytics da Application Insights](../app/analytics.md) per analizzare i dati di Application Insights.

![Analytics in Application Insights](media/data-platform-logs/app-insights-analytics.png)

È anche possibile recuperare i dati di log usando l' [api log Analytics](https://dev.loganalytics.io/documentation/overview) e l' [api REST di Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Origini dei log di monitoraggio di Azure
Monitoraggio di Azure può raccogliere dati di logo da un'ampia gamma di origini sia all'interno di Azure sia da risorse locali. Le tabelle seguenti elencano le diverse origini dati disponibili da risorse diverse che scrivono i dati nei log di monitoraggio di Azure. Ogni contiene un collegamento ai dettagli su qualsiasi configurazione richiesta.

### <a name="azure-tenant-and-subscription"></a>Tenant e sottoscrizione di Azure

| Data | Descrizione |
|:---|:---|
| Log di controllo Azure Active Directory | Configurato tramite le impostazioni di diagnostica per ogni directory. Vedere [integrare Azure ad log con i log di monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Log attività | Archiviato separatamente per impostazione predefinita e può essere usato per gli avvisi near Real Time. Installare la soluzione log Analytics di attività per scrivere in Log Analytics area di lavoro. Vedere [raccogliere e analizzare i log attività di Azure in log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Risorse di Azure

| Data | Descrizione |
|:---|:---|
| Diagnostica delle risorse | Configurare le impostazioni di diagnostica per scrivere nei dati di diagnostica, incluse le metriche in un'area di lavoro Log Analytics. Vedere [eseguire lo streaming dei log di diagnostica di Azure per log Analytics](resource-logs-collect-storage.md). |
| Soluzioni di monitoraggio | Le soluzioni di monitoraggio scrivono i dati raccolti nell'area di lavoro Log Analytics. Per un elenco delle soluzioni, vedere [Dettagli sulla raccolta dati per le soluzioni di gestione in Azure](../insights/solutions-inventory.md) . Per informazioni dettagliate sull'installazione e l'uso di soluzioni, vedere [monitoraggio delle soluzioni in monitoraggio di Azure](../insights/solutions.md) . |
| metrics | Inviare le metriche della piattaforma per le risorse di monitoraggio di Azure a un'area di lavoro di Log Analytics per conservare i dati di log per periodi più lunghi e per eseguire analisi complesse con altri tipi di dati usando il [linguaggio di query kusto](/azure/kusto/query/). Vedere [eseguire lo streaming dei log di diagnostica di Azure per log Analytics](resource-logs-collect-storage.md). |
| Archiviazione tabelle di Azure | Raccogliere i dati da archiviazione di Azure in cui alcune risorse di Azure scrivono i dati di monitoraggio. [Per gli eventi con log Analytics, vedere usare l'archiviazione BLOB di Azure per IIS e l'archiviazione tabelle di Azure](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Macchine virtuali

| Data | Descrizione |
|:---|:---|
|  Origini dati degli agenti | Le origini dati raccolte dagli agenti [Windows](agent-windows.md) e [Linux](../learn/quick-collect-linux-computer.md) includono eventi, dati sulle prestazioni e log personalizzati. Per un elenco di origini dati e informazioni dettagliate sulla configurazione, vedere [origini dati degli agenti in monitoraggio di Azure](data-sources.md) . |
| Soluzioni di monitoraggio | Le soluzioni di monitoraggio scrivono i dati raccolti dagli agenti nell'area di lavoro Log Analytics. Per un elenco delle soluzioni, vedere [Dettagli sulla raccolta dati per le soluzioni di gestione in Azure](../insights/solutions-inventory.md) . Per informazioni dettagliate sull'installazione e l'uso di soluzioni, vedere [monitoraggio delle soluzioni in monitoraggio di Azure](../insights/solutions.md) . |
| System Center Operations Manager | Connettere Operations Manager gruppo di gestione a monitoraggio di Azure per raccogliere dati di eventi e prestazioni dagli agenti locali nei log. Per informazioni dettagliate su questa configurazione, vedere [connettersi Operations Manager log Analytics](om-agents.md) . |


### <a name="applications"></a>Applicazioni

| Data | Descrizione |
|:---|:---|
| Richieste ed eccezioni | I dati dettagliati relativi alle richieste e alle eccezioni delle applicazioni si trovano nelle tabelle _richieste_, _pagine di visualizzazione_ed _eccezioni_ . Le chiamate ai [componenti esterni](../app/asp-net-dependencies.md) si trovano nella tabella delle _dipendenze_ . |
| Utilizzo e prestazioni | Le prestazioni dell'applicazione sono disponibili nelle tabelle _richieste_, _browserTimings_ e _PerformanceCounters_ . I dati per le [metriche personalizzate](../app/api-custom-events-metrics.md#trackevent) si trova nella tabella _customMetrics_ .|
| Dati di traccia | I risultati della [traccia distribuita](../app/distributed-tracing.md) vengono archiviati nella tabella _TRACES_ . |
| Test di disponibilità | I dati di riepilogo dei [test di disponibilità](../app/monitor-web-app-availability.md) vengono archiviati nella tabella _availabilityResults_ . I dati dettagliati di questi test si trovano in una risorsa di archiviazione separata ed è possibile accedervi da Application Insights nel portale di Azure. |

### <a name="insights"></a>Informazioni dettagliate

| Data | Descrizione |
|:---|:---|
| Monitoraggio di Azure per i contenitori | Dati sulle prestazioni e sull'inventario raccolti da [monitoraggio di Azure per i contenitori](../insights/container-insights-overview.md). Per un elenco delle tabelle, vedere la pagina relativa ai [Dettagli della raccolta dei dati del contenitore](../insights/container-insights-log-search.md#container-records) . |
| Monitoraggio di Azure per le macchine virtuali | Mappa e dati sulle prestazioni raccolti da [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md). Per informazioni dettagliate sull'esecuzione di query sui dati, vedere [come eseguire query sui log da monitoraggio di Azure per le macchine virtuali](../insights/vminsights-log-search.md) . |

### <a name="custom"></a>Personalizzato 

| Data | Descrizione |
|:---|:---|
| API REST | Scrivere i dati in un'area di lavoro Log Analytics da qualsiasi client REST. Per informazioni dettagliate, vedere [inviare i dati di log a monitoraggio di Azure con l'API dell'agente di raccolta dati http](data-collector-api.md) .
| App per la logica | Scrivere i dati in un'area di lavoro Log Analytics da un flusso di lavoro dell'app per la logica con l'azione dell' **agente di raccolta dati di Azure log Analytics** |

### <a name="security"></a>Security

| Data | Descrizione |
|:---|:---|
| Centro sicurezza di Azure | Il [Centro sicurezza di Azure](/azure/security-center/) archivia i dati raccolti in un'area di lavoro log Analytics dove possono essere analizzati con altri dati di log. Per informazioni dettagliate sulla configurazione dell'area di lavoro, vedere [raccolta dati nel centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md) . |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) archivia i dati dalle origini dati in un'area di lavoro log Analytics. Vedere [Connect Data Sources](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sulla [piattaforma dati di monitoraggio di Azure](data-platform.md).
- Informazioni sulle [metriche in monitoraggio di Azure](data-platform-metrics.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
