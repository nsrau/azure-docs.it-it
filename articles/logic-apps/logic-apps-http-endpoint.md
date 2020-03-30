---
title: Chiamare, attivare o annidare app per la logica
description: Configurare gli endpoint HTTP per chiamare, attivare o annidare i flussi di lavoro delle app per la logica nelle app per la logica di AzureSet up HTTP endpoints to call, trigger, or nest logic app workflows in Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191343"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Chiamare, attivare o annidare app per la logica usando gli endpoint HTTP nelle app per la logica di AzureCall, trigger, or nest logic apps by using HTTP endpoints in Azure Logic Apps

Per rendere l'app per la logica richiamabile tramite un URL in modo che l'app per la logica possa ricevere richieste in ingresso da altri servizi, è possibile esporre in modo nativo un endpoint HTTP sincrono come trigger in tale app per la logica. Quando configuri questa funzionalità, puoi anche annidare l'app per la logica all'interno di altre app per la logica, che ti consente di creare un modello di endpoint chiamabili.

Per configurare un endpoint HTTP, è possibile usare uno di questi tipi di trigger, che consentono alle app per la logica di ricevere le richieste in ingresso:To set up an HTTP endpoint, you can use any of these trigger types, which enable logic apps to receive incoming requests:

* [Richiesta](../connectors/connectors-native-reqres.md)
* [Webhook HTTP](../connectors/connectors-native-webhook.md)
* Trigger del connettore gestito con [il tipo ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e in grado di ricevere richieste HTTP in ingresso

> [!NOTE]
> In questi esempi viene usato il trigger Richiesta, ma è possibile usare qualsiasi trigger basato su richiesta HTTP presente nell'elenco precedente. Tutti i principi si applicano in modo identico a questi altri tipi di trigger.

Se non si ha familiarità con le app per la logica, vedere [Che cos'è App per](../logic-apps/logic-apps-overview.md) la logica di Azure e Guida [introduttiva: Creare la prima app per la logica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* App per la logica in cui si vuole configurare l'endpoint HTTP come trigger. Puoi iniziare con un'app per la logica vuota o un'app per la logica esistente in cui vuoi sostituire il trigger corrente. Questo esempio inizia con un'app per la logica vuota.

## <a name="create-a-callable-endpoint"></a>Creare un endpoint richiamabileCreate a callable endpoint

1. Accedere al [portale](https://portal.azure.com)di Azure . Creare e aprire un'app per la logica vuota in Progettazione app per la logica.

   In questo esempio viene utilizzato il trigger Request, ma è possibile utilizzare qualsiasi trigger in grado di ricevere richieste HTTP in ingresso. Tutti i principi si applicano in modo identico a questi fattori scatenanti. Per altre informazioni sul trigger di richiesta, vedere [Ricevere e rispondere alle chiamate HTTPS in ingresso tramite App per la logica](../connectors/connectors-native-reqres.md)di Azure.For more information about the Request trigger, see Receive and respond to incoming HTTPS calls by using Azure Logic Apps.

1. Nella casella di ricerca selezionare **Predefinito**. Nella casella di ricerca immettere `request` come filtro. Nell'elenco dei trigger selezionare **Quando viene ricevuta una richiesta HTTP.**

   ![Trovare e selezionare il trigger Richiesta](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Facoltativamente, nella casella **Schema JSON corpo richiesta** è possibile immettere uno schema JSON che descrive il payload o i dati che si prevede che il trigger riceva.

   La finestra di progettazione utilizza questo schema per generare token che rappresentano gli output dei trigger. È quindi possibile fare facilmente riferimento a questi output in tutto il flusso di lavoro dell'app per la logica. Altre informazioni sui [token generati dagli schemi JSON.](#generated-tokens)

   Per questo esempio, immettere questo schema:For this example, enter this schema:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Fornire lo schema JSON per l'azione RichiediProvide JSON schema for the Request action](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   In alternativa, è possibile generare uno schema JSON fornendo un payload di esempio:Or, you can generate a JSON schema by providing a sample payload:

   1. Nel trigger **Richiesta** selezionare **Usa payload di esempio per generare lo schema.**

   1. Nella casella **Immettere o incollare un payload JSON di esempio** immettere il payload di esempio, ad esempio:In the Enter or paste a sample JSON payload box, enter your sample payload, for example:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Quando si è pronti, selezionare **Fatto**.

      Nella casella **Schema JSON corpo richiesta** viene ora visualizzato lo schema generato.

1. Salvare l'app per la logica.

   La casella **HTTP POST a questo URL** ora mostra l'URL di callback generato che altri servizi possono usare per chiamare e attivare l'app per la logica. Questo URL include una chiave firma di accesso condiviso (SAS, Shared Access Signature), utilizzata per l'autenticazione, nei parametri di query, ad esempio:This URL includes a Shared Access Signature (SAS) key, which is used for authentication, in the query parameters, for example:

   ![URL di callback generato per endpoint](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   È anche possibile ottenere l'URL dell'endpoint HTTP dal riquadro **Panoramica** dell'app per la logica.

   1. Nel menu dell'app per la logica selezionare **Panoramica**.

   1. Nella sezione **Riepilogo** selezionare **Visualizza cronologia trigger**.

      ![Ottenere l'URL dell'endpoint HTTP GET dal portale di Azure](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. In **URL di richiamata [POST]** copiare l'URL:

      ![Copia URL endpoint HTTP dal portale di AzureCopy HTTP endpoint URL from Azure portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Oppure è possibile ottenere l'URL mediante questa chiamata:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Impostare il metodo HTTP previsto

Per impostazione predefinita il trigger Request prevede una richiesta HTTP POST. Tuttavia, è possibile specificare un metodo diverso da aspettarsi, ma solo un metodo.

1. Nel trigger di richiesta aprire l'elenco **Aggiungi nuovo parametro** e selezionare **Metodo**, che aggiunge questa proprietà al trigger.

   ![Aggiungere la proprietà "Metodo" al triggerAdd "Method" property to trigger](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Nell'elenco **Metodo** selezionare invece un altro metodo previsto dal trigger. In alternativa, è possibile specificare un metodo personalizzato.

   Ad esempio, selezionare il metodo **GET** in modo da poter testare l'URL dell'endpoint HTTP in un secondo momento.

   ![Selezionare il metodo HTTP da utilizzare per il triggerSelect HTTP method to use for trigger](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Accettare i parametri nell'URL dell'endpoint

Quando si desidera che l'URL dell'endpoint accetti i parametri, specificare il percorso relativo nel trigger. È inoltre necessario [impostare](#set-method) in modo esplicito il metodo previsto dalla richiesta HTTP.

1. Nel trigger Di richiesta aprire l'elenco **Aggiungi nuovo parametro** e selezionare **Percorso relativo**, che aggiunge questa proprietà al trigger.

   ![Aggiungere la proprietà "Percorso relativo" per attivare](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Nella proprietà **Percorso relativo** specificare il percorso relativo del parametro nello schema `address/{postalCode}`JSON che si desidera venga accettato dall'URL, ad esempio .

   ![Specificare il percorso relativo per il parametro](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Per usare il parametro, trovare e aggiungere un'azione **Risposta** all'app per la logica.

   1. Nel trigger Richiesta selezionare **Nuovo passaggio** > **Aggiungi un'azione**.

   1. Nella casella di ricerca di **Scegliere un'azione** immettere `response` come filtro.

   1. Nell'elenco delle azioni selezionare l'azione **Risposta.**

1. Nella proprietà **Body** dell'azione Risposta includere il token che rappresenta il parametro specificato nel percorso relativo del trigger.

   Si supponga, ad esempio, che `Postal Code: {postalCode}`si desideri che l'azione Risposta restituisca .

   Nella proprietà **Corpo,** immettere `Postal Code: ` uno spazio finale. Nell'elenco di contenuto dinamico visualizzato selezionare il token **postalCode.**

   ![Aggiungere il parametro specificato al corpo della risposta](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   La proprietà **Body** ora include il parametro selezionato:

   ![Corpo della risposta di esempio con parametroExample response body with parameter](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Salvare l'app per la logica.

    L'URL dell'endpoint HTTP include ora il percorso relativo, ad esempio:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Per testare l'endpoint HTTP, copiare e incollare l'URL aggiornato in un'altra finestra del browser, ma sostituire `{postalCode}` con `123456` e premere Invio.

   Il browser mostra questo testo:`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Chiamare l'app per la logica tramite l'endpoint HTTPCall logic app through HTTP endpoint

Dopo aver creato l'endpoint HTTP, è possibile `POST` attivare l'app per la logica inviando una richiesta HTTP all'URL completo dell'endpoint. Le app per la logica dispongono di supporto incorporato per gli endpoint di accesso diretto.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Token generati dallo schemaTokens generated from schema

Quando si fornisce uno schema JSON nel trigger di richiesta, Progettazione app per la logica genera token per le proprietà in tale schema. È quindi possibile usare tali token per passare dati tramite il flusso di lavoro di app per la logica.

Ad esempio, se si aggiungono `"suite"`altre proprietà, ad esempio , allo schema JSON, i token per tali proprietà sono disponibili per l'utilizzo nei passaggi successivi per l'app per la logica. Di seguito è riportato lo schema JSON completo:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Creare app per la logica annidate

È possibile nidificare i flussi di lavoro nell'app per la logica aggiungendo altre app per la logica che possono ricevere richieste. Per includere queste app per la logica, attenersi alla seguente procedura:

1. Nel passaggio in cui si vuole chiamare un'altra app per la logica selezionare **Nuovo passaggio** > **Aggiungi un'azione**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere `logic apps` come filtro. Nell'elenco delle azioni selezionare **Scegli un flusso di lavoro app per la logica**.

   ![App per la logica Nest all'interno dell'app per la logica correnteNest logic app inside current logic app](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   La finestra di progettazione mostra le app per la logica idonee da selezionare.

1. Selezionare l'app per la logica da chiamare dall'app per la logica corrente.

   ![Selezionare l'app per la logica da chiamare dall'app per la logica corrente](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Riferimento al contenuto dalla richiesta in ingresso

Se il tipo di contenuto `application/json`della richiesta in ingresso è , è possibile fare riferimento alle proprietà nella richiesta in ingresso. In caso contrario, questo contenuto viene considerato come una singola unità binaria che è possibile passare ad altre API. Per fare riferimento a questo contenuto all'interno del flusso di lavoro dell'app per la logica, devi prima convertire tale contenuto.

Ad esempio, se si passa `application/xml` contenuto con tipo, è possibile usare [ `@xpath()` l'espressione](../logic-apps/workflow-definition-language-functions-reference.md#xpath) per eseguire un'estrazione XPath oppure usare [ `@json()` l'espressione](../logic-apps/workflow-definition-language-functions-reference.md#json) per convertire XML in JSON. Ulteriori informazioni sull'utilizzo dei [tipi di contenuto](../logic-apps/logic-apps-content-type.md)supportati.

Per ottenere l'output da una richiesta in ingresso, è possibile utilizzare [ `@triggerOutputs` l'espressione](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Si supponga, ad esempio, di avere un output simile a questo esempio:For example, suppose you have output that looks like this example:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

Per accedere `body` in modo specifico alla proprietà, è possibile utilizzare [ `@triggerBody()` l'espressione](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) come collegamento.

## <a name="respond-to-requests"></a>Rispondere alle richieste

A volte vuoi rispondere a determinate richieste che attivano l'app per la logica restituendo contenuto al chiamante. Per costruire il codice di stato, l'intestazione e il corpo per la risposta, usare l'azione Risposta.To construct the status code, header, and body for your response, use the Response action. Questa azione può trovarsi in qualsiasi punto nell'app per la logica, non solo alla fine del flusso di lavoro. Se l'app per la logica non include un'azione Response, l'endpoint HTTP risponde immediatamente con lo stato **202 Accepted.If** your logic app doesn't include a Response action, the HTTP endpoint responds *immediately* with the 202 Accepted status.

Affinché il chiamante originale otturi correttamente la risposta, tutti i passaggi necessari per la risposta devono terminare entro il limite di timeout della [richiesta,](./logic-apps-limits-and-config.md) a meno che l'app per la logica attivata non venga chiamata come app per la logica annidata. Se non viene restituita alcuna risposta entro questo limite, la richiesta in ingresso scade e riceve la risposta **di timeout 408 Client.**

Per le app per la logica annidate, l'app per la logica padre continua ad attendere una risposta fino al completamento di tutti i passaggi, indipendentemente dalla quantità di tempo necessaria.

### <a name="construct-the-response"></a>Costruire la risposta

Nel corpo della risposta è possibile includere più intestazioni e qualsiasi tipo di contenuto. Ad esempio, l'intestazione di questa risposta specifica `application/json` che il tipo di `town` contenuto `postalCode` della risposta è e che il corpo contiene valori per le proprietà e , in base allo schema JSON descritto in precedenza in questo argomento per il trigger di richiesta.

![Fornire il contenuto della risposta per l'azione Risposta HTTPProvide Response content for HTTP Response action](./media/logic-apps-http-endpoint/content-for-response-action.png)

Le risposte hanno le seguenti proprietà:

| Proprietà (display) | Property (JSON) | Descrizione |
|--------------------|-----------------|-------------|
| **Codice di stato** | `statusCode` | Codice di stato HTTP da utilizzare nella risposta per la richiesta in ingresso. Può essere qualsiasi codice di stato valido che inizia con 2xx, 4xx o 5xx. I codici di stato 3xx non sono consentiti. |
| **Intestazioni** | `headers` | Una o più intestazioni da includere nella risposta |
| **Corpo** | `body` | Oggetto body che può essere una stringa, un oggetto JSON o anche contenuto binario a cui viene fatto riferimento da un passaggio precedente |
||||

Per visualizzare la definizione JSON per l'azione Response e la definizione JSON completa dell'app per la logica, nella barra degli strumenti di Progettazione app per la logica selezionare **Visualizzazione codice**.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Domande e risposte

#### <a name="q-what-about-url-security"></a>D: Come viene garantita la sicurezza degli URL?

**R:** Azure genera in modo sicuro gli URL di callback dell'app per la logica usando la firma di [accesso condiviso.](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature) Questa firma passa attraverso come parametro di query e deve essere convalidata prima che l'app per la logica possa essere eseguita. Azure genera la firma con una combinazione univoca che include la chiave privata per ogni app per la logica, il nome del trigger e l'operazione in esecuzione. Pertanto, a meno che un utente non ottenga l'accesso alla chiave privata dell'app per la logica, non potrà generare una firma valida.

> [!IMPORTANT]
> Per i sistemi di produzione e di sicurezza superiore, è consigliabile non chiamare l'app per la logica direttamente dal browser per i motivi seguenti:For production and higher security systems, we strongly sadvise against calling your logic app directly from the browser for these reasons:
>
> * La chiave di accesso condiviso viene visualizzata nell'URL.
> * Non è possibile gestire i criteri del contenuto di sicurezza a causa di domini condivisi tra i clienti di App per la logica di Azure.You can't manage security content policies due to shared domains across Azure Logic Apps customers.

#### <a name="q-can-i-configure-http-endpoints-further"></a>D: È possibile configurare ulteriormente gli endpoint HTTP?

**R:** Sì, gli endpoint HTTP supportano una configurazione più avanzata tramite Gestione API di [Azure.](../api-management/api-management-key-concepts.md) Questo servizio offre inoltre la possibilità di gestire tutte le API in modo coerente, incluse le app per la logica, di impostare i nomi di dominio personalizzato, usare più metodi di autenticazione e altro ancora, ad esempio:

* [Impostare il metodo della richiesta](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Modificare i segmenti dell'URL della richiesta](../api-management/api-management-transformation-policies.md#RewriteURL)
* Configurare i domini di Gestione API nel portale di [AzureSet](https://portal.azure.com/) up your API Management domains in the Azure portal
* Impostare la norma per verificare l'autenticazione di base

## <a name="next-steps"></a>Passaggi successivi

* [Ricevere e rispondere alle chiamate HTTPS in arrivo usando le app per la logica di AzureReceive and respond to incoming HTTPS calls by using Azure Logic Apps](../connectors/connectors-native-reqres.md)