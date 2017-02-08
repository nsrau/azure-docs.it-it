---
title: Tenere traccia dei messaggi B2B nel portale Operations Management Suite utilizzando una query | Documentazione Microsoft
description: Informazioni su come tenere traccia dei messaggi B2B nel portale di Operations Management Suite tramite una query.
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
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: df6beaaa5de45acfa850049721797fc43f4c9ed7


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>Tenere traccia dei messaggi B2B nel portale di Operations Management Suite utilizzando una query
Per tenere traccia dei messaggi di business-to-business (B2B) nel portale di Operations Management Suite, è possibile creare una query che filtra i dati per uno specifico numero di controllo interscambio.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire il debug e per ottenere informazioni di diagnostica più dettagliate, attivare la diagnostica nell'[account di integrazione](logic-apps-monitor-b2b-message.md) per le [app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) che dispongono di connettori X12. Eseguire quindi i passaggi per [pubblicare dati di diagnostica](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) nel portale di Operations Management Suite.

## <a name="to-create-a-query-to-search-for-a-specific-interchange-control-number"></a>Creare una query per cercare uno specifico numero di controllo interscambio

1. Nella pagina iniziale selezionare **Ricerca log**.  
![Selezionare Ricerca log](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. Nella casella di ricerca digitare **Type="AzureDiagnostics"**. Per filtrare i dati, selezionare **Aggiungi**.  
![Selezionare query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Digitare **interscambio**, selezionare **event_record_messageProperties_interchangeControlNumber_s** e quindi selezionare **Aggiungi**.  
![Aggiungere un filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Selezionare il numero di controllo prescelto per filtrare i dati e fare clic su **Applica**.  
![Cercare il numero di controllo](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Individuare la query creata per filtrare i dati per il numero di controllo selezionato.   
![Individuare la query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Digitare un nome per la query, quindi salvarlo.   
![Salvare la query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. Per visualizzare la query e utilizzarla in ricerche future, selezionare **Preferiti**.  
![Visualizzare la query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. È possibile aggiornare la query per cercare un nuovo numero di controllo interscambio.  
![Aggiornare la query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sugli [schemi di rilevamento personalizzati](logic-apps-track-integration-account-custom-tracking-schema.md).   
* Altre informazioni sugli [schemi di rilevamento AS2](logic-apps-track-integration-account-as2-tracking-schemas.md).    
* Altre informazioni sugli [schemi di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md).  
* Altre informazioni su [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).



<!--HONumber=Jan17_HO3-->


