---
title: I log in Monitoraggio di Azure | Microsoft Docs
description: Descrive i log in Monitoraggio di Azure che vengono usati per l'analisi avanzata dei dati di monitoraggio.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: 59213c5391b5b652eeead05c4a5af761571fcece
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360637"
---
# <a name="logs-in-azure-monitor"></a>Log in Monitoraggio di Azure

> [!NOTE]
> Tutti i dati raccolti da monitoraggio di Azure rientra in uno dei due tipi fondamentali, metriche e log. Questo articolo descrive i log. Fare riferimento a [le metriche in Monitoraggio di Azure](data-platform-metrics.md) per una descrizione dettagliata delle metriche e di ottenere [i dati di monitoraggio raccolti da monitoraggio di Azure](data-platform.md) per un confronto tra i due.

I log in Monitoraggio di Azure sono particolarmente utili per eseguire analisi complesse tra i dati da diverse origini. Questo articolo descrive come i log sono strutturate in Monitoraggio di Azure, operazioni eseguibili con i dati e identifica le origini dati diverse che archiviano i dati nei log.

> [!NOTE]
> È importante distinguere tra i log di monitoraggio di Azure e origini dei dati di log in Azure. Ad esempio, in cui vengono scritti gli eventi a livello di sottoscrizione di Azure a un [log attività](activity-logs-overview.md) che è possibile visualizzare dal menu di monitoraggio di Azure. La maggior parte delle risorse scriverà le informazioni operative per un [log di diagnostica](diagnostic-logs-overview.md) che è possibile inoltrare a posizioni diverse. Log di monitoraggio di Azure è una piattaforma di dati di log che raccoglie i log attività e nei log di diagnostica con altri dati di monitoraggio per fornire un'analisi approfondita in tutto il set di risorse.

## <a name="what-are-azure-monitor-logs"></a>Quali sono i log di monitoraggio di Azure?

I log in Monitoraggio di Azure contengono diversi tipi di dati organizzati in record con diversi set di proprietà per ogni tipo. I log possono contenere valori numerici, ad esempio le metriche di monitoraggio di Azure ma in genere contengono dati di testo con una descrizione dettagliata. Si differenziano ulteriormente dai dati delle metriche in quanto essi variano la struttura e spesso non vengono raccolti a intervalli regolari. I dati di telemetria, ad esempio eventi e tracce vengono archiviati i log di monitoraggio di Azure oltre i dati sulle prestazioni in modo che sia possibile tutti essere combinati per l'analisi.

Un tipo di voce di log comune è un evento, che viene raccolta sporadicamente. Gli eventi vengono creati da un'applicazione o servizio e in genere includono informazioni sufficienti per fornire un contesto completo in modo indipendente. Un evento indica, ad esempio, la creazione o la modifica di una risorsa, l'avvio di un nuovo host in risposta all'aumento del traffico o il rilevamento di un errore in un'applicazione.

 Poiché il formato dei dati può variare, le applicazioni possono creare log personalizzati usando la struttura necessaria. Dati delle metriche possono essere archiviati anche nei log per combinarli con altri dati di monitoraggio per l'individuazione di tendenze e altre operazioni di analisi dei dati.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Cosa può fare con i log di monitoraggio di Azure?
La tabella seguente elenca i diversi modi, che è possibile usare i log in Monitoraggio di Azure.


