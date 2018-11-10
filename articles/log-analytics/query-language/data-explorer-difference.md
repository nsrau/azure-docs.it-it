---
title: Informazioni di riferimento sul linguaggio di Log Analytics di Monitoraggio di Azure | Microsoft Docs
description: Informazioni di riferimento sul linguaggio di query per Esplora dati usato da Log Analytics. Include gli elementi aggiuntivi specifici di Log Analytics e gli elementi non supportati nelle query di Log Analytics.
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
ms.date: 10/31/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 65f3388074ff7a84c6e0516a64665019d5883ab1
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913173"
---
# <a name="log-analytics-query-language-differences"></a>Differenze del linguaggio di query di Log Analytics

Mentre [Log Analytics](../log-analytics-queries.md) è basato su [Esplora dati di Azure](/azure//data-explorer) e usa lo [stesso linguaggio di query](/azure/kusto/query), la versione del linguaggio presenta alcune differenze. Questo articolo identifica gli elementi che risultano diversi tra la versione del linguaggio usata per le query di Esplora dati e la versione usata per quelle di Log Analytics.

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Elementi di Esplora dati non supportati in Log Analytics
Le seguenti sezioni descrivono gli elementi del linguaggio di query di Esplora dati che non sono supportati da Log Analytics.

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
I seguenti operatori supportano specifiche funzionalità di Log Analytics e non sono disponibili all'esterno di Log Analytics.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Passaggi successivi

- Ottenere i riferimenti per diverse risorse [per la scrittura di query di Log Analytics](query-language.md).
- Accedere alla [documentazione di riferimento completa per il linguaggio di query di Esplora dati](/azure/kusto/query/).
