---
title: Monitorare i messaggi B2B usando monitoraggio di Azure
description: Risolvere i problemi relativi ai messaggi AS2, X12 ed EDIFACT impostando log di monitoraggio di Azure e raccogliendo i dati di diagnostica per app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5baa4d4d968adb25b5520ca91149970f5c5578e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86536269"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Configurare i log di Monitoraggio di Azure e raccogliere i dati di diagnostica per i messaggi B2B in App per la logica di Azure

Dopo aver configurato la comunicazione B2B tra partner commerciali nell'account di integrazione, tali partner possono scambiare messaggi utilizzando protocolli quali AS2, X12 ed EDIFACT. Per verificare che questa comunicazione funzioni come previsto, è possibile configurare i log di [monitoraggio di Azure](../azure-monitor/platform/data-platform-logs.md) per l'account di integrazione. [Monitoraggio di Azure](../azure-monitor/overview.md) consente di monitorare gli ambienti cloud e locali in modo da mantenere la disponibilità e le prestazioni più semplici. Usando i log di monitoraggio di Azure, è possibile registrare e archiviare i dati relativi a eventi e dati di runtime, ad esempio eventi trigger, eventi di esecuzione ed eventi di azione in un' [area di lavoro log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace). Per i messaggi, la registrazione raccoglie anche informazioni come:

* Conteggio e stato dei messaggi
* Stato degli acknowledgment
* Correlazioni tra messaggi e riconoscimenti
* Descrizione dettagliata degli errori

Monitoraggio di Azure consente di creare [query di log](../azure-monitor/log-query/log-query-overview.md) che consentono di trovare e rivedere queste informazioni. È anche possibile [usare questi dati di diagnostica con altri servizi di Azure](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data), ad esempio archiviazione di Azure e hub eventi di Azure.

Per configurare la registrazione per l'account di integrazione, [installare la soluzione app per la logica B2B](#install-b2b-solution) nel portale di Azure. Questa soluzione fornisce informazioni aggregate per gli eventi del messaggio B2B. Quindi, per abilitare la registrazione e la creazione di query per queste informazioni, configurare i [log di monitoraggio di Azure](#set-up-resource-logs).

