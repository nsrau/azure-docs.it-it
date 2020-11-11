---
title: Eseguire query di log in monitoraggio di Azure
description: Informazioni di riferimento per il linguaggio di query Kusto usato da Monitoraggio di Azure. Include elementi aggiuntivi specifici di Monitoraggio di Azure e gli elementi non supportati nelle query di log di Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 6174bcbe5a014cff8dbd8dff242880d7f0ef7aa0
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491393"
---
# <a name="log-queries-in-azure-monitor"></a>Eseguire query di log in monitoraggio di Azure
I log di monitoraggio di Azure si basano su Esplora dati di Azure e le query di log vengono scritte usando lo stesso linguaggio di query kusto (KQL). Si tratta di un linguaggio avanzato progettato per essere facile da leggere e creare, quindi è possibile iniziare a scrivere query con alcune linee guida di base.

Le aree di monitoraggio di Azure in cui si utilizzeranno le query sono le seguenti:

- [Log Analytics](../log-query/log-analytics-overview.md). Lo strumento principale nella portale di Azure per la modifica delle query di log e l'analisi interattiva dei risultati. Anche se si prevede di usare una query di log in un altro punto del monitoraggio di Azure, in genere si scrive e si testa il file in Log Analytics prima di copiarlo nel percorso finale.
- [Regole di avviso del log](../platform/alerts-overview.md). Identificare in modo proattivo i problemi dai dati nell'area di lavoro.  Ogni regola di avviso si basa su una query di log che viene eseguita automaticamente a intervalli regolari.  I risultati vengono esaminati per determinare se è necessario creare un avviso.
- [Cartelle di lavoro](../platform/workbooks-overview.md)di. Includere i risultati delle query di log usando visualizzazioni diverse nei report visivi interattivi nel portale di Azure.
- [Dashboard di Azure](../learn/tutorial-logs-dashboards.md). Aggiungere i risultati di una query in un dashboard di Azure che consente di visualizzare i dati di log e metriche insieme e, facoltativamente, di condividerli con altri utenti di Azure.
- [App](../platform/logicapp-flow-connector.md)per la logica.  Usare i risultati di una query di log in un flusso di lavoro automatizzato usando app per la logica.
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult). Usare i risultati di una query di log in uno script di PowerShell da una riga di comando o da un Runbook di automazione di Azure che usa Get-AzOperationalInsightsSearchResults.
- [API dei log di monitoraggio di Azure](https://dev.loganalytics.io). Recuperare i dati di log dall'area di lavoro da qualsiasi client dell'API REST.  La richiesta dell'API include una query eseguita su Monitoraggio di Azure per determinare i dati da recuperare.

## <a name="getting-started"></a>Introduzione
Il modo migliore per iniziare a imparare a scrivere query di log con KQL è usare le esercitazioni e gli esempi disponibili.

- [Log Analytics esercitazione](log-analytics-tutorial.md) : esercitazione sull'uso delle funzionalità di log Analytics, che è lo strumento che verrà usato nella portale di Azure per modificare ed eseguire le query. Consente inoltre di scrivere semplici query senza utilizzare direttamente il linguaggio di query. Se non si è mai usato Log Analytics prima, iniziare da qui per comprendere lo strumento da usare con le altre esercitazioni ed esempi.
- [Esercitazione di KQL](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) -Guida dettagliata sui concetti di base di KQL e sugli operatori comuni. Questo è il posto migliore in cui iniziare ad accelerare il linguaggio e la struttura delle query di log. 
- [Query di esempio](example-queries.md) : Descrizione delle query di esempio disponibili in log Analytics. È possibile usare le query senza modifiche o usarle come esempi per apprendere i KQL.
- [Esempi di query](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) : query di esempio che illustrano una varietà di concetti diversi.



## <a name="reference-documentation"></a>Documentazione di riferimento
La [documentazione per KQL](/azure/data-explorer/kusto/query/) , incluso il riferimento per tutti i comandi e gli operatori, è disponibile nella documentazione di Azure Esplora dati. Anche se si ha familiarità con KQL, si userà regolarmente il riferimento per esaminare i nuovi comandi e scenari che non sono stati usati in precedenza.


## <a name="language-differences"></a>Differenze di lingua
Mentre monitoraggio di Azure usa lo stesso KQL di Azure Esplora dati, esistono alcune differenze. Nella documentazione di KQL verranno specificati gli operatori non supportati da monitoraggio di Azure o con funzionalità diverse. Gli operatori specifici di monitoraggio di Azure sono documentati nel contenuto di monitoraggio di Azure. Nelle sezioni seguenti viene fornito un elenco delle differenze tra le versioni del linguaggio per riferimento rapido.

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

### <a name="plugins-not-supported-in-azure-monitor"></a>Plug-in non supportati in Monitoraggio di Azure

* [Plug-in Python](/azure/kusto/query/pythonplugin)
* [Plug-in sql_request](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Operatori aggiuntivi in Monitoraggio di Azure
Gli operatori seguenti supportano specifiche funzionalità di Monitoraggio di Azure e non sono disponibili all'esterno di Monitoraggio di Azure.

* [app ()](app-expression.md)
* [risorsa ()](resource-expression.md)
* [area di lavoro ()](workspace-expression.md)

## <a name="next-steps"></a>Passaggi successivi
- Seguire un'[esercitazione sulla scrittura di query](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor).
- Accedere alla [documentazione di riferimento completa per il linguaggio di query Kusto](/azure/kusto/query/).

