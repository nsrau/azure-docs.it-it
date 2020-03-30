---
title: Messaggi di Exchange per scenari di integrazione aziendale B2B
description: Ricevere e inviare messaggi B2B tra partner commerciali in App per la logica di Azure tramite Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151205"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Ricevere e inviare messaggi B2B usando Le app per la logica di Azure e Enterprise Integration Pack

Quando si dispone di un account di integrazione che definisce i partner commerciali e i contratti, è possibile creare un flusso di lavoro automatizzato business to business (B2B) che scambia messaggi tra partner commerciali utilizzando [Le app per la logica](../logic-apps/logic-apps-overview.md) di Azure con Enterprise Integration [Pack.](../logic-apps/logic-apps-enterprise-integration-overview.md) App per la logica di Azure funziona con connettori che supportano protocolli standard del settore AS2, X12, EDIFACT e RosettaNet.Azure Logic Apps works with connectors that support AS2, X12, EDIFACT, and RosettaNet industry-standard protocols. È inoltre possibile combinare questi connettori con altri [connettori disponibili in App per](../connectors/apis-list.md)la logica, ad esempio Salesforce e Office 365 Outlook.

Questo articolo illustra come creare un'app per la logica che riceve una richiesta HTTP usando un trigger di richiesta, decodifica il contenuto del messaggio usando le azioni AS2 e X12 e quindi restituisce una risposta tramite l'azione Response.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione, [iscriversi per ottenere un account Azure gratuito.](https://azure.microsoft.com/free/)

* Un'app per la logica vuota in modo che sia possibile creare il flusso di lavoro B2B usando il trigger [di richiesta](../connectors/connectors-native-reqres.md) seguito da queste azioni:A blank logic app so that you can create the B2B workflow by using the Request trigger that's followed by these actions:

  * [Decodifica AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Condizione](../logic-apps/logic-apps-control-flow-conditional-statement.md), che invia una [risposta](../connectors/connectors-native-reqres.md) in base all'esito positivo o negativo dell'azione di decodifica AS2

  * [Decodifica messaggio X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Se non si ha familiarità con le app per la logica, vedere [Che cos'è App per la logica](../logic-apps/logic-apps-overview.md) di Azure? e Guida [introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Account di [integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) associato alla sottoscrizione di Azure e collegato all'app per la logica. Sia l'app per la logica che l'account di integrazione devono esistere nello stesso percorso o nell'area di Azure.Both logic app and integration account must exist in the same location or Azure region.

* Almeno due [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) che hai già definito nel tuo account di integrazione insieme agli accordi [AS2 e X12](logic-apps-enterprise-integration-agreements.md) per tali partner.

## <a name="add-request-trigger"></a>Aggiungi trigger richiesta

Questo esempio usa la finestra di progettazione app per la logica nel portale di Azure, ma è possibile seguire passaggi simili per la finestra di progettazione app per la logica in Visual Studio.This example uses the Logic App Designer in the Azure portal, but you can follow similar steps for the Logic App Designer in Visual Studio.

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica vuota in Progettazione app per la logica.

1. Nella casella di `when a http request`ricerca immettere e selezionare **Quando viene ricevuta una richiesta HTTP** da utilizzare come trigger.

   ![Selezionare Richiedi trigger per avviare il flusso di lavoro dell'app per la logicaSelect Request trigger to start your logic app workflow](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Lasciare vuota la casella **Schema JSON del corpo** della richiesta perché il messaggio X12 è un file flat.

   ![Lasciare vuoto "Schema JSON del corpo della richiesta"](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Al termine, nella barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Questo passaggio genera **l'URL HTTP POST** da usare per l'invio della richiesta che attiva l'app per la logica. Per copiare l'URL, selezionare l'icona di copia accanto all'URL.

   ![URL generato per il trigger di richiesta per la ricezione delle chiamate](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>Azione di decodifica Aggiungi AS2

Aggiungere ora le azioni B2B che si desidera utilizzare. In questo esempio vengono utilizzate le azioni AS2 e X12.

1. Nel trigger selezionare **Nuovo passaggio**. Per nascondere i dettagli del trigger, fare clic sulla barra del titolo del trigger.

   ![Aggiungere un altro passaggio al flusso di lavoro dell'app per la logicaAdd another step to your logic app workflow](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. In **Scegliere un'azione**immettere `as2 decode`e selezionare **AS2 Decode (v2)** nella casella di ricerca.

   ![Trovare e selezionare "AS2 Decode (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Per la proprietà **Message to decodecode,** immettere l'input che si `body` desidera venga decodificato dall'azione AS2, ovvero il contenuto ricevuto dal trigger di richiesta HTTP. Sono disponibili diversi modi per specificare questo contenuto come input, dall'elenco del contenuto dinamico o come espressione:You have multiple ways to specify this content as input, either from the dynamic content list or as an expression:

   * Per selezionare da un elenco che mostra gli output di trigger disponibili, fare clic all'interno della casella **Invia a decodifica.** Dopo la visualizzazione dell'elenco di contenuto dinamico, in **Quando viene ricevuta una richiesta HTTP**selezionare Valore della proprietà **Corpo,** ad esempio:

     ![Selezionare il valore "Corpo" dal trigger](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Per immettere un'espressione che `body` fa riferimento all'output del trigger, fare clic all'interno della casella **Messaggio da decodificare.** Quando viene visualizzato l'elenco di contenuto dinamico, selezionare **Espressione**. Nell'editor delle espressioni immettere l'espressione qui e selezionare **OK**:

     `triggerOutputs()['body']`

     In alternativa, nella casella **Messaggio da decodificare** immettere direttamente questa espressione:

     `@triggerBody()`

     L'espressione viene risolta nel token **Body.**

     ![Uscita corpo risolto dal triggerResolved body output from trigger](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Per la proprietà **Intestazioni messaggio** immettere le intestazioni necessarie per `headers` l'azione AS2, descritte dal contenuto ricevuto dal trigger di richiesta HTTP.

   Per immettere un'espressione che `headers` fa riferimento all'output del trigger, fare clic all'interno della casella **Intestazioni messaggio.** Quando viene visualizzato l'elenco di contenuto dinamico, selezionare **Espressione**. Nell'editor delle espressioni immettere l'espressione qui e selezionare **OK**:

   `triggerOutputs()['Headers']`

   Per fare in modo che questa espressione venga risolta come token, passare dalla finestra di progettazione alla visualizzazione codice e alla visualizzazione codice, ad esempio:To get this expression to resolve as this token, switch between the designer and code view, for example:

   ![Output intestazioni risolto dal trigger](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Azione Aggiungi risposta per la notifica di ricezione dei messaggi

Per notificare al partner commerciale che il messaggio è stato ricevuto, è possibile restituire una risposta contenente una notifica di eliminazione messaggi AS2 (MDN) utilizzando l'azione **di risposta.** Aggiungendo questa azione immediatamente dopo l'azione **di decodifica AS2,** se l'azione ha esito negativo, l'app per la logica non continua l'elaborazione.

1. Nell'azione **Decodifica AS2** selezionare **Nuovo passaggio**.

1. In **Scegliere un'azione**, nella casella di ricerca selezionare **Predefinito**. Nella casella di ricerca immettere `condition`. Nell'elenco **Azioni** selezionare **Condizione**.

   ![Aggiungere l'azione "Condizione"](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Ora viene visualizzata la forma della condizione, inclusi i percorsi per verificare se la condizione viene soddisfatta o meno.

   ![Forma condizione con percorsi decisionali](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Specificare ora la condizione da valutare. Nella casella **Scegliere un valore** immettere l'espressione seguente:In the Choose a value box, enter this expression:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Nella casella centrale verificare che l'operazione di confronto sia impostata su `is equal to`. Nella casella a destra immettere `Expected`il valore . Per fare in modo che l'espressione venga risolta come token, passare dalla finestra di progettazione alla visualizzazione codice e alla visualizzazione codice.

   ![Forma condizione con percorsi decisionali](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Specificare ora le risposte da restituire se l'azione **di decodifica AS2** ha esito positivo o meno.

   1. Nel caso in cui l'azione **Decodifica AS2** abbia esito positivo, nella forma **Se true** selezionare **Aggiungi un'azione**. In **Scegliere un'azione**, nella `response`casella di ricerca immettere e selezionare **Risposta**.

      ![Trova e seleziona l'azione "Risposta"](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Per accedere all'AS2 MDN dall'output dell'azione **decodifica AS2,** specificare le espressioni seguenti:

      * Nella proprietà **Intestazioni** dell'azione **Risposta** immettere l'espressione seguente:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Nella proprietà Body dell'azione Risposta immettere l'espressione **seguente:In** the Response action's **Body** property, enter this expression:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Per fare in modo che le espressioni vengano risolte come token, passare dalla visualizzazione finestra di progettazione alla visualizzazione codice e alla visualizzazione codice:

      ![Espressione risolta per accedere a AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Nel caso in cui l'azione **Decodifica AS2** non riesca, nella forma **Se false** selezionare **Aggiungi un'azione**. In **Scegliere un'azione**, nella `response`casella di ricerca immettere e selezionare **Risposta**. Impostare l'azione **Risposta** per restituire lo stato e l'errore desiderati.

1. Salvare l'app per la logica.

## <a name="add-decode-x12-message-action"></a>Azione di invio del messaggio Aggiungi decodifica x12

1. Aggiungere ora l'azione decodifica **del messaggio X12.** Nell'azione **Risposta** selezionare **Aggiungi un'azione.**

1. In **Scegliere un'azione**, nella `x12 decode`casella di ricerca immettere e selezionare **Decodifica messaggio X12**.

   ![Trova e seleziona l'azione "Decodifica messaggio X12"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Se l'azione X12 richiede informazioni di connessione, specificare il nome della connessione, selezionare l'account di integrazione che si desidera utilizzare e quindi selezionare **Crea**.

   ![Creare una connessione X12 all'account di integrazioneCreate X12 connection to integration account](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Specificare ora l'input per l'azione X12. Questo esempio usa l'output dell'azione AS2, ovvero il contenuto del messaggio, ma tieni presente che questo contenuto è in formato oggetto JSON e viene codificato in base64. Quindi, devi convertire questo contenuto in una stringa.

   Nella casella **Messaggio file flat X12 da decodificare** immettere questa espressione per convertire l'output AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Per fare in modo che l'espressione venga risolta come token, passare dalla finestra di progettazione alla visualizzazione codice e alla visualizzazione codice.

    ![Convertire il contenuto con codifica base64 in una stringaConvert base64-encoded content to a string](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Salvare l'app per la logica.

   Se sono necessari passaggi aggiuntivi per questa app per la logica, ad esempio per decodificare il contenuto del messaggio e restituire tale contenuto in formato oggetto JSON, continuare a compilare l'app per la logica.

La configurazione dell'app per la logica B2B è stata completata. In un'app reale, è possibile archiviare i dati X12 decodificati in un'app line-of-business (LOB) o in un archivio dati. Ad esempio, vedere questi articoli:For example, see these articles:

* [Connettersi a sistemi SAP con App per la logica di Azure](../logic-apps/logic-apps-using-sap-connector.md)
* [Monitorare, creare e gestire i file SFTP usando SSH e App per la logica di Azure](../connectors/connectors-sftp-ssh.md)

Per connettere le tue app lob e usare queste API nell'app per la logica, puoi aggiungere altre azioni o [scrivere API personalizzate.](../logic-apps/logic-apps-create-api-app.md)

## <a name="next-steps"></a>Passaggi successivi

* [Ricevere e rispondere alle chiamate HTTPS in arrivo](../connectors/connectors-native-reqres.md)
* [Messaggi di Exchange AS2 per l'integrazione aziendale B2B](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Messaggi X12 di Exchange per l'integrazione aziendale B2B](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Ulteriori informazioni su [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)