|  |  |
|:---|:---|
| Analizzare | Uso [Log Analitica](../log-query/get-started-portal.md) nel portale di Azure per scrivere [registrare query](../log-query/log-query-overview.md) e analizzare in modo interattivo i dati di log usando il motore di analisi avanzato di Esplora dati.<br>Usare la [console di Application Insights analitica](../app/analytics.md) nel portale di Azure per scrivere le query di log e analizzare in modo interattivo i dati di log da Application Insights. |
| Visualizzazione | Aggiungere i risultati della query viene eseguito il rendering come tabelle o grafici a un [dashboard Azure](../../azure-portal/azure-portal-dashboards.md).<br>Creare un [cartella di lavoro](../app/usage-workbooks.md) da combinare con più set di dati in un report interattivo. <br>Esportazione dei risultati di una query in [Power BI](powerbi.md) per usare diverse visualizzazioni e condividerle con utenti esternamente ad Azure.<br>Esportare i risultati di una query sul [Grafana](grafana-plugin.md) per sfruttare la creazione di dashboard e si combina con altre origini dati.|
| Avviso | Configurazione di una [regola di avviso per il log](alerts-log.md) che invia una notifica o esegue un'[azione automatica](action-groups.md) quando i risultati della query corrispondono a un risultato specifico.<br>Configurare un [regola di avviso metrica](alerts-metric-logs.md) su alcuni log i log dei dati estratti come metriche. |
| Recupero | Accedere ai risultati della query di log da una riga di comando usando [CLI Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Accedere ai risultati della query di log da una riga di comando usando [i cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Accedere ai risultati della query di log da un'applicazione personalizzata con [API REST](https://dev.loganalytics.io/). |
| Esportazione | Creare un flusso di lavoro per recuperare i dati di log e copiarlo in una posizione esterna usando [App per la logica](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Quali sono i dati nei log di monitoraggio di Azure strutturata?
Dati raccolti da log di monitoraggio di Azure vengono archiviati in un [dell'area di lavoro di Log Analitica](../platform/manage-access.md). È possibile [creare più aree di lavoro](manage-access.md#determine-the-number-of-workspaces-you-need) nella propria sottoscrizione per gestire diversi set di dati di log. Ogni area di lavoro contiene più tabelle che contengono ciascuno dei dati da una determinata origine. Mentre tutte le tabelle condividono [alcune proprietà comuni](log-standard-properties.md), ognuno ha un solo set di proprietà in base al tipo di dati archiviati. Una nuova area di lavoro avrà il set standard di tabelle e verranno aggiunti ulteriori tabelle diverse soluzioni di monitoraggio e altri servizi che scrivono nell'area di lavoro.

Dati di log da Application Insights Usa lo stesso motore Log Analitica come aree di lavoro, ma vengano archiviati separatamente per ogni applicazione monitorata. Ogni applicazione dispone di un set standard di tabelle per contenere i dati, ad esempio le applicazioni richieste, eccezioni e visualizzazioni pagina.

Query di log verranno Usa dati da un'area di lavoro di Log Analitica né un'applicazione di Application Insights. È possibile usare una [query tra risorse](../log-query/cross-workspace-query.md) per analizzare i dati dell'applicazione insieme agli altri dati di log o per creare query tra più aree di lavoro o applicazioni.

![Aree di lavoro](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Query di log
I dati nei log di monitoraggio di Azure vengono recuperati usando un [query di log](../log-query/log-query-overview.md) scritti con la [linguaggio di query Kusto](../log-query/get-started-queries.md), che consente di recuperare, consolidare e analizzare i dati raccolti. Uso [Log Analitica](../log-query/portals.md) scrivere e testare le query di log nel portale di Azure. Consente di lavorare con i risultati in modo interattivo o aggiungerli a un dashboard per visualizzarli con altre visualizzazioni.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Aprire [Analitica di Log da Application Insights](../app/analytics.md) per analizzare i dati di Application Insights.

![Analytics in Application Insights](media/data-platform-logs/app-insights-analytics.png)

È anche possibile recuperare i dati di log usando il [API di Log Analitica](https://dev.loganalytics.io/documentation/overview) e il [API REST di Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Origini dei log di monitoraggio di Azure
Monitoraggio di Azure può raccogliere dati di logo da un'ampia gamma di origini sia all'interno di Azure sia da risorse locali. Le tabelle seguenti elencano le diverse origini dati disponibili da diverse risorse che scrivono dati di log di monitoraggio di Azure. Ognuno ha un collegamento a informazioni dettagliate su qualsiasi configurazione necessaria.

### <a name="azure-tenant-and-subscription"></a>Sottoscrizione e tenant di azure

| Dati | DESCRIZIONE |
|:---|:---|
| Log di controllo di Azure Active Directory | Configurato tramite le impostazioni di diagnostica per ogni directory. Visualizzare [log di integrazione di Azure AD con monitoraggio di Azure log](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Log attività | Archiviati separatamente per impostazione predefinita e può essere usato per quasi in tempo reale gli avvisi. Installare una soluzione Analitica Log attività di scrittura all'area di lavoro di Log Analitica. Visualizzare [raccogliere e analizzare i log attività di Azure in Log Analitica](collect-activity-logs.md). |

### <a name="azure-resources"></a>Risorse di Azure

| Dati | DESCRIZIONE |
|:---|:---|
| Diagnostica di risorsa | Configurare le impostazioni di diagnostica in cui scrivere i dati di diagnostica, incluse le metriche per un'area di lavoro di Log Analitica. Visualizzare [Stream log di diagnostica di Azure a Log Analitica](diagnostic-logs-stream-log-store.md). |
| Soluzioni di monitoraggio | Soluzioni di monitoraggio scrivono i dati che raccolti da tali all'area di lavoro di Log Analitica. Visualizzare [informazioni dettagliate sulla raccolta dei dati per le soluzioni di gestione in Azure](../insights/solutions-inventory.md) per un elenco delle soluzioni. Visualizzare [monitoraggio delle soluzioni in Monitoraggio di Azure](../insights/solutions.md) per informazioni dettagliate sull'installazione e utilizzo di soluzioni. |
| Metriche | Inviare le metriche di piattaforma per le risorse di monitoraggio di Azure a un'area di lavoro di Log Analitica per conservare i dati di log per periodi più lunghi ed eseguire analisi complesse con altri tipi di dati tramite il [linguaggio di query Kusto](/azure/kusto/query/). Visualizzare [Stream log di diagnostica di Azure a Log Analitica](diagnostic-logs-stream-log-store.md). |
| Archiviazione tabelle di Azure | Raccogliere i dati da archiviazione di Azure in cui scrivere alcune risorse di Azure i dati di monitoraggio. Visualizzare [archiviazione blob di Azure di uso per l'archiviazione di tabelle di Azure e IIS per gli eventi con Log Analitica](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Macchine virtuali

| Dati | DESCRIZIONE |
|:---|:---|
|  Origini dati degli agenti | Origini dei dati raccolti dai [Windows](agent-windows.md) e [Linux](../learn/quick-collect-linux-computer.md) gli agenti includono gli eventi, i dati sulle prestazioni e log personalizzati. Visualizzare [origini dati dell'agente in Monitoraggio di Azure](data-sources.md) per un elenco delle origini dati e informazioni dettagliate sulla configurazione. |
| Soluzioni di monitoraggio | Soluzioni di monitoraggio scrivono i dati che raccolti dagli agenti all'area di lavoro di Log Analitica. Visualizzare [informazioni dettagliate sulla raccolta dei dati per le soluzioni di gestione in Azure](../insights/solutions-inventory.md) per un elenco delle soluzioni. Visualizzare [monitoraggio delle soluzioni in Monitoraggio di Azure](../insights/solutions.md) per informazioni dettagliate sull'installazione e utilizzo di soluzioni. |
| System Center Operations Manager | Connettere il gruppo di gestione di Operations Manager a monitoraggio di Azure per raccogliere dati di eventi e prestazioni dagli agenti locali nei log. Visualizzare [connettere Operations Manager a Log Analitica](om-agents.md) per informazioni dettagliate su questa configurazione. |


### <a name="applications"></a>APPLICAZIONI

| Dati | DESCRIZIONE |
|:---|:---|
| Richieste ed eccezioni | Dati dettagliati sulle richieste di applicazioni e le eccezioni sono nel _richieste_, _pageViews_, e _eccezioni_ tabelle. Le chiamate a [componenti esterni](../app/asp-net-dependencies.md) nel _dipendenze_ tabella. |
| Utilizzo e le prestazioni | Le prestazioni dell'applicazione sono disponibile nel _richieste_, _browserTimings_ e _performanceCounters_ tabelle. I dati per [metriche personalizzate](../app/api-custom-events-metrics.md#trackevent) è la _customMetrics_ tabella.|
| I dati di traccia | È dovuta [analisi distribuita](../app/distributed-tracing.md) vengono archiviati nel _tracce_ tabella. |
| Test della disponibilità | Dati di riepilogo [test di disponibilità](../app/monitor-web-app-availability.md) viene archiviato nel _availabilityResults_ tabella. Dati dettagliati da questi test sono in un archivio separato e accessibili da Application Insights nel portale di Azure. |

### <a name="insights"></a>Informazioni dettagliate

| Dati | DESCRIZIONE |
|:---|:---|
| Monitoraggio di Azure per contenitori | Dati di inventario e delle prestazioni raccolti da [monitoraggio di Azure per contenitori](../insights/container-insights-overview.md). Visualizzare [dettagli di raccolta dati contenitore](../insights/container-insights-analyze.md#container-data-collection-details) per un elenco delle tabelle. |
| Monitoraggio di Azure per le macchine virtuali | Dati della mappa e sulle prestazioni raccolti da [monitoraggio di Azure per le macchine virtuali](../insights/vminsights-overview.md). Visualizzare [come eseguire una query di log da monitoraggio di Azure per le macchine virtuali](../insights/vminsights-log-search.md) per informazioni dettagliate sull'esecuzione di query su questi dati. |

### <a name="custom"></a>Personalizzate 

| Dati | DESCRIZIONE |
|:---|:---|
| API REST | Scrivere dati in un'area di lavoro di Log Analitica da qualsiasi client REST. Visualizzare [invio dati di log al monitoraggio di Azure con l'API dell'agente di raccolta dati HTTP](data-collector-api.md) per informazioni dettagliate.
| App per la logica | Scrivere tutti i dati in un'area di lavoro di Log Analitica da un flusso di lavoro di App per la logica con il **agente di raccolta dati di Azure Log Analitica** azione. |

### <a name="security"></a>Security

| Dati | DESCRIZIONE |
|:---|:---|
| Centro sicurezza di Azure | [Centro sicurezza di Azure](/azure/security-center/) archivia i dati raccolti in un'area di lavoro di Log Analitica dove possono essere analizzati con gli altri dati di log. Visualizzare [la raccolta dei dati nel Centro sicurezza Azure](../../security-center/security-center-enable-data-collection.md) per informazioni dettagliate sulla configurazione dell'area di lavoro. |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) archivia i dati da origini dati in un'area di lavoro di Log Analitica. Visualizzare [connettere origini dati](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [piattaforma dati di monitoraggio di Azure](data-platform.md).
- Scopri [le metriche in Monitoraggio di Azure](data-platform-metrics.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
