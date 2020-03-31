---
title: Struttura dei log di Monitoraggio di Azure - Documenti Microsoft
description: È necessaria una query di log per recuperare i dati di log da Monitoraggio di Azure.  Questo articolo descrive come vengono usate le nuove query di log in Monitoraggio di Azure e illustra i concetti con cui avere familiarità prima di crearne una.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662077"
---
# <a name="structure-of-azure-monitor-logs"></a>Struttura dei log di Monitoraggio di Azure
La possibilità di ottenere rapidamente informazioni dettagliate sui dati usando una query di log è una potente funzionalità di Monitoraggio di Azure.The ability to quickly gain insights into your data using a [log query](log-query-overview.md) is a powerful feature of Azure Monitor. Per creare query efficienti e utili, è necessario comprendere alcuni concetti di base, ad esempio la posizione dei dati desiderati e la modalità di struttura. In questo articolo vengono forniti i concetti di base necessari per iniziare.

## <a name="overview"></a>Panoramica
I dati nei log di Monitoraggio di Azure vengono archiviati in un'area di lavoro di Log Analytics o in un'applicazione di Application Insights.Data in Azure Monitor Logs is stored in either a Log Analytics workspace or an Application Insights application. Entrambi sono basati su [Azure Data Explorer,](/azure/data-explorer/) il che significa che sfruttano il motore dati potente e il linguaggio di query.

I dati nelle aree di lavoro e nelle applicazioni sono organizzati in tabelle, ognuna delle quali archivia diversi tipi di dati e dispone di un proprio set univoco di proprietà. La maggior parte delle [origini dati](../platform/data-sources.md) scriverà nelle proprie tabelle in un'area di lavoro di Log Analytics, mentre Application Insights scriverà in un set predefinito di tabelle in un'applicazione Application Insights. Le query di log sono molto flessibili, consentendo di combinare facilmente i dati di più tabelle e persino di utilizzare una query tra risorse per combinare i dati delle tabelle in più aree di lavoro o per scrivere query che combinano dati dell'area di lavoro e dell'applicazione.

L'immagine seguente mostra esempi di origini dati che scrivono in tabelle diverse utilizzate nelle query di esempio.

