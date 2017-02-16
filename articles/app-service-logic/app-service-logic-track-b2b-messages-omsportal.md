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
ms.sourcegitcommit: 7f64fe625b7c3b7e58f86154b9d601eff03e31d3
ms.openlocfilehash: 854db453a3af459c0398c4b88a6789c87747e920


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>Rilevamento dei messaggi B2B nel portale di OMS
La comunicazione B2B comporta lo scambio di messaggi tra due processi o applicazioni aziendali in esecuzione. Il rilevamento dei messaggi B2B nel portale di OMS offre funzionalità avanzate basate sul Web che consentono di visualizzare se i messaggi sono stati elaborati correttamente.  È possibile tenere traccia di:

* Conteggio e stato dei messaggi
* Stato degli acknowledgment
* Correlazione dei messaggi con gli acknowledgment
* Descrizione dettagliata degli errori
* Funzionalità di ricerca

## <a name="prerequisites"></a>Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un account di integrazione. È possibile creare un [account di integrazione](app-service-logic-enterprise-integration-create-integration-account.md) e abilitare la registrazione. La procedura è disponibile [qui](app-service-logic-monitor-b2b-message.md).
* Un'app per la logica. È possibile creare un'[app per la logica](app-service-logic-create-a-logic-app.md) e abilitare la registrazione. La procedura è disponibile [qui](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>Aggiunta di soluzioni B2B per App per la logica al portale di OMS

1. Selezionare **Altri servizi** nel portale, cercare **Log Analytics** e selezionare **Log Analytics**
![Cercare Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Selezionare **Log Analytics**
![Selezionare Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/selectla.png)

3. Selezionare **Portale di OMS** per aprire la home page del portale di OMS ![Esplorare il portale di OMS](./media/app-service-logic-track-b2b-messages-omsportal/omsportalpage.png)

4. Selezionare **Raccolta soluzioni**    
![Selezionare Raccolta soluzioni](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage1.png)

5. Selezionare **App per la logica B2B**
![Selezionare App per la logica B2B](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage2.png)

6. Fare clic su **Aggiungi** per aggiungere **Messaggi per le app per la logica B2B** alla home page ![Selezionare Aggiungi](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage3.png)

7. Esplorare la home page per visualizzare **Messaggi per le app per la logica B2B**
![Selezionare la home page](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="tracking-data-in-oms-portal"></a>Dati di rilevamento nel portale OMS

1. Pubblicare l'elaborazione messaggio. La home page viene aggiornata con il conteggio dei messaggi ![Selezionare la home page](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage6.png)

2. Selezionando **Messaggi per le app per la logica B2B** nella home page, viene visualizzato lo stato dei messaggi AS2 e X12.  I dati sono relativi all'ultimo giorno.
![Selezionare Messaggi per le app per la logica B2B](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage5.png)

3. Selezionando i messaggi AS2 o X12 in base allo stato, viene visualizzato l'elenco di messaggi ![Selezionare lo stato dei messaggi AS2](./media/app-service-logic-track-b2b-messages-omsportal/as2messagelist.png)

    ![Selezionare lo stato dei messaggi X12](./media/app-service-logic-track-b2b-messages-omsportal/x12messagelist.png)

4. Selezionare una riga nell'elenco dei messaggi X12 o AS2 per accedere alla ricerca nei log.  La ricerca nei log elenca tutte le azioni con lo stesso **ID esecuzione**
![Selezionare lo stato dei messaggi](./media/app-service-logic-track-b2b-messages-omsportal/logsearch.png)

## <a name="queries-in-oms-portal"></a>Query nel portale OMS

Nella pagina di ricerca è possibile creare una query e quindi, durante la ricerca, filtrare i risultati usando controlli facet.

### <a name="how-to-create-a-query"></a>Come creare una query

1. Nella ricerca di log, scrivere una query e selezionare **Salva**.  [Qui](app-service-logic-track-b2b-messages-omsportal-query-filter-control-number.md) vengono descritti i passaggi per scrivere una query ![Seleziona home page](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery.png)

2. Si apre **Salva ricerca**.  Indicare un **nome** e una **categoria**, quindi fare clic su **Salva**   
![Seleziona home page](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Per visualizzare la query, selezionare i **preferiti**    
![Seleziona home page](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Seleziona home page](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="how-to-use-a-saved-query"></a>Come usare una query salvata

1. Nella ricerca dei log, selezionare i **preferiti** per visualizzare le query salvate.  Selezionando uno dei preferiti si ottengono i risultati della query ![Seleziona home page](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Passaggi successivi
[Schema di rilevamento personalizzato](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[Schema di rilevamento AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[Schema di rilevamento X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[Altre informazioni su Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


