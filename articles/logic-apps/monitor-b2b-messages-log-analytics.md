---
title: Monitor B2B messages by using Azure Monitor
description: Risolvere i problemi relativi ai messaggi AS2, X12 ed EDIFACT configurando i log di Monitoraggio di Azure e raccogliendo dati di diagnostica per le app per la logica di AzureTroubleshoot AS2, X12, and EDIFACT messages by setting up Azure Monitor logs and collecting diagnostics data for Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907981"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Configurare i log di Monitoraggio di Azure e raccogliere i dati di diagnostica per i messaggi B2B nelle app per la logica di AzureSet up Azure Monitor logs and collect diagnostics data for B2B messages in Azure Logic Apps

Dopo aver impostato la comunicazione B2B tra partner commerciali nell'account di integrazione, tali partner possono scambiare messaggi utilizzando protocolli quali AS2, X12 ed EDIFACT. Per verificare che questa comunicazione funzioni nel modo previsto, è possibile configurare [i log](../azure-monitor/platform/data-platform-logs.md) di Monitoraggio di Azure per l'account di integrazione. [Monitoraggio di Azure](../azure-monitor/overview.md) consente di monitorare gli ambienti cloud e locali in modo da poterne gestire più facilmente la disponibilità e le prestazioni. Utilizzando i log di Monitoraggio di Azure, è possibile registrare e archiviare dati su dati ed eventi di runtime, ad esempio eventi trigger, eventi di esecuzione ed eventi azione in un'area di lavoro di [Log Analytics.](../azure-monitor/platform/resource-logs-collect-workspace.md) Per i messaggi, la registrazione raccoglie anche informazioni quali:

* Conteggio e stato dei messaggi
* Stato degli acknowledgment
* Correlazioni tra messaggi e riconoscimenti
* Descrizione dettagliata degli errori

Monitoraggio di Azure consente di creare [query di log](../azure-monitor/log-query/log-query-overview.md) per trovare ed esaminare queste informazioni. È anche possibile usare questi dati di diagnostica con altri servizi di [Azure,](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)ad esempio Archiviazione di Azure e Hub eventi di Azure.You can also use this diagnostics data with other Azure services , such as Azure Storage and Azure Event Hubs.

Per configurare la registrazione per l'account di integrazione, [installare la soluzione B2B app per la logica](#install-b2b-solution) nel portale di Azure.To set up logging for your integration account, install the Logic Apps B2B solution in the Azure portal. Questa soluzione fornisce informazioni aggregate per gli eventi dei messaggi B2B. Quindi, per abilitare la registrazione e la creazione di query per queste informazioni, configurare i log di [Monitoraggio di Azure.](#set-up-resource-logs)

Questo articolo illustra come abilitare la registrazione di Monitoraggio di Azure per l'account di integrazione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro Log Analytics. Se non si dispone di un'area di lavoro Log Analytics, vedere [Creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Un'app per la logica configurata con la registrazione di Monitoraggio di Azure e che invia tali informazioni a un'area di lavoro di Log Analytics.A logic app that's set up with Azure Monitor logging and sends that information to a Log Analytics workspace. Informazioni su come configurare i log di Monitoraggio di [Azure per l'app per la logica.](../logic-apps/monitor-logic-apps.md)

* Un account di integrazione collegato all'app per la logica. Informazioni su [come collegare l'account di integrazione all'app per la logica.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Installare la soluzione di App per la logica B2B

Prima che i log di Monitoraggio di Azure possano tenere traccia dei messaggi B2B per l'app per la logica, aggiungere la soluzione **B2B per le app per** la logica all'area di lavoro di Log Analytics.Before Azure Monitor logs can track the B2B messages for your logic app, add the Logic Apps B2B solution to your Log Analytics workspace.

