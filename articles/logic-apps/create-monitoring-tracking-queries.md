---
title: Visualizzare e creare query per app per la logica nei log di monitoraggio di Azure
description: Visualizzare e creare query nei log di monitoraggio di Azure per app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76908072"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Visualizzare e creare query per il monitoraggio e il rilevamento nei log di monitoraggio di Azure per app per la logica di Azure

È possibile visualizzare le query sottostanti che producono i risultati dei [log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) e creare query che filtrano i risultati in base a criteri specifici. Ad esempio, è possibile trovare i messaggi in base a un numero di controllo interscambio specifico. Le query usano il [linguaggio di query kusto](https://aka.ms/LogAnalyticsLanguageReference), che è possibile modificare se si desidera visualizzare risultati diversi. Per altre informazioni, vedere [query di log di monitoraggio di Azure](../azure-monitor/log-query/query-language.md).

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro Log Analytics. Se non si dispone di un'area di lavoro Log Analytics, vedere [Creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Un'app per la logica configurata con la registrazione di monitoraggio di Azure e invia tali informazioni a un'area di lavoro Log Analytics. Informazioni su [come configurare i log di monitoraggio di Azure per l'app per la logica](../logic-apps/monitor-logic-apps.md).

* Se si usa un account di integrazione, verificare di aver configurato l'account con la registrazione di monitoraggio di Azure per inviare tali informazioni a un'area di lavoro Log Analytics. Informazioni su come [configurare la registrazione di monitoraggio di Azure per l'account di integrazione](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Visualizza query dietro i risultati

Per visualizzare o modificare la query che produce i risultati nel riepilogo dell'area di lavoro, attenersi alla seguente procedura:

1. Nella parte inferiore della pagina dei risultati selezionare **Visualizza tutto**.

   ![Visualizza tutti i risultati](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Viene visualizzata la pagina log in cui è riportata la query che si trova dietro la pagina risultati precedenti.

   ![Pagina log-visualizzazione query](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Nella pagina **logs** è possibile selezionare le opzioni seguenti:

   * Per visualizzare i risultati della query sotto forma di tabella, nell'editor di query selezionare **tabella**.

   * Per modificare la query, aggiornare la stringa di query e selezionare **Esegui** per visualizzare i risultati nella tabella.

## <a name="create-your-own-query"></a>Creare una query personalizzata

Per trovare o filtrare i risultati in base a proprietà o valori specifici, è possibile creare una query personalizzata iniziando da una query vuota o utilizzando una query esistente. Per altre informazioni, vedere [Introduzione alle query di log in monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md).

1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare l'area di lavoro log Analytics.

1. Nel menu dell'area di lavoro in **generale**selezionare **log**.

1. Iniziare da una query vuota o da eventuali query esistenti disponibili.

   * Per verificare se sono disponibili query esistenti, sulla barra degli strumenti query, selezionare **esempi**  >  di**cronologia**delle query, che mostra le query delle esecuzioni precedenti della query, oppure selezionare **Esplora query**, che mostra le query predefinite.

     Ad esempio, la soluzione App per la logica B2B fornisce le query predefinite seguenti:

     ![Inizia con le query predefinite della soluzione "App per la logica B2B"](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Per iniziare da una query vuota, nell'editor di query iniziare a digitare il [linguaggio di query kusto](../azure-monitor/log-query/query-language.md) per la query.

     ![Inizia con una query vuota](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Passaggi successivi

* [Schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemi di rilevamento X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schemi di rilevamento personalizzati](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)