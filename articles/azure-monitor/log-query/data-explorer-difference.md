---
title: Differenze nel linguaggio di query di log di Monitoraggio di Azure | Microsoft Docs
description: Informazioni di riferimento per il linguaggio di query Kusto usato da Monitoraggio di Azure. Include elementi aggiuntivi specifici di Monitoraggio di Azure e gli elementi non supportati nelle query di log di Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: bwren
ms.openlocfilehash: 9892b8884d901033a80fae4168f25da97edb5a68
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990173"
---
# <a name="azure-monitor-log-query-language-differences"></a>Differenze nel linguaggio di query di log di Monitoraggio di Azure

Sebbene i log di [Monitoraggio di Azure](log-query-overview.md) si basino su [Esplora dati di Azure](/azure/data-explorer) e usino lo [stesso linguaggio Kusto](/azure/kusto/query), la versione del linguaggio presenta alcune differenze. Questo articolo identifica gli elementi che risultano diversi tra la versione del linguaggio usata per Esplora dati e la versione usata per le query di log di Monitoraggio di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementi KQL non supportati in Monitoraggio di Azure
Le sezioni seguenti descrivono gli elementi del linguaggio di query Kusto che non sono supportati da Monitoraggio di Azure.

### <a name="statements-not-supported-in-azure-monitor"></a>Istruzioni non supportate in Monitoraggio di Azure

* [Alias](/azure/kusto/query/aliasstatement)
* [Parametri di query](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funzioni non supportate in Monitoraggio di Azure

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operatori non supportati in Monitoraggio di Azure

* [Join tra cluster](/azure/kusto/query/joincrosscluster)
* [Operatore externaldata](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plug-in non supportati in Monitoraggio di Azure

* [Plug-in Python](/azure/kusto/query/pythonplugin)
* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Operatori aggiuntivi in Monitoraggio di Azure
Gli operatori seguenti supportano specifiche funzionalità di Monitoraggio di Azure e non sono disponibili all'esterno di Monitoraggio di Azure.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Passaggi successivi

- Ottenere i riferimenti a diverse [risorse per la scrittura di query di log di Monitoraggio di Azure](query-language.md).
- Accedere alla [documentazione di riferimento completa per il linguaggio di query Kusto](/azure/kusto/query/).