Questo articolo illustra come abilitare la registrazione di monitoraggio di Azure per l'account di integrazione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro Log Analytics. Se non si dispone di un'area di lavoro Log Analytics, vedere [Creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Un'app per la logica configurata con la registrazione di monitoraggio di Azure e invia tali informazioni a un'area di lavoro Log Analytics. Informazioni su [come configurare i log di monitoraggio di Azure per l'app per la logica](../logic-apps/monitor-logic-apps.md).

* Un account di integrazione collegato all'app per la logica. Informazioni [su come collegare l'account di integrazione all'app per la logica](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Installare la soluzione di App per la logica B2B

Prima che i log di monitoraggio di Azure possano tenere traccia dei messaggi B2B per l'app per la logica, aggiungere la soluzione **app per la logica B2B** all'area di lavoro di log Analytics.

1. Nella casella di ricerca del [portale di Azure](https://portal.azure.com)immettere `log analytics workspaces` e quindi selezionare **log Analytics aree di lavoro**.

   ![Selezionare "aree di lavoro Log Analytics"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. In **log Analytics aree di lavoro**selezionare l'area di lavoro.

   ![Selezionare l'area di lavoro Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. Nel riquadro Panoramica, in Introduzione **a log Analytics**  >  **configurare le soluzioni di monitoraggio**, selezionare **Visualizza soluzioni**.

   ![Nel riquadro Panoramica selezionare "Visualizza soluzioni".](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. Nel riquadro Panoramica selezionare **Aggiungi**.

   ![Nel riquadro Panoramica aggiungere una nuova soluzione](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Dopo l'apertura del **Marketplace** , nella casella di ricerca immettere `logic apps b2b` e selezionare **app per la logica B2B**.

   ![Da Marketplace selezionare "gestione app per la logica"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. Nel riquadro Descrizione soluzione selezionare **Crea**.

   ![Selezionare "Crea" per aggiungere la soluzione "App per la logica B2B"](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Verificare e confermare l'area di lavoro Log Analytics in cui si vuole installare la soluzione e selezionare di nuovo **Crea** .

   ![Selezionare "Crea" per "App per la logica B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Quando Azure distribuisce la soluzione al gruppo di risorse di Azure che contiene l'area di lavoro Log Analytics, la soluzione viene visualizzata nel riquadro di riepilogo dell'area di lavoro. Quando vengono elaborati i messaggi B2B, il numero di messaggi in questo riquadro viene aggiornato.

   ![Riquadro di riepilogo area di lavoro](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configurare i log di Monitoraggio di Azure

È possibile abilitare la registrazione di monitoraggio di Azure direttamente dall'account di integrazione.

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione.

   ![Trovare e selezionare l'account di integrazione](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. Dal menu dell'account di integrazione, in **monitoraggio**selezionare **impostazioni di diagnostica**. Selezionare **Aggiungi impostazioni di diagnostica**.

   ![In "monitoraggio" selezionare "impostazioni di diagnostica".](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Per creare l'impostazione, attenersi alla seguente procedura:

   1. Consente di specificare un nome per l'impostazione.

   1. Selezionare **Invia a Log Analytics**.

   1. Per **Subscription (sottoscrizione**) selezionare la sottoscrizione di Azure associata all'area di lavoro log Analytics.

   1. Per **log Analytics area di lavoro**, selezionare l'area di lavoro che si desidera utilizzare.

   1. In **log**selezionare la categoria **IntegrationAccountTrackingEvents** , che specifica la categoria di eventi che si desidera registrare.

   1. Al termine, selezionare **Salva**.

   Ad esempio: 

   ![Configurare i log di monitoraggio di Azure per raccogliere i dati di diagnostica](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Visualizzare lo stato dei messaggi

Dopo l'esecuzione dell'app per la logica, è possibile visualizzare lo stato e i dati relativi a tali messaggi nell'area di lavoro Log Analytics.

1. Nella casella di ricerca [portale di Azure](https://portal.azure.com) individuare e aprire l'area di lavoro log Analytics.

1. Nel menu dell'area di lavoro selezionare app per la logica B2B di **Riepilogo dell'area di lavoro**  >  **Logic Apps B2B**.

   ![Riquadro di riepilogo area di lavoro](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Se il riquadro App per la logica B2B non visualizza immediatamente i risultati dopo un'esecuzione, provare a selezionare **Aggiorna** o attendere un breve periodo di tempo prima di riprovare.

   Per impostazione predefinita, il riquadro **App per la logica B2B** visualizza i dati relativi a un solo giorno. Per impostare l'ambito dei dati su un intervallo diverso, selezionare il controllo ambito nella parte superiore della pagina:

   ![Modifica intervallo](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Quando viene aperto il dashboard con lo stato dei messaggi, è possibile visualizzare altri dettagli per un tipo di messaggio specifico, con i dati relativi a un solo giorno. Selezionare il riquadro per **AS2**, **X12**o **EDIFACT**.

   ![Visualizzare gli Stati dei messaggi](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

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

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../azure-monitor/log-query/log-query-overview.md).

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
| **Status** | Stato dei messaggi AS2 <br>Operazione completata = ricevuto o inviato un messaggio AS2 valido. Non sono configurate notifiche sulla ricezione del messaggio. <br>Operazione completata = ricevuto o inviato un messaggio AS2 valido. La notifica sulla ricezione del messaggio è stata configurata e ricevuta o è stata inviata. <br>Operazione non riuscita = ricevuto un messaggio AS2 non valido. Non sono configurate notifiche sulla ricezione del messaggio. <br>In sospeso = ricevuto o inviato un messaggio AS2 valido. La notifica sulla ricezione del messaggio è stata configurata ed è prevista. |
| **ACK** | Stato dei messaggi di notifica sulla ricezione del messaggio <br>Accettato = ricevuto o inviato un messaggio di notifica sulla ricezione del messaggio positivo. <br>In sospeso = in attesa di ricevere o inviare un messaggio di notifica sulla ricezione del messaggio. <br>Rifiutato = ricevuto o inviato un messaggio di notifica sulla ricezione del messaggio negativo. <br>Non richiesto = notifica sulla ricezione del messaggio non configurata nell'accordo. |
| **Direzione** | Direzione dei messaggi AS2 |
| **ID di traccia** | ID che correla tutti i trigger e le azioni in un'app per la logica |
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
| **Status** | Stato dei messaggi X12 <br>Operazione completata = ricevuto o inviato un messaggio X12 valido. Non sono configurati ack funzionali. <br>Operazione completata = ricevuto o inviato un messaggio X12 valido. L'ack funzionale è stato configurato e ricevuto o inviato. <br>Operazione non riuscita = ricevuto o inviato un messaggio X12 non valido. <br>In sospeso = ricevuto o inviato un messaggio X12 valido. L'ack funzionale è stato configurato ed è previsto. |
| **ACK** | Stato ACK funzionale (997) <br>Accettato = ricevuto o inviato un ack funzionale positivo. <br>Rifiutato = ricevuto o inviato un ack funzionale negativo. <br>In sospeso = in attesa di un ack funzionale non ricevuto. <br>In sospeso = ack funzionale generato, ma impossibile inviarlo al partner. <br>Non richiesto = ack funzionale non configurato. |
| **Direzione** | Direzione dei messaggi X12 |
| **ID di traccia** | ID che correla tutti i trigger e le azioni in un'app per la logica |
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
| **Status** | Stato dei messaggi EDIFACT <br>Operazione completata = ricevuto o inviato un messaggio EDIFACT valido. Non sono configurati ack funzionali. <br>Operazione completata = ricevuto o inviato un messaggio EDIFACT valido. L'ack funzionale è stato configurato e ricevuto o inviato. <br>Operazione non riuscita = ricevuto o inviato un messaggio EDIFACT non valido <br>In sospeso = ricevuto o inviato un messaggio EDIFACT valido. L'ack funzionale è stato configurato ed è previsto. |
| **ACK** | Stato ACK funzionali (CONTRL) <br>Accettato = ricevuto o inviato un ack funzionale positivo. <br>Rifiutato = ricevuto o inviato un ack funzionale negativo. <br>In sospeso = in attesa di un ack funzionale non ricevuto. <br>In sospeso = ack funzionale generato, ma impossibile inviarlo al partner. <br>Non richiesto = ack funzionale non configurato. |
| **Direzione** | Direzione dei messaggi EDIFACT |
| **ID di traccia** | ID che correla tutti i trigger e le azioni in un'app per la logica |
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
