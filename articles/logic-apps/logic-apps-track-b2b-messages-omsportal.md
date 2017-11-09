---
title: 'Tenere traccia dei messaggi B2B in Operations Management Suite: App per la logica di Azure | Microsoft Docs'
description: Tenere traccia delle comunicazioni B2B per l'account di integrazione e le app per la logica in Operations Management Suite (OMS) con Azure Log Analytics
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
ms.openlocfilehash: 3ef7a4054be80547b0d91ad1f13777d915005f8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="track-b2b-communication-in-the-microsoft-operations-management-suite-oms"></a>Tenere traccia delle comunicazioni B2B in Microsoft Operations Management Suite (OMS)

Dopo avere configurato la comunicazione B2B tra due processi o applicazioni aziendali in esecuzione tramite l'account di integrazione, tali entità possono scambiarsi messaggi. Per controllare se questi messaggi vengono elaborati correttamente, è possibile tenere traccia dei messaggi AS2, X12 ed EDIFACT con [Azure Log Analytics](../log-analytics/log-analytics-overview.md) in [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). È ad esempio possibile usare queste funzionalità di rilevamento basate sul Web per tenere traccia dei messaggi:

* Conteggio e stato dei messaggi
* Stato degli acknowledgment
* Correlare i messaggi con i riconoscimenti
* Descrizione dettagliata degli errori
* Funzionalità di ricerca

## <a name="requirements"></a>Requisiti

