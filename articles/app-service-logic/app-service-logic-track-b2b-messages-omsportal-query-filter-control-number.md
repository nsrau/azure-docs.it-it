---
title: Tenere traccia dei messaggi B2B nel portale di OMS | Documentazione Microsoft
description: Come tenere traccia dei messaggi B2B nel portale di OMS
author: padmavc
manager: erikre
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
ms.sourcegitcommit: 6e56dae0ab30612a7794bd39e6eba2f84bcecc19
ms.openlocfilehash: eae37a357e3e1c00310c7c638d3164118403ee9f


---
# <a name="create-a-query-in-oms-portal"></a>Creare una query nel portale di OMS 
Passaggi per creare una query che consente di filtrare i dati relativi a uno specifico numero di controllo interscambio

## <a name="prerequisites"></a>Prerequisiti

Abilitare la diagnostica per il proprio [account di integrazione](app-service-logic-monitor-b2b-message.md), [app per la logica](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) che dispongono di 12 connettori per il debug e i dettagli più completi.  Seguire i passaggi descritti [qui](app-service-logic-track-b2b-messages-omsportal.md) per pubblicare i dati di diagnostica sul portale di OMS.

## <a name="create-a-query-to-search-data-for-a-specific-interchange-control-number"></a>Creare una query per cercare i dati relativi a uno specifico numero di controllo interscambio

1. Selezione di **ricerca log** nella home page  
![Selezionare Ricerca log](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. Digitare **Type="AzureDiagnostics"** nella finestra di ricerca ed estrarre tutti i dati.  Fare clic su **Aggiungi** per filtrare i dati  
![Selezionare query](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Digitare **interscambio**, selezionare **event_record_messageProperties_interchangeControlNumber_s** e fare clic su **Aggiungi**  
![Selezionare numero di controllo](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Selezionare il numero di controllo prescelto per filtrare i dati e fare clic su **Applica**  
![Selezionare numero di controllo](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. È possibile trovare la query creata per filtrare i dati per il numero di controllo selezionato   
![Selezionare numero di controllo](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Attribuire un nome alla query e salvarla   
![Selezionare numero di controllo](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png) 

7. Selezionare i **preferiti** per visualizzare la query e usarla nelle ricerche future  
![Selezionare numero di controllo](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. La query può essere aggiornata per cercare un nuovo numero di controllo interscambio  
![Selezionare numero di controllo](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png) 


## <a name="next-steps"></a>Passaggi successivi
[Schema di rilevamento personalizzato](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[Schema di rilevamento AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[Schema di rilevamento X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[Altre informazioni su Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


