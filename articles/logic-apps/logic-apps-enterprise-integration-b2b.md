---
title: Scambiare messaggi per gli scenari di integrazione aziendale B2B
description: Ricevere e inviare messaggi B2B tra partner commerciali in app per la logica di Azure usando il Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: afae49cf6ee44b138a55f58f415fc761308b7894
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542377"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Ricevere e confermare messaggi AS2 B2B usando app per la logica di Azure e Enterprise Integration Pack

Quando si dispone di un account di integrazione che definisce partner commerciali e contratti, è possibile creare un flusso di lavoro business to business (B2B) automatizzato che scambia i messaggi tra partner commerciali usando app per la [logica di Azure](../logic-apps/logic-apps-overview.md) con il [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). App per la logica di Azure funziona con i connettori che supportano i protocolli standard del settore AS2, X12, EDIFACT e RosettaNet. È anche possibile combinare questi connettori con altri [connettori disponibili nelle app](../connectors/apis-list.md)per la logica, ad esempio Salesforce e Office 365 Outlook.

Questo articolo illustra come creare un'app per la logica che riceve una richiesta HTTP usando un trigger di richiesta, decodifica il contenuto del messaggio usando le azioni AS2 e X12, quindi restituisce una risposta usando l'azione di risposta.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, [iscriversi per ottenere un account Azure gratuito](https://azure.microsoft.com/free/).

* Un'app per la logica vuota in modo che sia possibile creare il flusso di lavoro B2B usando il trigger di [richiesta](../connectors/connectors-native-reqres.md) seguito da queste azioni:

  * [Decodifica AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Condizione](../logic-apps/logic-apps-control-flow-conditional-statement.md), che invia una [risposta](../connectors/connectors-native-reqres.md) a seconda che l'azione di decodifica AS2 abbia esito positivo o negativo

  * [Decodifica messaggio X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Se non si ha familiarità con le app per la logica, vedere informazioni sulle [app per la](../logic-apps/logic-apps-overview.md) logica di Azure e [Guida introduttiva: creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [account di integrazione](./logic-apps-enterprise-integration-create-integration-account.md) associato alla sottoscrizione di Azure e collegato all'app per la logica. Sia l'app per la logica che l'account di integrazione devono essere nella stessa località o nella stessa area di Azure.

* Almeno due [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione insieme ai [contratti AS2 e X12](logic-apps-enterprise-integration-agreements.md) per tali partner.

## <a name="add-request-trigger"></a>Aggiungere un trigger di richiesta

Questo esempio usa la finestra di progettazione di app per la logica nella portale di Azure, ma è possibile seguire una procedura simile per la finestra di progettazione di app per la logica in Visual Studio.

1. Nella [portale di Azure](https://portal.azure.com)aprire l'app per la logica vuota nella finestra di progettazione dell'app per la logica.

1. Nella casella di ricerca immettere `when a http request` e selezionare **quando viene ricevuta una richiesta http** da usare come trigger.

   ![Selezionare trigger di richiesta per avviare il flusso di lavoro dell'app per la logica](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Lasciare vuota la casella **dello schema JSON del corpo della richiesta** perché il messaggio X12 è un file flat.

   ![Lascia vuoto "schema JSON del corpo della richiesta"](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Al termine, sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Questo passaggio genera l' **URL http post** da usare per l'invio della richiesta che attiva l'app per la logica. Per copiare questo URL, selezionare l'icona di copia accanto all'URL.

   ![URL generato per il trigger di richiesta per la ricezione di chiamate](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>Aggiungi azione di decodifica AS2

Aggiungere ora le azioni B2B che si vuole usare. In questo esempio vengono utilizzate le azioni AS2 e X12.

1. Nel trigger selezionare **Nuovo passaggio**. Per nascondere i dettagli del trigger, fare clic sulla barra del titolo del trigger.

   ![Aggiungere un altro passaggio al flusso di lavoro dell'app per la logica](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. In **scegliere un'azione**, nella casella di ricerca, immettere `as2 decode` e selezionare **AS2 Decode (v2)**.

   ![Trovare e selezionare "AS2 Decode (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Per la proprietà **Message to decode** , immettere l'input da decodificare nell'azione AS2, ovvero il `body` contenuto ricevuto dal trigger di richiesta HTTP. Sono disponibili diversi modi per specificare questo contenuto come input, dall'elenco di contenuto dinamico o come espressione:

   * Per effettuare una selezione da un elenco che Mostra gli output dei trigger disponibili, fare clic all'interno della casella **messaggio da decodificare** . Quando viene visualizzato l'elenco di contenuto dinamico, in **quando viene ricevuta una richiesta http**, selezionare il valore della proprietà **Body** , ad esempio:

     ![Selezionare il valore "Body" dal trigger](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Per immettere un'espressione che fa riferimento all'output del trigger `body` , fare clic all'interno della casella **messaggio da decodificare** . Quando viene visualizzato l'elenco contenuto dinamico, selezionare **espressione**. Nell'editor espressioni immettere qui l'espressione e selezionare **OK**:

     `triggerOutputs()['body']`

     In alternativa, nella casella **messaggio da decodificare** immettere direttamente questa espressione:

     `@triggerBody()`

     L'espressione viene risolta nel token del **corpo** .

     ![Output del corpo risolto dal trigger](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Per la proprietà **intestazioni messaggio** immettere le intestazioni necessarie per l'azione AS2, descritte dal `headers` contenuto ricevuto dal trigger della richiesta HTTP.

   Per immettere un'espressione che fa riferimento all'output del trigger `headers` , fare clic all'interno della casella **intestazioni messaggio** . Quando viene visualizzato l'elenco contenuto dinamico, selezionare **espressione**. Nell'editor espressioni immettere qui l'espressione e selezionare **OK**:

   `triggerOutputs()['Headers']`

   Per ottenere questa espressione per la risoluzione di questo token, passare dalla finestra di progettazione alla visualizzazione codice, ad esempio:

   ![Output intestazioni risolte dal trigger](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Aggiungi azione di risposta per la notifica di ricezione del messaggio

Per notificare al partner commerciale che è stato ricevuto il messaggio, è possibile restituire una risposta che contiene una notifica di disposizione messaggi AS2 (MDN) utilizzando l'azione di **risposta** . Aggiungendo questa azione immediatamente dopo l'azione **AS2 Decode** , in caso di esito negativo dell'azione, l'app per la logica non continuerà l'elaborazione.

1. Nell'azione **AS2 Decode** selezionare **nuovo passaggio**.

1. In **scegliere un'azione**, nella casella di ricerca, selezionare **predefinito**. Nella casella di ricerca immettere `condition`. Nell'elenco **Azioni** selezionare **Condizione**.

   ![Aggiungere l'azione "Condition"](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Viene ora visualizzata la forma condizione, inclusi i percorsi per verificare se la condizione è soddisfatta o meno.

   ![Screenshot mostra la forma condizione con percorsi vuoti.](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. A questo punto, specificare la condizione da valutare. Nella casella **scegliere un valore** immettere l'espressione seguente:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Nella casella centrale verificare che l'operazione di confronto sia impostata su `is equal to` . Nella casella sul lato destro immettere il valore `Expected` . Per ottenere l'espressione da risolvere come questo token, passare dalla finestra di progettazione alla visualizzazione del codice e viceversa.

   ![Screenshot mostra la forma condizione con una condizione aggiunta.](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Specificare ora le risposte per restituire se l'azione **AS2 Decode** ha esito positivo o negativo.

   1. Nel caso in cui l'azione di **decodifica AS2** abbia esito positivo, nella forma **If true** Selezionare **Aggiungi un'azione**. In **scegliere un'azione**, nella casella di ricerca, immettere `response` e selezionare **risposta**.

      ![Trovare e selezionare l'azione di risposta](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Per accedere al messaggio MDN AS2 dall'output dell'azione di **decodifica AS2** , specificare le espressioni seguenti:

      * Nella proprietà **intestazioni** azione di **risposta** immettere questa espressione:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Nella proprietà **Body** dell'azione di **risposta** immettere questa espressione:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Per ottenere le espressioni da risolvere come token, passare dalla finestra di progettazione alla visualizzazione del codice e viceversa:

      ![Espressione risolta per accedere al messaggio MDN AS2](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Nel caso in cui l'azione **AS2 Decode** abbia esito negativo, nella forma **se false** Selezionare **Aggiungi un'azione**. In **scegliere un'azione**, nella casella di ricerca, immettere `response` e selezionare **risposta**. Configurare l'azione di **risposta** per restituire lo stato e l'errore desiderati.

1. Salvare l'app per la logica.

## <a name="add-decode-x12-message-action"></a>Azione Aggiungi decodifica messaggio X12

1. A questo punto, aggiungere l'azione **Decode X12 Message** . Nell'azione **risposta** selezionare **Aggiungi un'azione**.

1. In **scegliere un'azione**, nella casella di ricerca, immettere `x12 decode` e selezionare **decodifica messaggio X12**.

   ![Trova e seleziona l'azione "decodifica messaggio X12"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Se l'azione X12 richiede informazioni di connessione, specificare il nome per la connessione, selezionare l'account di integrazione che si vuole usare e quindi selezionare **Crea**.

   ![Crea connessione X12 all'account di integrazione](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Specificare ora l'input per l'azione X12. Questo esempio usa l'output dell'azione AS2, ovvero il contenuto del messaggio, ma si noti che questo contenuto è in formato oggetto JSON ed è con codifica Base64. Quindi, è necessario convertire questo contenuto in una stringa.

   Nella casella **messaggio file flat X12 da decodificare** immettere questa espressione per convertire l'output AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Per ottenere l'espressione da risolvere come questo token, passare dalla finestra di progettazione alla visualizzazione del codice e viceversa.

    ![Convertire il contenuto con codifica Base64 in una stringa](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Salvare l'app per la logica.

   Se sono necessari passaggi aggiuntivi per questa app per la logica, ad esempio per decodificare il contenuto del messaggio e restituire il contenuto in formato oggetto JSON, continuare a compilare l'app per la logica.

A questo punto è stata eseguita la configurazione dell'app per la logica B2B. In un'app reale, potrebbe essere necessario archiviare i dati X12 decodificati in un'app line-of-business (LOB) o un archivio dati. Vedere ad esempio gli articoli seguenti:

* [Connettersi a sistemi SAP con App per la logica di Azure](../logic-apps/logic-apps-using-sap-connector.md)
* [Monitorare, creare e gestire i file SFTP usando SSH e App per la logica di Azure](../connectors/connectors-sftp-ssh.md)

Per connettere le app LOB e usare queste API nell'app per la logica, è possibile aggiungere altre azioni o [scrivere API personalizzate](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Passaggi successivi

* [Ricevere e rispondere alle chiamate HTTPS in ingresso](../connectors/connectors-native-reqres.md)
* [Scambiare messaggi AS2 per B2B Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Scambiare messaggi X12 per B2B Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Scopri di più sulla [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
