---
title: Ricevere e rispondere alle chiamate utilizzando HTTPS
description: Gestire le richieste HTTPS in ingresso da servizi esterni usando le app per la logica di AzureHandle inbound HTTPS requests from external services by using Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: d65b81f18d4dcb0ee97a21a7edec885e308bd8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297297"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Ricevere e rispondere alle richieste HTTPS in ingresso nelle app per la logica di AzureReceive and respond to inbound HTTPS requests in Azure Logic Apps

Con [le app per](../logic-apps/logic-apps-overview.md) la logica di Azure e l'azione Richiesta o Risposta incorporata, è possibile creare attività e flussi di lavoro automatizzati che ricevono e rispondono alle richieste HTTPS in ingresso. Ad esempio, puoi avere la tua app per la logica:

* Ricevere e rispondere a una richiesta HTTPS di dati in un database locale.
* Attivare un flusso di lavoro quando si verifica un evento webhook esterno.
* Ricevere e rispondere a una chiamata HTTPS da un'altra app per la logica.

> [!NOTE]
> Il trigger di richiesta supporta *solo* Transport Layer Security (TLS) 1.2 per le chiamate in ingresso. Le chiamate in uscita continuano a supportare TLS 1.0, 1.1 e 1.2. Per ulteriori informazioni, vedere [Risoluzione del problema TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Se vengono visualizzati errori di handshake SSL, assicurarsi di utilizzare TLS 1.2. Per le chiamate in arrivo, ecco le suite di crittografia supportate:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone di una sottoscrizione, è possibile [iscriversi per ottenere un account Azure gratuito.](https://azure.microsoft.com/free/)

* Informazioni di base sulle [app per la logica](../logic-apps/logic-apps-overview.md). Se non si ha familiarità con le app per la logica, scoprire [come creare la prima app per la logica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Aggiungi trigger richiesta

Questo trigger predefinito crea un endpoint HTTPS chiamabile manualmente in grado di ricevere *solo* le richieste HTTPS in ingresso. Quando si verifica questo evento, il trigger viene attivato ed esegue l'app per la logica. Per altre informazioni sulla definizione JSON sottostante del trigger e su come chiamare questo trigger, vedere il tipo di trigger di richiesta e [Chiamare, attivare o annidare flussi di lavoro con endpoint HTTP in App per la logica](../logic-apps/logic-apps-http-endpoint.md)di Azure.For more information about the trigger's underlying JSON definition and how to call this trigger, see the Request trigger [type](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) and Call, trigger, or nest workflows with HTTP endpoints in Azure Logic Apps .

1. Accedere al [portale](https://portal.azure.com)di Azure . Creare un'app per la logica vuota.

1. Dopo l'apertura di Progettazione app per la logica, nella casella di ricerca immettere "richiesta http" come filtro. Nell'elenco dei trigger selezionare il trigger **Quando viene ricevuta una richiesta HTTP,** che è il primo passaggio del flusso di lavoro dell'app per la logica.

   ![Seleziona trigger di richiesta](./media/connectors-native-reqres/select-request-trigger.png)

   Il trigger Richiesta mostra queste proprietà:The Request trigger shows these properties:

   ![Trigger di richiesta](./media/connectors-native-reqres/request-trigger.png)

   | Nome proprietà | Nome della proprietà JSON | Obbligatoria | Descrizione |
   |---------------|--------------------|----------|-------------|
   | **URL POST HTTP** | {none} | Sì | URL dell'endpoint generato dopo aver salvato l'app per la logica e utilizzato per chiamare l'app per la logica |
   | **Schema JSON del corpo della richiestaRequest Body JSON Schema** | `schema` | No | Schema JSON che descrive le proprietà e i valori nel corpo della richiesta in ingresso |
   |||||

1. Nella casella Schema JSON corpo richiesta immettere facoltativamente uno schema JSON che descriva il corpo nella richiesta in ingresso, ad esempio:In the **Request Body JSON Schema** box, optionally enter a JSON schema that describes the body in the incoming request, for example:

   ![Schema JSON di esempio](./media/connectors-native-reqres/provide-json-schema.png)

   La finestra di progettazione utilizza questo schema per generare token per le proprietà nella richiesta. In questo modo, l'app per la logica può analizzare, usare e passare i dati dalla richiesta tramite il trigger nel flusso di lavoro.

   Di seguito è riportato lo schema di esempio:Here is the sample schema:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Quando si immette uno schema JSON, la `Content-Type` finestra di progettazione mostra un `application/json`promemoria per includere l'intestazione nella richiesta e imposta tale valore di intestazione su . Per ulteriori informazioni, consultate [Gestire i tipi di contenuto.](../logic-apps/logic-apps-content-type.md)

   ![Promemoria per includere l'intestazione "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Ecco l'aspetto di questa intestazione in formato JSON:Here's what this header looks like in JSON format:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Per generare uno schema JSON basato sul payload previsto (dati), è possibile usare uno strumento come JSONSchema.net oppure eseguire la procedura seguente:To generate a JSON schema that's based on the expected payload (data), you can use a tool such as [JSONSchema.net](https://jsonschema.net), or you can follow these steps:

   1. Nel trigger di richiesta selezionare **Usare il payload di esempio per generare lo schema**.

      ![Generare lo schema dal payload](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Immettere il payload di esempio e selezionare **Fatto**.

      ![Generare lo schema dal payload](./media/connectors-native-reqres/enter-payload.png)

      Ecco il payload di esempio:Here is the sample payload:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Per specificare proprietà aggiuntive, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri che si desidera aggiungere.

   | Nome proprietà | Nome della proprietà JSON | Obbligatoria | Descrizione |
   |---------------|--------------------|----------|-------------|
   | **Metodo** | `method` | No | Il metodo che la richiesta in ingresso deve usare per chiamare l'app per la logica |
   | **Percorso relativo** | `relativePath` | No | Percorso relativo per il parametro che l'URL dell'endpoint dell'app per la logica può accettare |
   |||||

   In questo esempio viene aggiunta la proprietà **Method:**

   ![Parametro Add Method](./media/connectors-native-reqres/add-parameters.png)

   Il **metodo** proprietà viene visualizzata nel trigger in modo che è possibile selezionare un metodo dall'elenco.

   ![Select (metodo)](./media/connectors-native-reqres/select-method.png)

1. A questo punto, aggiungere un'altra azione come passaggio successivo del flusso di lavoro. Sotto il trigger selezionare **Passaggio successivo** in modo da trovare l'azione che si desidera aggiungere.

   Ad esempio, è possibile rispondere alla richiesta [aggiungendo un'azione Di risposta](#add-response), che consente di restituire una risposta personalizzata ed è descritta più avanti in questo argomento.

   L'app per la logica mantiene aperta la richiesta in ingresso solo per un minuto. Supponendo che il flusso di lavoro dell'app per la logica includa un'azione Response, se l'app per la logica non restituisce una risposta dopo questo periodo di tempo, l'app per la logica restituisce un `504 GATEWAY TIMEOUT` al chiamante. In caso contrario, se l'app per la logica `202 ACCEPTED` non include un'azione Response, l'app per la logica restituisce immediatamente una risposta al chiamante.

1. Al termine, salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**. 

   Questo passaggio genera l'URL da usare per l'invio della richiesta che attiva l'app per la logica. Per copiare l'URL, selezionare l'icona di copia accanto all'URL.

   ![URL per l'attivazione dell'app per la logica](./media/connectors-native-reqres/generated-url.png)

1. Per attivare l'app per la logica, inviare un POST HTTP all'URL generato. Ad esempio, è possibile utilizzare uno strumento come [Postman](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Output dei trigger

Ecco altre informazioni sugli output dal trigger Di richiesta:Here's more information about the outputs from the Request trigger:

| Nome della proprietà JSON | Tipo di dati | Descrizione |
|--------------------|-----------|-------------|
| `headers` | Oggetto | Oggetto JSON che descrive le intestazioni della richiesta |
| `body` | Oggetto | Oggetto JSON che descrive il contenuto del corpo della richiesta |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Aggiungere un'azione di risposta

Puoi usare l'azione Risposta per rispondere con un payload (dati) a una richiesta HTTPS in ingresso, ma solo in un'app per la logica attivata da una richiesta HTTPS. È possibile aggiungere l'azione Risposta in qualsiasi punto del flusso di lavoro. Per ulteriori informazioni sulla definizione JSON sottostante per questo trigger, vedere il [tipo di azione Risposta](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

L'app per la logica mantiene aperta la richiesta in ingresso solo per un minuto. Supponendo che il flusso di lavoro dell'app per la logica includa un'azione Response, se l'app per la logica non restituisce una risposta dopo questo periodo di tempo, l'app per la logica restituisce un `504 GATEWAY TIMEOUT` al chiamante. In caso contrario, se l'app per la logica `202 ACCEPTED` non include un'azione Response, l'app per la logica restituisce immediatamente una risposta al chiamante.

> [!IMPORTANT]
> Se un'azione di risposta include queste intestazioni, App per la logica rimuove queste intestazioni dal messaggio di risposta generato senza visualizzare alcun avviso o errore:If a Response action includes these headers, Logic Apps removes these headers from the generated response message without showing any warning or error:
>
> * `Allow`
> * `Content-*`con queste eccezioni: `Content-Disposition`, `Content-Encoding`, e`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Anche se app per la logica non impedirà di salvare le app per la logica che hanno un'azione di risposta con queste intestazioni, App per la logica ignora queste intestazioni.

1. In Progettazione app per la logica, nel passaggio in cui si vuole aggiungere un'azione Risposta, selezionare **Nuovo passaggio**.

   Ad esempio, utilizzando il trigger di richiesta da prima:

   ![Aggiungi nuovo passaggio](./media/connectors-native-reqres/add-response.png)

   Per aggiungere un'azione tra i passaggi, spostare il puntatore sulla freccia tra questi passaggi. Selezionare il**+** segno più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. In **Scegliere un'azione**immettere "risposta" nella casella di ricerca e selezionare l'azione **Risposta.**

   ![Selezionare l'azione Risposta](./media/connectors-native-reqres/select-response-action.png)

   Il trigger di richiesta è compresso in questo esempio per semplicità.

1. Aggiungere i valori necessari per il messaggio di risposta. 

   In alcuni campi, facendo clic all'interno delle relative caselle viene aperto l'elenco dei contenuti dinamici. È quindi possibile selezionare i token che rappresentano gli output disponibili dai passaggi precedenti del flusso di lavoro. Le proprietà dello schema specificato nell'esempio precedente vengono ora visualizzate nell'elenco del contenuto dinamico.

   Ad esempio, per la `Content-Type` casella **Intestazioni,** includere come nome `application/json` della chiave e impostare il valore della chiave come indicato in precedenza in questo argomento. Per la casella **Corpo,** è possibile selezionare l'output del corpo del trigger dall'elenco del contenuto dinamico.

   ![Dettagli azione risposta](./media/connectors-native-reqres/response-details.png)

   Per visualizzare le intestazioni in formato JSON, selezionare **Passa alla visualizzazione di testo**.

   ![Intestazioni - Passare alla visualizzazione di testo](./media/connectors-native-reqres/switch-to-text-view.png)

   Di seguito sono riportate ulteriori informazioni sulle proprietà che è possibile impostare nell'azione Risposta. 

   | Nome proprietà | Nome della proprietà JSON | Obbligatoria | Descrizione |
   |---------------|--------------------|----------|-------------|
   | **Codice di stato** | `statusCode` | Sì | Il codice di stato da restituire nella risposta |
   | **Intestazioni** | `headers` | No | Oggetto JSON che descrive una o più intestazioni da includere nella risposta |
   | **Corpo** | `body` | No | Il corpo della risposta |
   |||||

1. Per specificare proprietà aggiuntive, ad esempio uno schema JSON per il corpo della risposta, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri che si desidera aggiungere.

1. Al termine, salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**. 

## <a name="next-steps"></a>Passaggi successivi

* [Connettori per App per la logica](../connectors/apis-list.md)
