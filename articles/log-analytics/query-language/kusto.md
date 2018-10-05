---
title: Informazioni di riferimento sul linguaggio di Log Analytics di Monitoraggio di Azure | Microsoft Docs
description: Informazioni di riferimento sul linguaggio Kusto usato da Log Analytics. Include gli elementi aggiuntivi specifici di Log Analytics e gli elementi non supportati nelle query di Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451069"
---
# <a name="log-analytics-query-language-reference"></a>Informazioni di riferimento sul linguaggio di query di Log Analytics
Le [query di Log Analytics](../log-analytics-queries.md) usano lo stesso linguaggio di query e lo stesso motore usati da [Esplora dati di Azure](/azure/data-explorer/). È possibile accedere alle informazioni di riferimento e ad altri dettagli sul linguaggio dalla posizione seguente: [Informazioni di riferimento sul linguaggio Kusto](/azure/kusto/query)



## <a name="kusto-elements-not-support-in-log-analytics"></a>Elementi di Kusto non supportati in Log Analytics
Anche se le query di Log Analytics usano un'implementazione di Kusto, esistono alcuni elementi di Kusto non supportati, come descritto nelle sezioni seguenti.

### <a name="statements-not-supported-in-log-analytics"></a>Istruzioni non supportate in Log Analytics

* [Alias](/kusto/query/aliasstatement)
* [Parametri di query](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>Funzioni non supportate in Log Analytics

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>Operatori non supportati in Log Analytics

* [Join tra cluster](/azure/kusto/query/joincrosscluster)
* [Operatore externaldata](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Plug-in non supportati in Log Analytics

* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Operatori aggiuntivi in Log Analytics
Per supportare specifiche funzionalità di Log Analytics, vengono forniti gli operatori di Kusto aggiuntivi seguenti che non sono disponibili all'esterno di Log Analytics. 

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle query in [Log Analytics](../log-analytics-queries.md).
- Eseguire una lezione sulla scrittura di una [query di Log Analytics](/log-analytics/query-language/get-started-queries.md).
- Accedere alla [documentazione di riferimento completa per Kusto](/azure/kusto/query/).