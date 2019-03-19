---
title: Query di log di Monitoraggio di Azure | Microsoft Docs
description: Riferimenti alle risorse per l'apprendimento su come scrivere query di log in Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: ecbea91cef69c80975dd105c35f526b357033a6b
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749819"
---
# <a name="azure-monitor-log-queries"></a>Query di log di Monitoraggio di Azure
I log di Monitoraggio di Azure si basano su Esplora dati, mentre le query di log di Monitoraggio di Azure usano una versione dello stesso linguaggio di query Kusto. La [documentazione del linguaggio di query Kusto](/azure/kusto/query) contiene tutti i dettagli del linguaggio e deve essere la risorsa principale per la scrittura di query di log di Monitoraggio di Azure. Questa pagina fornisce collegamenti ad altre risorse per apprendere come scrivere le query e sulle differenze con l'implementazione del linguaggio di Monitoraggio di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Introduzione

- [Introduzione a Azure Monitor Log Analitica](get-started-portal.md) è una lezione per la scrittura di query e l'utilizzo con i risultati nel portale di Azure.
- [Introduzione alle query di log in Monitoraggio di Azure](get-started-queries.md) è una lezione per la scrittura di query e l'uso con i dati di log di Monitoraggio di Azure.

## <a name="concepts"></a>Concetti
- [Analizzare i dati di log in Monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md) offre una breve panoramica delle query di log e descrive la struttura dei dati di log di Monitoraggio di Azure.
- [Visualizzazione e analisi dei dati di log in Monitoraggio di Azure](../../azure-monitor/log-query/portals.md) illustra i portali in cui vengono create ed eseguite query di log.

## <a name="reference"></a>Riferimenti

- [Riferimento al linguaggio di query](/azure/kusto/query) è il riferimento completo al linguaggio per il linguaggio di query Kusto.
- [Differenze del linguaggio di query di log in Monitoraggio di Azure](data-explorer-difference.md) descrive le differenze tra le versioni del linguaggio di query Kusto.
- [Proprietà standard nei record di log di Monitoraggio di Azure](../../azure-monitor/platform/log-standard-properties.md) descrive le proprietà standard per tutti i dati di log di Monitoraggio di Azure.
- [Eseguire ricerche incrociate nelle query di log di più risorse con Monitoraggio di Azure](../../azure-monitor/log-query/cross-workspace-query.md) illustra come scrivere query di log che usino dati provenienti da più aree di lavoro di Log Analytics e da applicazioni di Application Insights.


## <a name="examples"></a>Esempi

- [Esempi di query di log in Monitoraggio di Azure](examples.md) fornisce query di esempio usando i dati di log di Monitoraggio di Azure.



## <a name="lessons"></a>Lezioni

- [Uso di stringhe nelle query di log di Monitoraggio di Azure](string-operations.md) illustra come usare i dati contenenti delle stringhe.
- [Uso dei valori di data e ora nelle query di log di Monitoraggio di Azure](datetime-operations.md) descrive come usare i dati di data e ora. 
- [Aggregazioni nelle query di log di Monitoraggio di Azure](aggregations.md) e [Aggregazioni avanzate nelle query di log di Monitoraggio di Azure](advanced-aggregations.md) descrivono come aggregare e riepilogare i dati.
- [Creare un join nelle query di log di Monitoraggio di Azure](joins.md) descrive come creare un join dei dati da più tabelle.
- [Uso di JSON e strutture dei dati nelle query di log di Monitoraggio di Azure](json-data-structures.md) descrive come analizzare i dati JSON.
- [Scrittura di query di log avanzate in Monitoraggio di Azure](advanced-query-writing.md) descrive le strategie per la creazione di query complesse e per riutilizzare il codice.
- [Creazione di grafici e diagrammi da query di log di Monitoraggio di Azure](charts.md) descrive come visualizzare i dati da una query di log.

## <a name="cheatsheets"></a>Schede di riferimento rapido

-  [Query di log da SQL a Monitoraggio di Azure ](sql-cheatsheet.md) assiste gli utenti che hanno già familiarità con SQL.
-  [Query di log da Splunk a di Monitoraggio di Azure](sql-cheatsheet.md) assiste gli utenti che hanno già familiarità con Splunk.
 
## <a name="next-steps"></a>Passaggi successivi

- Accedere alla [documentazione di riferimento completa per il linguaggio di query Kusto](/azure/kusto/query/).