---
title: Linguaggio di query in Log Analytics di Monitoraggio di Azure | Microsoft Docs
description: Riferimenti alle risorse per l'apprendimento di come scrivere query in Log Analytics.
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
ms.date: 10/29/2018
ms.author: bwren
ms.openlocfilehash: 32e64ce7772d562ea34a0d74afbd737be27d247d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968869"
---
# <a name="log-analytics-query-language"></a>Linguaggio di query di Log Analytics
Log Analytics fornisce la raccolta di log e l'analisi per monitoraggio di Azure. È basato su Esplora dati di Azure e usa una versione dello stesso linguaggio di query. La [documentazione del linguaggio di query di Esplora dati di Azure](/azure/kusto/query) contiene tutti i dettagli del linguaggio e deve essere la risorsa principale per la scrittura di query di Log Analytics. Questa pagina fornisce collegamenti ad altre risorse per apprendere come scrivere le query e sulle differenze con l'implementazione del linguaggio di Log Analytics.

## <a name="getting-started"></a>Introduzione

- [Introduzione a Log Analytics nel portale di Azure](get-started-portal.md) è una lezione per la scrittura di query e l'utilizzo con i risultati nel portale di Azure.
-  [Introduzione alle query in Log Analytics](get-started-queries.md) è una lezione per la scrittura di query utilizzando i dati di Log Analytics.

## <a name="concepts"></a>Concetti
- [Analizzare i dati di Log Analytics in Monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md) offre una breve panoramica delle query di log e viene descritta la struttura dei dati di Log Analytics.
- In [Visualizzazione e analisi dei dati in Log Analytics](../../azure-monitor/log-query/portals.md) vengono illustrati i portali in cui vengono create ed eseguite query di Log Analytics.

## <a name="reference"></a>riferimento

- [Riferimento al linguaggio di query](/azure/kusto/query) è il riferimento completo al linguaggio per il linguaggio di query di Esplora dati.
- In [Differenze del linguaggio di query di Log Analytics](data-explorer-difference.md) vengono descritte le differenze tra le versioni del linguaggio di query di Esplora dati.
- In [Proprietà standard nei record di Log Analytics](../../azure-monitor/platform/log-standard-properties.md) vengono descritte le proprietà standard per tutti i dati di Log Analytics.
- In [Eseguire ricerche nei log di più risorse con Log Analytics](../../azure-monitor/log-query/cross-workspace-query.md) viene descritto come scrivere query che utilizzano dati provenienti da più aree di lavoro di Log Analytics e le applicazioni di Application Insights.


## <a name="examples"></a>Esempi

- [Esempi di query in Log Analytics](examples.md) fornisce query di esempio usando i dati di Log Analytics.



## <a name="lessons"></a>Lezioni

- In [Uso di stringhe nelle query di Log Analytics](string-operations.md) viene descritto come usare i dati di tipo stringa.
- In [Uso dei valori di data e ora nelle query di Azure Log Analytics](datetime-operations.md) viene descritto come usare i dati di data e ora. 
- In [Aggregazioni nelle query di Log Analytics](aggregations.md) e [Aggregazioni avanzate nelle query di Log Analytics](advanced-aggregations.md) viene descritto come aggregare e riepilogare i dati.
- In [Creare un join nelle query di Log Analytics](joins.md) viene descritto come creare un join dei dati da più tabelle.
- In [Uso di JSON e strutture dei dati nelle query di Log Analytics](json-data-structures.md) viene descritto come analizzare i dati json.
- In [Scrittura di query avanzate in Log Analytics](advanced-query-writing.md) vengono descritte le strategie per la creazione di query complesse e riutilizzo del codice.
- In [Creazione di grafici e diagrammi da query di Log Analytics](charts.md) viene descritto come visualizzare la data da una query.

## <a name="cheatsheets"></a>Schede di riferimento rapido

-  [Informazioni di riepilogo per il linguaggio di query di Log Analytics](sql-cheatsheet.md) assiste gli utenti che hanno già familiarità con SQL.
-  [Splunk in informazioni di riepilogo per il linguaggio di query di Log Analytics](sql-cheatsheet.md) assiste gli utenti che hanno già familiarità con SQL.
 
## <a name="next-steps"></a>Passaggi successivi

- Accedere alla [documentazione di riferimento completa per il linguaggio di query di Esplora dati](/azure/kusto/query/).