---
title: Struttura dei log di Monitoraggio di Azure | Microsoft Docs
description: È necessaria una query di log per recuperare i dati di log da Monitoraggio di Azure.  Questo articolo descrive come vengono usate le nuove query di log in Monitoraggio di Azure e illustra i concetti con cui avere familiarità prima di crearne una.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2020
ms.openlocfilehash: 58724656dd407f09687b57d0ab034f3a1f808b76
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196279"
---
# <a name="structure-of-azure-monitor-logs"></a>Struttura dei log di Monitoraggio di Azure
La possibilità di ottenere rapidamente informazioni dettagliate sui dati usando una [query di log](log-query-overview.md) è una funzionalità potente di Monitoraggio di Azure. Per creare query efficienti e utili, è necessario comprendere alcuni concetti di base, ad esempio la posizione dei dati desiderati e il modo in cui sono strutturati. Questo articolo illustra i concetti di base necessari per iniziare.

## <a name="overview"></a>Panoramica
I dati in log di Monitoraggio di Azure vengono archiviati in un'area di lavoro Log Analytics o in un'applicazione di Application Insights. Entrambe si basano su [Esplora dati di Azure](/azure/data-explorer/) e sfruttano quindi il potente motore dati e il linguaggio di query.

> [!IMPORTANT]
> Se si usa una [risorsa di Application Insights basata sull'area di lavoro](../app/create-workspace-resource.md), i dati di telemetria vengono archiviati in un'area di lavoro Log Analytics con tutti gli altri dati di log. Le tabelle sono state rinominate e ristrutturate ma presentano le stesse informazioni delle tabelle nell'applicazione di Application Insights.

I dati nelle aree di lavoro e nelle applicazioni sono organizzati in tabelle, ognuna delle quali archivia diversi tipi di dati e dispone di un proprio set di proprietà univoco. La maggior parte delle [origini dati](../platform/data-sources.md) scriverà nelle proprie tabelle in un'area di lavoro Log Analytics, mentre Application Insights scriverà in un set predefinito di tabelle in un'applicazione di Application Insights. Le query di log sono molto flessibili, consentendo di combinare facilmente i dati provenienti da più tabelle e persino di usare una query tra risorse per combinare i dati delle tabelle in più aree di lavoro o scrivere query che combinano i dati dell'area di lavoro e dell'applicazione.

Nell'immagine seguente vengono illustrati esempi di origini dati che scrivono in tabelle diverse usate nelle query di esempio.

![Tabelle](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Tutti i dati raccolti dai log di Monitoraggio di Azure vengono archiviati in un'[area di lavoro Log Analytics](../platform/manage-access.md), ad eccezione di Application Insights. È possibile creare una o più aree di lavoro in base a requisiti specifici. [Le origini dati](../platform/data-sources.md) come log attività e log risorse delle risorse di Azure, agenti su macchine virtuali e dati da soluzioni di monitoraggio e informazioni dettagliate scrivono i dati in una o più aree di lavoro configurate come parte dell'onboarding. Anche altri servizi, ad esempio [Centro sicurezza di Azure](/azure/security-center/) e [Azure Sentinel](/azure/sentinel/) usano un'area di lavoro Log Analytics per archiviare i dati in modo che possano essere analizzati usando le query di log insieme a dati di monitoraggio provenienti da altre origini.

Diversi tipi di dati vengono archiviati in tabelle diverse dell'area di lavoro e ogni tabella dispone di un set di proprietà univoco. Un set standard di tabelle viene aggiunto a un'area di lavoro al momento della creazione e durante l'onboarding vengono aggiunte nuove tabelle per diverse origini dati, soluzioni e servizi. È anche possibile creare tabelle personalizzate usando l'[API dell'Agente di raccolta dati](../platform/data-collector-api.md).

È possibile esplorare le tabelle in un'area di lavoro e il relativo schema nella scheda **Schema** in Log Analytics per l'area di lavoro.

![Schema dell'area di lavoro](media/scope/workspace-schema.png)

Usare la query seguente per elencare le tabelle nell'area di lavoro e il numero di record raccolti in ognuna di esse nel giorno precedente. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Per informazioni dettagliate sulle tabelle create, vedere la documentazione per ogni origine dati. Gli esempi includono articoli per [origini dati degli agenti](../platform/agent-data-sources.md), [log risorse](../platform/diagnostic-logs-schema.md) e [soluzioni di monitoraggio](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Autorizzazioni dell'area di lavoro
Vedere [Progettazione di una distribuzione dei log di Monitoraggio di Azure](../platform/design-logs-deployment.md) per comprendere la strategia di controllo degli accessi e le raccomandazioni per offrire l'accesso ai dati in un'area di lavoro. Oltre a concedere l'accesso all'area di lavoro stessa, è possibile limitare l'accesso a singole tabelle usando [Controllo degli accessi in base al ruolo a livello di tabella](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Applicazione di Application Insights

> [!IMPORTANT]
> Se si usa una [risorsa di Application Insights basata sull'area di lavoro](../app/create-workspace-resource.md), i dati di telemetria vengono archiviati in un'area di lavoro Log Analytics con tutti gli altri dati di log. Le tabelle sono state rinominate e ristrutturate ma presentano le stesse informazioni delle tabelle nella risorsa classica di Application Insights.

Quando si crea un'applicazione in Application Insights, viene creata automaticamente un'applicazione corrispondente nei log di Monitoraggio di Azure. Non è necessaria alcuna configurazione per la raccolta di dati e l'applicazione scriverà automaticamente i dati di monitoraggio, ad esempio visualizzazioni di pagina, richieste ed eccezioni.

A differenza di un'area di lavoro Log Analytics, un'applicazione di Application Insights dispone di un set fisso di tabelle. Non è possibile configurare altre origini dati da scrivere nell'applicazione in modo da non creare tabelle aggiuntive. 

| Tabella | Descrizione | 
|:---|:---|
| availabilityResults | Dati di riepilogo da test di disponibilità. |
| browserTimings      | Dati sulle prestazioni del client, ad esempio il tempo impiegato per elaborare i dati in ingresso. |
| customEvents        | Eventi personalizzati creati dall'applicazione. |
| customMetrics       | Metriche personalizzate create dall'applicazione. |
| dipendenze        | Chiamate dall'applicazione ad altri componenti (inclusi componenti esterni) registrati tramite TrackDependency(), ad esempio chiamate ad API REST, database o un file system. |
| eccezioni          | Eccezioni generate dal runtime dell'applicazione, acquisisce sia le eccezioni lato server sia quelle lato client (browser).|
| pageViews           | Dati relativi a ogni visualizzazione del sito Web con le informazioni del browser. |
| performanceCounters | Misurazioni delle prestazioni dalle risorse di calcolo che supportano l'applicazione, ad esempio, i contatori delle prestazioni di Windows. |
| requests            | Richieste ricevute dall'applicazione. Ad esempio, viene registrato un record di richiesta separato per ogni richiesta HTTP ricevuta dall'app Web.  |
| traces              | Log dettagliati (traces) emessi tramite il codice dell'applicazione/framework di registrazione registrati tramite TrackTrace(). |

È possibile visualizzare lo schema per ogni tabella nella scheda **Schema** in Log Analytics per l'applicazione.

![Schema dell'applicazione](media/scope/application-schema.png)

## <a name="standard-properties"></a>Proprietà standard
Anche se ogni tabella nei log di Monitoraggio di Azure ha un proprio schema, sono presenti proprietà standard condivise da tutte le tabelle. Per informazioni dettagliate su ogni proprietà, vedere [Proprietà standard nei log di Monitoraggio di Azure](../platform/log-standard-properties.md).

| Area di lavoro Log Analytics | Applicazione di Application Insights | Descrizione |
|:---|:---|:---|
| TimeGenerated | timestamp  | Data e ora di creazione del record. |
| Type          | itemType   | Nome della tabella da cui è stato recuperato il record. |
| _ResourceId   |            | Identificatore univoco per la risorsa associata al record. |
| _IsBillable   |            | Specifica se i dati inseriti sono fatturabili. |
| _BilledSize   |            | Specifica le dimensioni in byte dei dati che verranno fatturati. |

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come usare [Log Analytics per creare e modificare le ricerche nei log](../log-query/portals.md).
- Vedere un'[esercitazione sulla scrittura di query](../log-query/get-started-queries.md) con il nuovo linguaggio di query.