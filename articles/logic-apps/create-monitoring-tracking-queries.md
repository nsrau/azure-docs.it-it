---
title: Visualizzare e creare query per le app per la logica nei log di Monitoraggio di AzureView and create queries for logic apps in Azure Monitor logs
description: Visualizzare e creare query nei log di Monitoraggio di Azure per le app per la logica di AzureView and create queries in Azure Monitor logs for Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908072"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Visualizzare e creare query per il monitoraggio e il rilevamento nei log di Monitoraggio di Azure per le app per la logica di AzureView and create queries for monitoring and tracking in Azure Monitor logs for Azure Logic Apps

È possibile visualizzare le query sottostanti che producono i risultati dai log di [Monitoraggio di Azure](../log-analytics/log-analytics-overview.md) e creare query che filtrano i risultati in base ai criteri specifici. Ad esempio, è possibile trovare i messaggi in base a un numero di controllo interscambio specifico. Le query utilizzano il linguaggio di [query Kusto](https://aka.ms/LogAnalyticsLanguageReference), che è possibile modificare se si desidera visualizzare risultati diversi. Per altre informazioni, vedere Query di [log di Monitoraggio di Azure.For](../azure-monitor/log-query/query-language.md)more information, see Azure Monitor log queries .

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro Log Analytics. Se non si dispone di un'area di lavoro Log Analytics, vedere [Creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Un'app per la logica configurata con la registrazione di Monitoraggio di Azure e che invia tali informazioni a un'area di lavoro di Log Analytics.A logic app that's set up with Azure Monitor logging and sends that information to a Log Analytics workspace. Informazioni su come configurare i log di Monitoraggio di [Azure per l'app per la logica.](../logic-apps/monitor-logic-apps.md)

* Se si usa un account di integrazione, assicurarsi di aver configurato l'account con la registrazione di Monitoraggio di Azure per inviare tali informazioni a un'area di lavoro di Log Analytics.If you're using an integration account, make sure that you've set up the account with Azure Monitor logging to send that information to a Log Analytics workspace. Informazioni su come configurare la registrazione di [Monitoraggio di Azure per l'account di integrazione.](../logic-apps/monitor-b2b-messages-log-analytics.md)

## <a name="view-queries-behind-results"></a>Visualizzare le query dietro i risultati

Per visualizzare o modificare la query che produce i risultati nel riepilogo dell'area di lavoro, attenersi alla seguente procedura:

1. In qualsiasi pagina dei risultati, nella parte inferiore, selezionare **Visualizza tutto**.

   ![Vedi tutti i risultati](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Viene visualizzata la pagina Registri con la query che si basa sulla pagina dei risultati precedente.

   ![Pagina Registri - Visualizzazione query](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Nella pagina **Registri** è possibile selezionare le opzioni seguenti:

   * Per visualizzare i risultati della query sotto l'editor di query selezionare **Tabella**.

   * Per modificare la query, aggiornare la stringa di query e selezionare **Esegui** per visualizzare i risultati nella tabella.

## <a name="create-your-own-query"></a>Creare una query personalizzata

Per trovare o filtrare i risultati in base a proprietà o valori specifici, è possibile creare una query personalizzata iniziando da una query vuota o utilizzando una query esistente. Per altre informazioni, vedere Introduzione alle query di log in Monitoraggio di Azure.For more information, see [Get started with log queries in Azure Monitor](../azure-monitor/log-query/get-started-queries.md).

1. Nel portale di Azure individuare e selezionare l'area di lavoro Log Analytics.In the [Azure portal,](https://portal.azure.com)find and select your Log Analytics workspace.

1. Nel menu dell'area di lavoro, in **Generale**, selezionare **Registri**.

1. Iniziare da una query vuota o da qualsiasi query esistente disponibile.

   * Per verificare se sono disponibili query esistenti, nella barra degli strumenti della query selezionare**Cronologia** **query** > samples , che mostra le query delle esecuzioni di query precedenti, oppure Esplora **query**, che mostra le query predefinite.

     Ad esempio, la soluzione B2B App per la logica fornisce le query predefinite:For example, the Logic Apps B2B solution provides these prebuilt queries:

     ![Iniziare con le query predefinite della soluzione "App per la logica B2B"](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Per iniziare da una query vuota, nell'editor di query iniziare a digitare il linguaggio di [query Kusto](../azure-monitor/log-query/query-language.md) per la query.

     ![Inizia con una query vuota](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Passaggi successivi

* [Schemi di rilevamento AS2AS2 tracking schemas](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemi di rilevamento X12X12 tracking schemas](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schemi di rilevamento personalizzati](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)