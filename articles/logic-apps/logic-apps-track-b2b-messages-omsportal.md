---
title: Tenere traccia dei messaggi B2B nel portale Operations Management Suite - Azure| Microsoft Docs
description: Informazioni su come tenere traccia dei messaggi B2B nel portale di Operations Management Suite
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
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: d1c61ba220b4334f053428a23e620e8004fc60f7
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal"></a>Tenere traccia dei messaggi B2B nel portale di Operations Management Suite

![Simbolo di App per la logica B2B](./media/logic-apps-track-b2b-messages-omsportal/logic-apps-b2b-symbol.png)

La comunicazione B2B comporta lo scambio di messaggi tra due processi o applicazioni aziendali in esecuzione. Usare le seguenti funzionalità di rilevamento basate su web nel portale di Operations Management Suite per verificare che i messaggi vengano elaborati correttamente:

* Conteggio e stato dei messaggi
* Stato degli acknowledgment
* Correlare i messaggi con i riconoscimenti
* Descrizione dettagliata degli errori
* Funzionalità di ricerca

## <a name="prerequisites"></a>Prerequisiti
* Un account Azure. È possibile creare un [account gratuito](https://azure.microsoft.com/free).
* Un account di integrazione. È possibile creare un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e impostarne la registrazione. Per impostare la registrazione, vedere [Monitorare i messaggi B2B](logic-apps-monitor-b2b-message.md).
* Un'app per la logica. È possibile creare un'[app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) e impostarne la registrazione. Per impostare la registrazione, vedere [Diagnostica e avvisi di Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts).

## <a name="add-logic-apps-b2b-solution-to-the-operations-management-suite-portal"></a>Aggiungere la soluzione B2B di App per la logica al portale di Operations Management Suite

1. Nel Portale di Azure selezionare **Altri servizi**, cercare Log Analytics, quindi selezionare **Log Analytics**.   
![Ricerca in Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Selezionare **Log Analytics**.  
![Selezionare Log Analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Selezionare **OMS Portal** (Portale OMS). Viene visualizzato il portale di Operations Management Suite.   
![Navigare nel portale di Operations Management Suite](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Selezionare **Solutions Gallery** (Raccolta soluzioni).    
![Selezionare Raccolta soluzioni](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Selezionare **Logic Apps B2B** (App per la logica B2B).     
![Selezionare App per la logica B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Fare clic su **Aggiungi** per aggiungere i messaggi di **App per la logica B2B** alla home page.  
![Selezionare Aggiungi](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. **I messaggi di App per la logica B2B** vengono visualizzati nella home page.   
![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="track-data-in-the-operations-management-suite-portal"></a>Tenere traccia dei dati nel portale di Operations Management Suite

1. In seguito all'elaborazione dei messaggi, viene visualizzato il numero di messaggi aggiornato.   
![Messaggi aggiornati](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. Selezionare **Logic Apps B2B Messages** (Messaggi per le app per la logica B2B) nella home page per visualizzare lo stato dei messaggi AS2 e X12.  I dati sono relativi a un solo giorno.
![Selezionare Messaggi per le app per la logica B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. Selezionare un messaggio AS2, X12 o EDIFACT in base allo stato per passare all'elenco dei messaggi. La schermata successiva mostra lo stato dei messaggi AS2. Le descrizioni delle proprietà di stato del messaggio AS2 e X12 vengono visualizzate in seguito in "Descrizioni delle proprietà di elenco dei messaggi".  
![Selezionare lo stato dei messaggi AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

4. Selezionare una riga nell'elenco dei messaggi X12, AS2 o EDIFACT per passare alla ricerca log.  La ricerca nei log elenca tutte le azioni con lo stesso ID di esecuzione.
![Selezionare lo stato dei messaggi](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="message-list-property-descriptions"></a>Descrizioni delle proprietà di elenco di messaggi

#### <a name="as2-message-list-property-descriptions"></a>Descrizioni delle proprietà di elenco di messaggi AS2

| Proprietà | Descrizione |
| --- | --- |
| Mittente | Il partner guest configurato nelle impostazioni di ricezione o il partner host configurato nelle impostazioni di invio di un contratto AS2. |
| Ricevitore | Il partner host configurato nelle impostazioni di ricezione o il partner guest configurato nelle impostazioni di invio di un contratto AS2. |
| App per la logica | App per la logica in cui sono configurate le azioni AS2. |
| Stato | Stato dei messaggi AS2 <br>Operazione riuscita = ricevuto o inviato un messaggio AS2 corretto, nessun MDN configurato <br>Operazione riuscita = ricevuto o inviato un messaggio AS2 corretto, MDN configurato e ricevuto o MDN inviato <br>Operazione non riuscita = ricevuto un messaggio AS2 non corretto, nessun MDN configurato <br>In sospeso = ricevuto o inviato un messaggio AS2 corretto, MDN configurato e previsto |
| Ack | Lo stato dei messaggi MDN <br>Accettato = ricevuto o inviato un messaggio MDN positivo <br>In sospeso = in attesa di ricevere o inviare un messaggio MDN <br>Rifiutato = ricevuto o inviato un messaggio MDN negativo <br>Non richiesto = MDN non configurato nel contratto |
| Direzione | Direzione dei messaggi AS2. |
| ID correlazione | ID per correlare tutti i trigger e le azioni all'interno di un'app per la logica. |
| ID del messaggio |  ID del messaggio AS2, dalle intestazioni del messaggio AS2. |
| Timestamp | Ora in cui l'azione AS2 elabora il messaggio. |

#### <a name="x12-message-list-property-descriptions"></a>Descrizioni delle proprietà di elenco di messaggi X12

| Proprietà | Descrizione |
| --- | --- |
| Mittente | Il partner guest configurato nelle impostazioni di ricezione o il partner host configurato nelle impostazioni di invio di un contratto X12. |
| Ricevitore | Il partner host configurato nelle impostazioni di ricezione o il partner guest configurato nelle impostazioni di invio di un contratto X12. |
| App per la logica | App per la logica in cui sono configurate le azioni AS2. |
| Stato | Stato dei messaggi X12 <br>Operazione riuscita = ricevuto o inviato un messaggio X12 corretto, nessun ACK funzionale configurato <br>Operazione riuscita = ricevuto o inviato un messaggio X12 corretto, ACK funzionale configurato e ricevuto o ACK funzionale inviato <br>Operazione non riuscita = ricevuto o inviato un messaggio X12 errato <br>In sospeso = ricevuto o inviato un messaggio X12 corretto, ACK funzionale configurato e ACK funzionale previsto. |
| Ack | Stato ACK funzionale (997) <br>Accettato = ricevuto o inviato un ACK funzionale positivo <br>Rifiutato = ricevuto o inviato un ACK funzionale negativo <br>In sospeso = in attesa di un ACK funzionale ma non ricevuto <br>In sospeso = ACK funzionale generato ma impossibile inviarlo al partner <br>Non richiesto = ACK funzionale non configurato |
| Direzione | Direzione dei messaggi X12. |
| ID correlazione | ID per correlare tutti i trigger e le azioni all'interno di un'app per la logica. |
| Tipo di messaggio |  Tipo di messaggio EDI X12. |
| ICN | Numero di controllo interscambio del messaggio X12. |
| TSCN | Numero di controllo set transazionale del messaggio X12. |
| Timestamp | Ora in cui l'azione X12 elabora il messaggio. |


#### <a name="edifact-message-list-property-descriptions"></a>Descrizioni delle proprietà di elenco di messaggi EDIFACT

| Proprietà | Descrizione |
| --- | --- |
| Mittente | Il partner guest configurato nelle impostazioni di ricezione o il partner host configurato nelle impostazioni di invio di un contratto EDIFACT. |
| Ricevitore | Il partner host configurato nelle impostazioni di ricezione o il partner guest configurato nelle impostazioni di invio di un contratto EDIFACT. |
| App per la logica | App per la logica in cui sono configurate le azioni AS2. |
| Stato | Stato dei messaggi EDIFACT <br>Operazione riuscita = ricevuto o inviato un messaggio X12 corretto, nessun ACK funzionale configurato <br>Operazione riuscita = ricevuto o inviato un messaggio X12 corretto, ACK funzionale configurato e ricevuto o ACK funzionale inviato <br>Operazione non riuscita = ricevuto o inviato un messaggio X12 errato <br>In sospeso = ricevuto o inviato un messaggio X12 corretto, ACK funzionale configurato e ACK funzionale previsto. |
| Ack | Stato ACK funzionale (997) <br>Accettato = ricevuto o inviato un ACK funzionale positivo <br>Rifiutato = ricevuto o inviato un ACK funzionale negativo <br>In sospeso = in attesa di un ACK funzionale ma non ricevuto <br>In sospeso = ACK funzionale generato ma impossibile inviarlo al partner <br>Non richiesto = ACK funzionale non configurato |
| Direzione | Direzione dei messaggi EDIFACT. |
| ID correlazione | ID per correlare tutti i trigger e le azioni all'interno di un'app per la logica. |
| Tipo di messaggio |  Tipo dei messaggi EDIFACT. |
| ICN | Numero di controllo interscambio del messaggio EDIFACT. |
| TSCN | Numero di controllo set transazionale del messaggio EDIFACT. |
| Timestamp | Ora in cui l'azione EDIFACT elabora il messaggio. |


## <a name="queries-in-the-operations-management-suite-portal"></a>Query nel portale di Operations Management Suite

Nella pagina di ricerca è possibile creare una query. Quando si esegue una ricerca, è possibile filtrare i risultati usando i controlli facet.

### <a name="create-a-query"></a>Creare una query

1. Nella ricerca di log, scrivere una query e selezionare **Salva**. Si apre **Salva ricerca**. Per scrivere una query, vedere [Tenere traccia dei messaggi B2B nel portale di Operations Management Suite utilizzando una query](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md).
![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. In **Salva ricerca** aggiungere una **nome** e una **categoria**, quindi selezionare **Salva**.   
![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Per visualizzare la query, selezionare i **Preferiti**.    
![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="use-a-saved-query"></a>Usare una query salvata

* Nella ricerca dei log, selezionare i **preferiti** per visualizzare le query salvate.  Per visualizzare i risultati della query, selezionare una query.
![Seleziona home page](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Passaggi successivi
[Schema di rilevamento personalizzato](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[Schema di rilevamento AS2](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[Schema di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")

