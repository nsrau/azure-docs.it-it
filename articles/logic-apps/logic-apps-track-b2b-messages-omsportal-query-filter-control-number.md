---
title: Query per i messaggi B2B in Operations Management Suite - App per la logica di Azure| Microsoft Docs
description: Creare query per tenere traccia dei messaggi AS2, X12 ed EDIFACT in Operations Management Suite
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 2748d3d3daf7c13dca05f663a4a088598e1b3605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="query-for-as2-x12-and-edifact-messages-in-the-microsoft-operations-management-suite-oms"></a>Query per i messaggi AS2, X12 ed EDIFACT in Microsoft Operations Management Suite, ovvero OMS

Per trovare i messaggi AS2, X12 o EDIFACT che si desidera monitorare con [Azure Log Analytics](../log-analytics/log-analytics-overview.md) in [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), è possibile creare query per filtrare le azioni in base a criteri specifici. Ad esempio, è possibile trovare i messaggi in base a un numero di controllo interscambio specifico.

## <a name="requirements"></a>Requisiti

* Un'app per la logica configurata con la registrazione diagnostica. Informazioni su [come creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) e [come configurare la registrazione per tale app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Un account di integrazione configurato con il monitoraggio e la registrazione. Informazioni su [come creare un account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [come configurare il monitoraggio e la registrazione per tale account](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se non è già stato fatto, [pubblicare i dati di diagnostica in Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) e [configurare la verifica dei messaggi in OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Dopo avere soddisfatto i requisiti precedenti, sarà presente un'area di lavoro in [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). È consigliabile usare la stessa area di lavoro di OMS per tenere traccia delle comunicazioni B2B in OMS. 
>  
> Se non si ha un'area di lavoro OMS, vedere [come crearne una](../log-analytics/log-analytics-get-started.md).

## <a name="create-message-queries-with-filters-in-the-operations-management-suite-portal"></a>Creare query per i messaggi con i filtri nel portale di Operations Management Suite

Questo esempio illustra come trovare i messaggi in base a un numero di controllo interscambio.

> [!TIP] 
> Se si conosce il nome dell'area di lavoro OMS, passare alla home page dell'area di lavoro (`https://{your-workspace-name}.portal.mms.microsoft.com`) e iniziare dal passaggio 4. Altrimenti iniziare dal passaggio 1.

1. Nel [portale di Azure](https://portal.azure.com) scegliere **Altri servizi**. Cercare "Log Analytics" e quindi scegliere **Log Analytics**, come illustrato qui:

   ![Trovare Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. In **Log Analytics** trovare e selezionare l'area di lavoro di OMS.

   ![Selezionare l'area di lavoro di OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. In **Gestione** scegliere **Portale di OMS**.

   ![Scegliere Portale di OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. Nella home page di OMS scegliere **Ricerca log**.

   ![Nella home page di OMS scegliere "Ricerca log"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -oppure-

   ![Dal menu di OMS scegliere "Ricerca log"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. Nella casella di ricerca inserire il campo che si vuole trovare e premere **Invio**. Quando si inizia a digitare, OMS mostra le corrispondenze e operazioni che è possibile usare. Altre informazioni su [come trovare i dati in Log Analytics](../log-analytics/log-analytics-log-searches.md).

   Questo esempio cerca gli eventi con **Type=AzureDiagnostics**.

   ![Iniziare a digitare una stringa di query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. Nella barra a sinistra scegliere l'intervallo di tempo che si vuole visualizzare. Per aggiungere un filtro alla query, scegliere **+Aggiungi**.

   ![Aggiungere un filtro alla query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. In **Aggiungi filtri** immettere il nome del filtro per trovare quello desiderato. Selezionare il filtro e scegliere **+Aggiungi**.

   Per trovare il numero di controllo interscambio, questo esempio cerca la parola "interscambio" e seleziona **event_record_messageProperties_interchangeControlNumber_s** come filtro.

   ![Selezionare il filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. Nella barra a sinistra selezionare il valore del filtro che si vuole usare e scegliere **Applica**.

   Questo esempio consente di selezionare il numero di controllo interscambio per i messaggi desiderati.

   ![Selezionare il valore del filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. Tornare ora alla query che si sta creando. La query viene aggiornata con l'evento e il valore del filtro selezionati. Vengono ora filtrati anche i risultati precedenti.

    ![Tornare alla query con i risultati filtrati](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Per salvare la query per un uso futuro

1. Dalla query nella pagina **Ricerca log** scegliere **Salva**. Assegnare un nome alla query, selezionare una categoria e scegliere **Salva**.

   ![Assegnare un nome e una categoria alla query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Per visualizzare la query, scegliere **Preferiti**.

   ![Scegliere "Preferiti"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. In **Ricerche salvate** selezionare la query per visualizzare i risultati. Per aggiornare la query e trovare risultati diversi, modificare la query.

   ![Selezionare la query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-the-operations-management-suite-portal"></a>Trovare ed eseguire le query nel portale di Operations Management Suite

1. Aprire la home page dell'area di lavoro OMS (`https://{your-workspace-name}.portal.mms.microsoft.com`) e scegliere **Ricerca log**.

   ![Nella home page di OMS scegliere "Ricerca log"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -oppure-

   ![Dal menu di OMS scegliere "Ricerca log"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. Nella home page **Ricerca log** scegliere **Preferiti**.

   ![Scegliere "Preferiti"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. In **Ricerche salvate** selezionare la query per visualizzare i risultati. Per aggiornare la query e trovare risultati diversi, modificare la query.

   ![Selezionare la query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Passaggi successivi

* [Schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemi di rilevamento X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schemi di rilevamento personalizzati](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)