1. Nella casella di ricerca del `log analytics workspaces`portale di [Azure](https://portal.azure.com)immettere e quindi selezionare **Aree di lavoro**di Log Analytics .

   ![Selezionare "Aree di lavoro di Log Analytics"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. In **Aree di lavoro**di Log Analytics selezionare l'area di lavoro.

   ![Selezionare l'area di lavoro Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. Nel riquadro Panoramica, in **Introduzione a Log Analytics** > **Configura soluzioni**di monitoraggio, selezionare **Visualizza soluzioni**.

   ![Nel riquadro Panoramica selezionare "Visualizza soluzioni"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. Nel riquadro Panoramica selezionare **Aggiungi**.

   ![Nel riquadro Panoramica aggiungere una nuova soluzione](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Dopo l'apertura del **Marketplace,** `logic apps b2b`nella casella di ricerca immettere e selezionare **App per la logica B2B**.

   ![Su Marketplace, seleziona "Gestione app per la logica"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. Nel riquadro descrizione soluzione selezionare **Crea**.

   ![Selezionare "Crea" per aggiungere la soluzione "App per la logica B2B"](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Esaminare e confermare l'area di lavoro di Log Analytics in cui si desidera installare la soluzione e selezionare di nuovo **Crea.**

   ![Selezionare "Crea" per "App per la logica B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Dopo che Azure distribuisce la soluzione nel gruppo di risorse di Azure che contiene l'area di lavoro di Log Analytics, la soluzione viene visualizzata nel riquadro di riepilogo dell'area di lavoro. Quando i messaggi B2B vengono elaborati, il conteggio dei messaggi in questo riquadro viene aggiornato.

   ![Riquadro di riepilogo dell'area di lavoro](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configurare i log di Monitoraggio di Azure

È possibile abilitare la registrazione di Monitoraggio di Azure direttamente dall'account di integrazione.

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione.

   ![Trovare e selezionare l'account di integrazione](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. Nel menu dell'account di integrazione, in **Monitoraggio,** selezionare **Impostazioni di diagnostica.** Selezionare **Aggiungi impostazione diagnostica**.

   ![In "Monitoraggio", seleziona "Impostazioni di diagnostica"](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Per creare l'impostazione, attenersi alla seguente procedura:

   1. Specificare un nome per l'impostazione.

   1. Selezionare **Invia a Log Analytics**.

   1. In Sottoscrizione selezionare la sottoscrizione di Azure associata all'area di lavoro di Log Analytics.For **Subscription**, select the Azure subscription that's associated with your Log Analytics workspace.

   1. Per **Log Analytics Workspace**, selezionare l'area di lavoro che si desidera utilizzare.

   1. In **Log**selezionare la categoria **IntegrationAccountTrackingEvents,** che specifica la categoria di eventi che si desidera registrare.

   1. Al termine, selezionare **Salva**.

   Ad esempio: 

   ![Configurare i log di Monitoraggio di Azure per raccogliere dati di diagnosticaSet up Azure Monitor logs to collect diagnostic data](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Visualizzare lo stato dei messaggi

Dopo l'esecuzione dell'app per la logica, è possibile visualizzare lo stato e i dati relativi ai messaggi nell'area di lavoro di Log Analytics.After your logic app runs, you can view the status and data about those messages in your Log Analytics workspace.

1. Nella casella di ricerca del portale di Azure individuare e aprire l'area di lavoro Log Analytics.In the [Azure portal](https://portal.azure.com) search box, find and open your Log Analytics workspace.

1. Nel menu dell'area di lavoro selezionare **Riepilogo area di lavoro** > **App per la logica B2B**.

   ![Riquadro di riepilogo dell'area di lavoro](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Se il riquadro B2B app per la logica non mostra immediatamente i risultati dopo un'esecuzione, provare a selezionare **Aggiorna** o attendere un breve periodo di tempo prima di riprovare.

   Per impostazione predefinita, il riquadro **App per la logica B2B** visualizza i dati relativi a un solo giorno. Per modificare l'ambito dei dati in un intervallo diverso, selezionare il controllo ambito nella parte superiore della pagina:

   ![Modifica intervallo](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Quando viene aperto il dashboard con lo stato dei messaggi, è possibile visualizzare altri dettagli per un tipo di messaggio specifico, con i dati relativi a un solo giorno. Selezionare il riquadro per **AS2**, **X12**o **EDIFACT**.

   ![Visualizzare gli stati dei messaggi](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Un elenco di messaggi viene visualizzato per il riquadro scelto. Ecco, ad esempio, come può risultare un elenco di messaggi AS2:

   ![Stati e dettagli per i messaggi AS2](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Per altre informazioni sulle proprietà di ogni tipo di messaggio, vedere queste descrizioni delle proprietà dei messaggi:

   * [Proprietà dei messaggi AS2](#as2-message-properties)
   * [Proprietà dei messaggi X12](#x12-message-properties)
   * [Proprietà dei messaggi EDIFACT](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrizioni delle proprietà e formati di nome per i messaggi AS2, X12 ed EDIFACT

Per ogni tipo di messaggio, di seguito sono elencate le descrizioni delle proprietà e i formati dei nomi per i file di messaggi scaricati.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrizioni delle proprietà dei messaggi AS2

Ecco le descrizioni delle proprietà per ogni messaggio AS2.

| Proprietà | Descrizione |
|----------|-------------|
| **Mittente** | Partner guest specificato in **Impostazioni di ricezione** o partner host specificato in **Impostazioni di invio** di un accordo AS2 |
| **Ricevitore** | Partner host specificato in **Impostazioni di ricezione** o partner guest specificato in **Impostazioni di invio** di un accordo AS2 |
| **App per la logica** | App per la logica in cui sono configurate le azioni AS2 |
| **Stato** | Stato dei messaggi AS2 <br>Operazione completata = ricevuto o inviato un messaggio AS2 valido. Non sono configurate notifiche sulla ricezione del messaggio. <br>Operazione completata = ricevuto o inviato un messaggio AS2 valido. La notifica sulla ricezione del messaggio è stata configurata e ricevuta o è stata inviata. <br>Operazione non riuscita = ricevuto un messaggio AS2 non valido. Non sono configurate notifiche sulla ricezione del messaggio. <br>In sospeso = ricevuto o inviato un messaggio AS2 valido. La notifica sulla ricezione del messaggio è stata configurata ed è prevista. |
| **Ack** | Stato dei messaggi di notifica sulla ricezione del messaggio <br>Accettato = ricevuto o inviato un messaggio di notifica sulla ricezione del messaggio positivo. <br>In sospeso = in attesa di ricevere o inviare un messaggio di notifica sulla ricezione del messaggio. <br>Rifiutato = ricevuto o inviato un messaggio di notifica sulla ricezione del messaggio negativo. <br>Non richiesto = notifica sulla ricezione del messaggio non configurata nell'accordo. |
| **Direzione** | Direzione dei messaggi AS2 |
| **ID monitoraggio** | ID che correla tutti i trigger e le azioni in un'app per la logica |
| **ID messaggio** | ID del messaggio AS2 dalle intestazioni del messaggio AS2 |
| **Timestamp** | Ora in cui l'azione AS2 ha elaborato il messaggio |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descrizioni delle proprietà dei messaggi X12

Ecco le descrizioni delle proprietà per ogni messaggio X12.

| Proprietà | Descrizione |
|----------|-------------|
| **Mittente** | Partner guest specificato in **Impostazioni di ricezione** o partner host specificato in **Impostazioni di invio** di un accordo X12 |
| **Ricevitore** | Partner host specificato in **Impostazioni di ricezione** o partner host specificato in **Impostazioni di invio** di un accordo X12 |
| **App per la logica** | App per la logica in cui sono configurate le azioni X12 |
| **Stato** | Stato dei messaggi X12 <br>Operazione completata = ricevuto o inviato un messaggio X12 valido. Non sono configurati ack funzionali. <br>Operazione completata = ricevuto o inviato un messaggio X12 valido. L'ack funzionale è stato configurato e ricevuto o inviato. <br>Operazione non riuscita = ricevuto o inviato un messaggio X12 non valido. <br>In sospeso = ricevuto o inviato un messaggio X12 valido. L'ack funzionale è stato configurato ed è previsto. |
| **Ack** | Stato ACK funzionale (997) <br>Accettato = ricevuto o inviato un ack funzionale positivo. <br>Rifiutato = ricevuto o inviato un ack funzionale negativo. <br>In sospeso = in attesa di un ack funzionale non ricevuto. <br>In sospeso = ack funzionale generato, ma impossibile inviarlo al partner. <br>Non richiesto = ack funzionale non configurato. |
| **Direzione** | Direzione dei messaggi X12 |
| **ID monitoraggio** | ID che correla tutti i trigger e le azioni in un'app per la logica |
| **Tipo di messaggio** | Tipo di messaggio EDI X12 |
| **ICN** | Numero di controllo interscambio del messaggio X12 |
| **TSCN** | Numero di controllo del set di transazioni del messaggio X12 |
| **Timestamp** | Ora in cui l'azione X12 ha elaborato il messaggio |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descrizioni delle proprietà dei messaggi EDIFACT

Ecco le descrizioni delle proprietà per ogni messaggio EDIFACT.

| Proprietà | Descrizione |
|----------|-------------|
| **Mittente** | Partner guest specificato in **Impostazioni di ricezione** o partner host specificato in **Impostazioni di invio** di un accordo EDIFACT |
| **Ricevitore** | Partner host specificato in **Impostazioni di ricezione** o partner guest specificato in **Impostazioni di invio** di un accordo EDIFACT |
| **App per la logica** | App per la logica in cui sono configurate le azioni EDIFACT |
| **Stato** | Stato dei messaggi EDIFACT <br>Operazione completata = ricevuto o inviato un messaggio EDIFACT valido. Non sono configurati ack funzionali. <br>Operazione completata = ricevuto o inviato un messaggio EDIFACT valido. L'ack funzionale è stato configurato e ricevuto o inviato. <br>Operazione non riuscita = ricevuto o inviato un messaggio EDIFACT non valido <br>In sospeso = ricevuto o inviato un messaggio EDIFACT valido. L'ack funzionale è stato configurato ed è previsto. |
| **Ack** | Stato Ack funzionale (CONTRL) <br>Accettato = ricevuto o inviato un ack funzionale positivo. <br>Rifiutato = ricevuto o inviato un ack funzionale negativo. <br>In sospeso = in attesa di un ack funzionale non ricevuto. <br>In sospeso = ack funzionale generato, ma impossibile inviarlo al partner. <br>Non richiesto = ack funzionale non configurato. |
| **Direzione** | Direzione dei messaggi EDIFACT |
| **ID monitoraggio** | ID che correla tutti i trigger e le azioni in un'app per la logica |
| **Tipo di messaggio** | Tipo di messaggio EDIFACT |
| **ICN** | Numero di controllo interscambio del messaggio EDIFACT |
| **TSCN** | Numero di controllo del set di transazioni del messaggio EDIFACT |
| **Timestamp** | Ora in cui l'azione EDIFACT ha elaborato il messaggio |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Passaggi successivi

* [Creare query di monitoraggio e rilevamento](../logic-apps/create-monitoring-tracking-queries.md)