![Tabelle](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Tutti i dati raccolti dai log di Monitoraggio di Azure, ad eccezione di Application Insights, vengono archiviati in un'area di lavoro di [Log Analytics.](../platform/manage-access.md) È possibile creare una o più aree di lavoro in base alle esigenze specifiche. [Origini dati,](../platform/data-sources.md) ad esempio log attività e log delle risorse dalle risorse di Azure, agenti nelle macchine virtuali e dati da soluzioni di monitoraggio e informazioni dettagliate scriveranno i dati in una o più aree di lavoro configurate come parte dell'onboarding. Altri servizi, ad esempio [Centro sicurezza di Azure](/azure/security-center/) e Azure [Sentinel](/azure/sentinel/) usano anche un'area di lavoro di Log Analytics per archiviare i dati in modo che possano essere analizzati usando query di log insieme a dati di monitoraggio provenienti da altre origini.

Tipi di dati diversi vengono archiviati in tabelle diverse nell'area di lavoro e ogni tabella dispone di un set univoco di proprietà. Un set standard di tabelle viene aggiunto a un'area di lavoro quando viene creata e vengono aggiunte nuove tabelle per origini dati, soluzioni e servizi diversi man mano che vengono integrate. È inoltre possibile creare tabelle personalizzate utilizzando [l'API dell'agente](../platform/data-collector-api.md)di raccolta dati .

È possibile sfogliare le tabelle in un'area di lavoro e il relativo schema nella scheda **Schema** in Log Analytics per l'area di lavoro.

![Schema dell'area di lavoro](media/scope/workspace-schema.png)

Utilizzare la query seguente per elencare le tabelle nell'area di lavoro e il numero di record raccolti in ognuna nel giorno precedente. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Per informazioni dettagliate sulle tabelle create, vedere la documentazione relativa a ogni origine dati. Gli esempi includono articoli per [origini dati agente,](../platform/agent-data-sources.md) [log risorse](../platform/diagnostic-logs-schema.md)e soluzioni di [monitoraggio.](../insights/solutions-inventory.md)

### <a name="workspace-permissions"></a>Autorizzazioni per l'area di lavoro
Vedere [Progettazione di una distribuzione di Log di Monitoraggio di Azure](../platform/design-logs-deployment.md) per comprendere la strategia di controllo di accesso e i consigli per fornire l'accesso ai dati in un'area di lavoro. Oltre a concedere l'accesso all'area di lavoro stessa, è possibile limitare l'accesso alle singole tabelle utilizzando il controllo degli accessi in base al ruolo a livello di [tabella.](../platform/manage-access.md#table-level-rbac)

## <a name="application-insights-application"></a>Applicazione Application Insights
Quando si crea un'applicazione in Application Insights, viene creata automaticamente un'applicazione corrispondente nei log di Monitoraggio di Azure.When you create an application in Application Insights, a corresponding application is automatically created in Azure Monitor Logs. Non è necessaria alcuna configurazione per raccogliere dati e l'applicazione scriverà automaticamente i dati di monitoraggio, ad esempio visualizzazioni di pagina, richieste ed eccezioni.

A differenza di un'area di lavoro di Log Analytics, un'applicazione Application Insights dispone di un set fisso di tabelle. Non è possibile configurare altre origini dati per scrivere nell'applicazione in modo che non sia possibile creare tabelle aggiuntive. 

| Tabella | Descrizione | 
|:---|:---|
| availabilityRisultati | Dati di riepilogo dai test di disponibilità. |
| browserTimings      | Dati sulle prestazioni del client, ad esempio il tempo necessario per elaborare i dati in ingresso. |
| customEvents        | Eventi personalizzati creati dall'applicazione. |
| customMetrics       | Metriche personalizzate create dall'applicazione. |
| dependencies        | Chiamate dall'applicazione a componenti esterni. |
| eccezioni          | Eccezioni generate dal runtime dell'applicazione. |
| Pagine visualizzate           | Dati relativi a ogni visualizzazione del sito Web con le informazioni del browser. |
| performanceCounters | Misurazioni delle prestazioni dalle risorse di calcolo che supportano l'applicazione. |
| requests            | Dettagli di ogni richiesta di domanda.  |
| traces              | Risultati della traccia distribuita. |

È possibile visualizzare lo schema per ogni tabella nella scheda **Schema** in Log Analytics per l'applicazione.

![Schema dell'applicazione](media/scope/application-schema.png)

## <a name="standard-properties"></a>Proprietà standard
Mentre ogni tabella nei log di Monitoraggio di Azure ha il proprio schema, esistono proprietà standard condivise da tutte le tabelle. Per informazioni dettagliate, vedere Proprietà standard nei log di Monitoraggio di [Azure.See Standard properties in Azure Monitor Logs](../platform/log-standard-properties.md) for details of each.

| Area di lavoro Log Analytics | Applicazione Application Insights | Descrizione |
|:---|:---|:---|
| TimeGenerated |  timestamp  | Data e ora di creazione del record. |
| Type          | itemType   | Nome della tabella da cui è stato recuperato il record. |
| _ResourceId   |            | Identificatore univoco della risorsa a cui è associato il record. |
| _IsBillable   |            | Specifica se i dati ingeriti sono fatturabili. |
| _BilledSize   |            | Specifica la dimensione in byte di dati che verranno fatturati. |

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sull'utilizzo di [Log Analytics per creare e modificare le ricerche nei log.](../log-query/portals.md)
- Vedere un'[esercitazione sulla scrittura di query](../log-query/get-started-queries.md) con il nuovo linguaggio di query.
