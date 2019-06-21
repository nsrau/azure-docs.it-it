---
title: Struttura dei log di monitoraggio di Azure | Microsoft Docs
description: È necessaria una query di log per recuperare i dati di log da Monitoraggio di Azure.  Questo articolo descrive come vengono usate le nuove query di log in Monitoraggio di Azure e illustra i concetti con cui avere familiarità prima di crearne una.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: e243ebbc31f9e941678ac76a83738276995b5ba1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297298"
---
# <a name="structure-of-azure-monitor-logs"></a>Struttura dei log di monitoraggio di Azure
La possibilità di ottenere rapidamente informazioni dettagliate sui tuoi dati usando un [query di log](log-query-overview.md) è una potente funzionalità di monitoraggio di Azure. Per creare query efficienti e utile, è necessario comprendere alcuni concetti di base, ad esempio in cui si trovano i dati desiderati e la struttura. Questo articolo illustra i concetti di base che necessari per iniziare.

## <a name="overview"></a>Panoramica
Dati nei log di monitoraggio di Azure vengono archiviati in un'area di lavoro di Log Analitica o un'applicazione di Application Insights. Entrambi sono basate su GPU [Esplora dati di Azure](/azure/data-explorer/) vale a dire che si avvalgono di un linguaggio di motore e query avanzati per i dati.

Dati in entrambe le aree di lavoro e le applicazioni sono organizzati in tabelle, ognuno dei quali archivia i diversi tipi di dati e si ha un proprio set di proprietà univoco. La maggior parte degli [zdroje dat](../platform/data-sources.md) scriverà autonomamente le tabelle in un'area di lavoro di Log Analitica, mentre Application Insights verranno scritti in un set predefinito di tabelle in un'applicazione di Application Insights. Le query di log sono molto flessibile che consente di combinare facilmente i dati da più tabelle e anche usare una query più risorse per combinare dati provenienti da tabelle in più aree di lavoro o per scrivere le query che combinano i dati dell'area di lavoro e l'applicazione.

L'immagine seguente mostra alcuni esempi di origini dati che scrivono in diverse tabelle utilizzate nella query di esempio.

![Tabelle](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Tutti i dati raccolti da log di monitoraggio di Azure ad eccezione di Application Insights vengono archiviati in un [dell'area di lavoro di Log Analitica](../platform/manage-access.md). È possibile creare uno o più aree di lavoro a seconda delle esigenze specifiche. [Zdroje dat](../platform/data-sources.md) , ad esempio log di log attività e di diagnostica dalle risorse di Azure, gli agenti di macchine virtuali e i dati delle informazioni dettagliate e soluzioni di monitoraggio dati verranno scritti in uno o più aree di lavoro configurata come parte del loro onboarding. Altri servizi, ad esempio [Centro sicurezza di Azure](/azure/security-center/) e [Azure Sentinel](/azure/sentinel/) anche usare un'area di lavoro di Log Analitica per archiviare i dati in modo che può essere analizzato con query di log insieme ai dati da altre di monitoraggio origini dati.

Diversi tipi di dati vengono archiviati in tabelle diverse nell'area di lavoro e ogni tabella dispone di un set di proprietà univoco. Un set standard di tabelle vengono aggiunte a un'area di lavoro quando viene creato e vengono aggiunte nuove tabelle per origini dati diverse, soluzioni e servizi non appena vengono caricate. È anche possibile creare tabelle personalizzate usando il [API di raccolta dati](../platform/data-collector-api.md).

È possibile esplorare le tabelle in un'area di lavoro e i relativi schemi nel **Schema** scheda nel Log Analitica per l'area di lavoro.

![Schema dell'area di lavoro](media/scope/workspace-schema.png)

Usare la query seguente per elencare le tabelle nell'area di lavoro e il numero di record raccolti in ognuno nel giorno precedente. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Vedere la documentazione per ogni origine dati per i dettagli delle tabelle che vengono create. Esempi includono gli articoli relativi [origini dati dell'agente](../platform/agent-data-sources.md), [log di diagnostica](../platform/diagnostic-logs-schema.md), e [soluzioni di monitoraggio](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Autorizzazioni dell'area di lavoro
Visualizzare [le autorizzazioni dell'area di lavoro e l'ambito](../platform/manage-access.md#workspace-permissions-and-scope) per informazioni dettagliate sulla capacità di fornire accesso ai dati in un'area di lavoro. Oltre a concedere l'accesso all'area di lavoro, è possibile limitare l'accesso per le singole tabelle usando [RBAC a livello di tabella](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Applicazione di application Insights
Quando si crea un'applicazione in Application Insights, un'applicazione corrispondente viene creata automaticamente nel log di monitoraggio di Azure. È necessaria alcuna configurazione per raccogliere i dati e l'applicazione scriverà automaticamente il monitoraggio dei dati, ad esempio visualizzazioni pagina, le richieste ed eccezioni.

A differenza di un'area di lavoro di Log Analitica, un'applicazione di Application Insights dispone di un set fisso di tabelle. Non è possibile configurare altre origini dati in cui scrivere l'applicazione in modo che non è possibile creare alcun tabelle aggiuntive. 

| Tabella | Descrizione | 
|:---|:---|
| availabilityResults | Dati di riepilogo dai test di disponibilità. |
| browserTimings      | Dati sulle prestazioni del client, ad esempio il tempo impiegato per elaborare i dati in ingresso. |
| customEvents        | Eventi personalizzati creati dall'applicazione. |
| customMetrics       | Metriche personalizzate create dall'applicazione. |
| Dipendenze        | Chiamate dall'applicazione a componenti esterni. |
| Eccezioni          | Eccezioni generate dal runtime dell'applicazione. |
| Visualizzazioni pagina           | Consente di visualizzare i dati relativi a ogni sito Web con informazioni di visualizzazione. |
| performanceCounters | Misurazioni delle prestazioni tra le risorse di calcolo che supportano l'applicazione. |
| requests            | Dettagli di ogni richiesta dell'applicazione.  |
| traces              | Risultati della traccia distribuita. |

È possibile visualizzare lo schema per ogni tabella di **Schema** scheda nel Log Analitica per l'applicazione.

![Schema dell'applicazione](media/scope/application-schema.png)

## <a name="standard-properties"></a>Proprietà standard
Anche se ogni tabella nel log di monitoraggio di Azure ha un proprio schema, esistono standard proprietà condivise da tutte le tabelle. Visualizzare [delle proprietà Standard nei log di monitoraggio di Azure](../platform/log-standard-properties.md) per informazioni dettagliate di ognuno.

| Area di lavoro Log Analytics | Applicazione di application Insights | Descrizione |
|:---|:---|:---|
| TimeGenerated | timestamp  | Data e ora di che creazione del record. |
| Type          | itemType   | Nome della tabella che di record è stato recuperato dal. |
| _ResourceId   |            | Identificatore univoco per il record della risorsa è associato. |
| _IsBillable   |            | Specifica se i dati inseriti sono fatturabili. |
| _BilledSize   |            | Specifica la dimensione in byte dei dati che verranno fatturati. |

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come usare [ricerche nei log di Log Analitica per creare e modificare](../log-query/portals.md).
- Vedere un'[esercitazione sulla scrittura di query](../log-query/get-started-queries.md) con il nuovo linguaggio di query.
