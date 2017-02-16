---
title: Tenere traccia dei messaggi B2B nel portale di OMS | Documentazione Microsoft
description: Come tenere traccia dei messaggi B2B nel portale di OMS
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
ms.sourcegitcommit: 53195091ac4b93ed94f432990c84c407615fc03e
ms.openlocfilehash: 9c3855c7fce5a9f38424f0bb6cd03f7a2c8d36be


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
* Un account di integrazione. È possibile creare un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e abilitare la registrazione. La procedura è disponibile [qui](logic-apps-monitor-b2b-message.md).
* Un'app per la logica. È possibile creare un'[app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) e abilitare la registrazione. La procedura è disponibile [qui](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>Aggiunta di soluzioni B2B per App per la logica al portale di OMS

1. Selezionare **Altri servizi** nel portale, cercare **Log Analytics** e selezionare **Log Analytics**   
![Ricerca in Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Selezionare **Log Analytics**  
![Selezionare Log Analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Selezionare **Portale OMS** per aprire la home page del portale OMS   
![Esplorare il portale OMS](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Selezionare **Raccolta soluzioni**    
![Selezionare Raccolta soluzioni](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Selezionare **App per la logica B2B**     
![Selezionare App per la logica B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Fare clic su **Aggiungi** per aggiungere i **Messaggi per le app per la logica B2B** alla home page  
![Selezionare Aggiungi](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. Esplorare la home page per visualizzare i **Messaggi per le app per la logica B2B**   
![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="tracking-data-in-oms-portal"></a>Dati di rilevamento nel portale OMS

1.Post elaborazione dei messaggi; la home page viene aggiornata con il conteggio dei messaggi   
![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2.Selezionando **Messaggi per le app per la logica B2B** nella home page, viene visualizzato lo stato dei messaggi AS2 e X12.  I dati sono relativi all'ultimo giorno.
![Selezionare Messaggi per le app per la logica B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)



3. Selezionando i messaggi AS2 o X12 in base allo stato, viene visualizzato l'elenco di messaggi   
![Selezionare lo stato dei messaggi AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

| Proprietà | Descrizione |
| --- | --- |
| Mittente | Il partner guest configurato nelle impostazioni di ricezione o il partner host configurato nelle impostazioni di invio di un contratto AS2 |
| Ricevitore | Il partner host configurato nelle impostazioni di ricezione o il partner guest configurato nelle impostazioni di invio di un contratto AS2 |
| App per la logica | App per la logica in cui sono configurate le azioni AS2 |
| Stato | Lo stato dei messaggi AS2. Operazione completata = Ricevuto o inviato un messaggio AS2 valido, nessun MDN configurato; Operazione completata = Ricevuto o inviato un messaggio AS2 valido, MDN configurato e MDN ricevuto o inviato; Operazione non riuscita = Ricevuto un messaggio AS2 non valido, nessun MDN configurato; In sospeso = Ricevuto o inviato un messaggio AS2 valido, MDN; configurato e atteso ack funzionale; |
| Ack | Lo stato dei messaggi MDN |
| Direzione | Direzione dei messaggi AS2 |
| ID correlazione | ID per correlare tutti i trigger e le azioni all'interno di un'app per la logica |
| ID del messaggio |  ID del messaggio AS2, dalle intestazioni del messaggio AS2 |
| Timestamp | Ora in cui l'azione AS2 elabora il messaggio |
|  |  |


![Selezionare lo stato dei messaggi X12](media/logic-apps-track-b2b-messages-omsportal/x12messagelist.png)

| Proprietà | Descrizione |
| --- | --- |
| Mittente | Il partner guest configurato nelle impostazioni di ricezione o il partner host configurato nelle impostazioni di invio di un contratto AS2 |
| Ricevitore | Il partner host configurato nelle impostazioni di ricezione o il partner guest configurato nelle impostazioni di invio di un contratto AS2 |
| App per la logica | App per la logica in cui sono configurate le azioni AS2 |
| Stato | Lo stato dei messaggi X12. Operazione completata = Ricevuto o inviato un messaggio X12 valido, nessun ack funzionale configurato; Operazione completata = Ricevuto o inviato un messaggio X12 valido, ack funzionale configurato e ack funzionale inviato o ricevuto; Operazione non riuscita = Ricevuto o inviato un messaggio X12 non valido; In sospeso: Ricevuto o inviato un messaggio X12 valido, ack funzionale configurato e atteso ack funzionale |
| Ack | Stato Ack funzionali (997).  Accettato = Ricevuto o inviato ack funzionale positivo; Rifiutato = Ricevuto o inviato ack funzionale negativo; In sospeso = Atteso ack funzionale ma non ricevuto; In sospeso = Generato un ack funzionale; impossibile inviarlo al partner |
| Direzione | Direzione dei messaggi X12 |
| ID correlazione | ID per correlare tutti i trigger e le azioni all'interno di un'app per la logica |
| Tipo di messaggio |  Tipo di messaggio EDI&12; X |
| ICN | Numero di controllo interscambio del messaggio X12 |
| TSCN | Numero di controllo set transazionale del messaggio X12 |
| Timestamp | Ora in cui l'azione X12 elabora il messaggio |
| | |

4.Selezionare una riga nell'elenco dei messaggi X12 o AS2 per accedere alla ricerca nei log.  La ricerca nei log elenca tutte le azioni con lo stesso **ID esecuzione**
![Selezionare lo stato dei messaggi](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="queries-in-oms-portal"></a>Query nel portale OMS

Nella pagina di ricerca è possibile creare una query e quindi, durante la ricerca, filtrare i risultati usando controlli facet.

### <a name="how-to-create-a-query"></a>Come creare una query

1. Nella ricerca di log, scrivere una query e selezionare **Salva**.  [Qui](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md) vengono descritti i passaggi per scrivere una query ![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. Si apre **Salva ricerca**.  Indicare un **nome** e una **categoria**, quindi fare clic su **Salva**   
![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Per visualizzare la query, selezionare i **preferiti**    
![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="how-to-use-a-saved-query"></a>Come usare una query salvata

* Nella ricerca dei log, selezionare i **preferiti** per visualizzare le query salvate.  Selezionando uno dei preferiti si ottengono i risultati della query ![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Passaggi successivi
[Schema di rilevamento personalizzato](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[Schema di rilevamento AS2](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[Schema di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Jan17_HO4-->