* Un'app per la logica configurata con la registrazione diagnostica. Informazioni su [come creare un'app per la logica](logic-apps-create-a-logic-app.md) e [come configurare la registrazione per tale app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Un account di integrazione configurato con il monitoraggio e la registrazione. Informazioni su [come creare un account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [come configurare il monitoraggio e la registrazione per tale account](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se non è già stato fatto, [pubblicare i dati di diagnostica in Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) in OMS.

> [!NOTE]
> Dopo avere soddisfatto i requisiti precedenti, sarà presente un'area di lavoro in [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). È consigliabile usare la stessa area di lavoro di OMS per tenere traccia delle comunicazioni B2B in OMS. 
>  
> Se non si ha un'area di lavoro di OMS, vedere [come crearne una](../log-analytics/log-analytics-get-started.md).

## <a name="add-the-logic-apps-b2b-solution-to-the-operations-management-suite-oms"></a>Aggiungere la soluzione B2B di App per la logica a Operations Management Suite (OMS)

Per fare in modo che OMS tenga traccia dei messaggi B2B per l'app per la logica, è necessario aggiungere la soluzione **App per la logica B2B** al portale di OMS. Informazioni sull'[aggiunta di soluzioni a OMS](../log-analytics/log-analytics-get-started.md).

1. Nel [portale di Azure](https://portal.azure.com) scegliere **Altri servizi**. Cercare "Log Analytics" e quindi scegliere **Log Analytics**, come illustrato qui:

   ![Trovare Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. In **Log Analytics** trovare e selezionare l'area di lavoro di OMS. 

   ![Selezionare l'area di lavoro di OMS](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. In **Gestione** scegliere **Portale di OMS**.

   ![Scegliere Portale di OMS](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Quando si apre la home page di OMS, scegliere **Raccolta soluzioni**.    

   ![Scegliere Raccolta soluzioni](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. In **Tutte le soluzioni** trovare e scegliere **App per la logica B2B**.     

   ![Scegliere App per la logica B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. In **App per la logica B2B** scegliere **Aggiungi**.

   ![Scegliere Aggiungi](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   Nella home page di OMS viene ora visualizzato il riquadro **Messaggi per le app per la logica B2B**. 
   Questo riquadro aggiorna il numero di messaggi B2B quando vengono elaborati.

   ![Home page di OMS, riquadro Messaggi per le app per la logica B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-the-operations-management-suite"></a>Tenere traccia dello stato e dei dettagli dei messaggi in Operations Management Suite

1. Dopo che i messaggi B2B sono stati elaborati, è possibile visualizzarne lo stato e i dettagli. Nella home page di OMS scegliere il riquadro **Messaggi per le app per la logica B2B**.

   ![Numero di messaggi aggiornato](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > Per impostazione predefinita, il riquadro **Messaggi per le app per la logica B2B** visualizza i dati relativi a un solo giorno. Per impostare l'ambito dei dati su un intervallo diverso, scegliere il controllo dell'ambito nella parte superiore della pagina di OMS:
   > 
   > ![Modificare l'ambito dei dati](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. Quando viene aperto il dashboard con lo stato dei messaggi, è possibile visualizzare altri dettagli per un tipo di messaggio specifico, con i dati relativi a un solo giorno. Scegliere il riquadro **AS2**, **X12** o **EDIFACT**.

   ![Visualizzare lo stato dei messaggi](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Un elenco di messaggi viene visualizzato per il riquadro scelto. 
   Per altre informazioni sulle proprietà di ogni tipo di messaggio, vedere queste descrizioni delle proprietà dei messaggi:

   * [Proprietà dei messaggi AS2](#as2-message-properties)
   * [Proprietà dei messaggi X12](#x12-message-properties)
   * [Proprietà dei messaggi EDIFACT](#EDIFACT-message-properties)

   Ecco, ad esempio, come può risultare un elenco di messaggi AS2:

   ![Visualizzare i messaggi AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Per visualizzare o esportare gli input e gli output di messaggi specifici, selezionare i messaggi e scegliere **Scarica**. Quando richiesto, salvare il file ZIP nel computer locale e quindi estrarre il file. 

   La cartella estratta include una cartella per ogni messaggio selezionato. 
   Se si configurano gli acknowledgement, la cartella del messaggio include anche i file con i dettagli degli acknowledgement. 
   Ogni cartella di messaggio include almeno questi file: 
   
   * File leggibili con i dettagli dei payload di input e dei payload di output
   * File codificati con gli input e gli output

   Per ogni tipo di messaggio, è possibile trovare i formati dei nomi di file e cartelle qui:

   * [Formati dei nomi di file e cartelle AS2](#as2-folder-file-names)
   * [Formati dei nomi di file e cartelle X12](#x12-folder-file-names)
   * [Formati dei nomi di file e cartelle EDIFACT](#edifact-folder-file-names)

   ![Scaricare i file dei messaggi](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Per visualizzare tutte le azioni con lo stesso ID esecuzione, nella pagina **Ricerca log** scegliere un messaggio dall'elenco di messaggi.

   È possibile ordinare queste azioni per colonna o cercare risultati specifici.

   ![Azioni con lo stesso ID esecuzione](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Per cercare i risultati con le query predefinite, scegliere **Preferiti**.

   * Informazioni su [come creare le query aggiungendo i filtri](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Altre informazioni su [come trovare i dati con le ricerche log in Log Analytics](../log-analytics/log-analytics-log-searches.md).

   * Per modificare la query nella casella di ricerca, aggiornare la query con le colonne e i valori che si vogliono usare come filtri.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrizioni delle proprietà e formati di nome per i messaggi AS2, X12 ed EDIFACT

Per ogni tipo di messaggio, di seguito sono elencate le descrizioni delle proprietà e i formati dei nomi per i file di messaggi scaricati.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrizioni delle proprietà dei messaggi AS2

Ecco le descrizioni delle proprietà per ogni messaggio AS2.

| Proprietà | Descrizione |
| --- | --- |
| Mittente | Partner guest specificato in **Impostazioni di ricezione** o partner host specificato in **Impostazioni di invio** di un accordo AS2 |
| Ricevitore | Partner host specificato in **Impostazioni di ricezione** o partner guest specificato in **Impostazioni di invio** di un accordo AS2 |
| App per la logica | App per la logica in cui sono configurate le azioni AS2 |
| Stato | Stato dei messaggi AS2 <br>Operazione completata = ricevuto o inviato un messaggio AS2 valido. Non sono configurate notifiche sulla ricezione del messaggio. <br>Operazione completata = ricevuto o inviato un messaggio AS2 valido. La notifica sulla ricezione del messaggio è stata configurata e ricevuta o è stata inviata. <br>Operazione non riuscita = ricevuto un messaggio AS2 non valido. Non sono configurate notifiche sulla ricezione del messaggio. <br>In sospeso = ricevuto o inviato un messaggio AS2 valido. La notifica sulla ricezione del messaggio è stata configurata ed è prevista. |
| Ack | Stato dei messaggi di notifica sulla ricezione del messaggio <br>Accettato = ricevuto o inviato un messaggio di notifica sulla ricezione del messaggio positivo. <br>In sospeso = in attesa di ricevere o inviare un messaggio di notifica sulla ricezione del messaggio. <br>Rifiutato = ricevuto o inviato un messaggio di notifica sulla ricezione del messaggio negativo. <br>Non richiesto = notifica sulla ricezione del messaggio non configurata nell'accordo. |
| Direzione | Direzione dei messaggi AS2 |
| ID correlazione | ID che correla tutti i trigger e le azioni in un'app per la logica |
| ID del messaggio | ID del messaggio AS2 dalle intestazioni del messaggio AS2 |
| Timestamp | Ora in cui l'azione AS2 ha elaborato il messaggio |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formati dei nomi AS2 per i file di messaggi scaricati

Ecco i formati dei nomi per ogni cartella e file di messaggi AS2 scaricati.

| File o cartella | Formato del nome |
| :------------- | :---------- |
| Cartella del messaggio | [mittente]\_[destinatario]\_AS2\_[ID-correlazione]\_[ID-messaggio]\_[timestamp] |
| File di input, output e, se configurati, acknowledgement | **Payload di input**: [mittente]\_[destinatario]\_AS2\_[ID-correlazione]\_input_payload.txt </p>**Payload di output**: [mittente]\_[destinatario]\_AS2\_[ID-correlazione]\_output\_payload.txt </p></p>**Input**: [mittente]\_[destinatario]\_AS2\_[ID-correlazione]\_inputs.txt </p></p>**Output**: [mittente]\_[destinatario]\_AS2\_[ID-correlazione]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descrizioni delle proprietà dei messaggi X12

Ecco le descrizioni delle proprietà per ogni messaggio X12.

| Proprietà | Descrizione |
| --- | --- |
| Mittente | Partner guest specificato in **Impostazioni di ricezione** o partner host specificato in **Impostazioni di invio** di un accordo X12 |
| Ricevitore | Partner host specificato in **Impostazioni di ricezione** o partner host specificato in **Impostazioni di invio** di un accordo X12 |
| App per la logica | App per la logica in cui sono configurate le azioni X12 |
| Stato | Stato dei messaggi X12 <br>Operazione completata = ricevuto o inviato un messaggio X12 valido. Non sono configurati ack funzionali. <br>Operazione completata = ricevuto o inviato un messaggio X12 valido. L'ack funzionale è stato configurato e ricevuto o inviato. <br>Operazione non riuscita = ricevuto o inviato un messaggio X12 non valido. <br>In sospeso = ricevuto o inviato un messaggio X12 valido. L'ack funzionale è stato configurato ed è previsto. |
| Ack | Stato ACK funzionale (997) <br>Accettato = ricevuto o inviato un ack funzionale positivo. <br>Rifiutato = ricevuto o inviato un ack funzionale negativo. <br>In sospeso = in attesa di un ack funzionale non ricevuto. <br>In sospeso = ack funzionale generato, ma impossibile inviarlo al partner. <br>Non richiesto = ack funzionale non configurato. |
| Direzione | Direzione dei messaggi X12 |
| ID correlazione | ID che correla tutti i trigger e le azioni in un'app per la logica |
| Tipo di messaggio | Tipo di messaggio EDI X12 |
| ICN | Numero di controllo interscambio del messaggio X12 |
| TSCN | Numero di controllo del set di transazioni del messaggio X12 |
| Timestamp | Ora in cui l'azione X12 ha elaborato il messaggio |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formati dei nomi X12 per i file di messaggi scaricati

Ecco i formati dei nomi per ogni cartella e file di messaggi X12 scaricati.

| File o cartella | Formato del nome |
| :------------- | :---------- |
| Cartella del messaggio | [mittente]\_[destinatario]\_X12\_[numero-controllo-interscambio]\_[numero-controllo-globale]\_[numero-controllo-set-transazioni]\_[timestamp] |
| File di input, output e, se configurati, acknowledgement | **Payload di input**: [mittente]\_[destinatario]\_X12\_[numero-controllo-interscambio]\_input_payload.txt </p>**Payload di output**: [mittente]\_[destinatario]\_X12\_[numero-controllo-interscambio]\_output\_payload.txt </p></p>**Input**: [mittente]\_[destinatario]\_X12\_[numero-controllo-interscambio]\_inputs.txt </p></p>**Output**: [mittente]\_[destinatario]\_X12\_[numero-controllo-interscambio]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descrizioni delle proprietà dei messaggi EDIFACT

Ecco le descrizioni delle proprietà per ogni messaggio EDIFACT.

| Proprietà | Descrizione |
| --- | --- |
| Mittente | Partner guest specificato in **Impostazioni di ricezione** o partner host specificato in **Impostazioni di invio** di un accordo EDIFACT |
| Ricevitore | Partner host specificato in **Impostazioni di ricezione** o partner guest specificato in **Impostazioni di invio** di un accordo EDIFACT |
| App per la logica | App per la logica in cui sono configurate le azioni EDIFACT |
| Stato | Stato dei messaggi EDIFACT <br>Operazione completata = ricevuto o inviato un messaggio EDIFACT valido. Non sono configurati ack funzionali. <br>Operazione completata = ricevuto o inviato un messaggio EDIFACT valido. L'ack funzionale è stato configurato e ricevuto o inviato. <br>Operazione non riuscita = ricevuto o inviato un messaggio EDIFACT non valido <br>In sospeso = ricevuto o inviato un messaggio EDIFACT valido. L'ack funzionale è stato configurato ed è previsto. |
| Ack | Stato ACK funzionale (997) <br>Accettato = ricevuto o inviato un ack funzionale positivo. <br>Rifiutato = ricevuto o inviato un ack funzionale negativo. <br>In sospeso = in attesa di un ack funzionale non ricevuto. <br>In sospeso = ack funzionale generato, ma impossibile inviarlo al partner. <br>Non richiesto = ack funzionale non configurato. |
| Direzione | Direzione dei messaggi EDIFACT |
| ID correlazione | ID che correla tutti i trigger e le azioni in un'app per la logica |
| Tipo di messaggio | Tipo di messaggio EDIFACT |
| ICN | Numero di controllo interscambio del messaggio EDIFACT |
| TSCN | Numero di controllo del set di transazioni del messaggio EDIFACT |
| Timestamp | Ora in cui l'azione EDIFACT ha elaborato il messaggio |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formati dei nomi EDIFACT per i file di messaggi scaricati

Ecco i formati dei nomi per ogni cartella e file di messaggi EDIFACT scaricati.

| File o cartella | Formato del nome |
| :------------- | :---------- |
| Cartella del messaggio | [mittente]\_[destinatario]\_EDIFACT\_[numero-controllo-interscambio]\_[numero-controllo-globale]\_[numero-controllo-set-transazioni]\_[timestamp] |
| File di input, output e, se configurati, acknowledgement | **Payload di input**: [mittente]\_[destinatario]\_EDIFACT\_[numero-controllo-interscambio]\_input_payload.txt </p>**Payload di output**: [mittente]\_[destinatario]\_EDIFACT\_[numero-controllo-interscambio]\_output\_payload.txt </p></p>**Input**: [mittente]\_[destinatario]\_EDIFACT\_[numero-controllo-interscambio]\_inputs.txt </p></p>**Output**: [mittente]\_[destinatario]\_EDIFACT\_[numero-controllo-interscambio]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Passaggi successivi

* [Cercare i messaggi B2B in Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemi di rilevamento X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schemi di rilevamento personalizzati](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)