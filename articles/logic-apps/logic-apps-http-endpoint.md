---
title: Chiamare, attivare o annidare app per la logica
description: Configurare endpoint HTTPS per chiamare, attivare o annidare flussi di lavoro di app per la logica in app per la logica di Azure
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: b5c4005c95a88a40a836b9c0f6d1fd01e0417ed0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84170274"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>Chiamare, attivare o annidare app per la logica usando endpoint HTTPS in app per la logica di Azure

Per fare in modo che l'app per la logica richiamabile tramite un URL in modo che l'app per la logica possa ricevere le richieste in ingresso da altri servizi, è possibile esporre in modo nativo un endpoint HTTPS sincrono come trigger nell'app per la logica. Quando si configura questa funzionalità, è anche possibile annidare l'app per la logica all'interno di altre app per la logica, che consente di creare un modello di endpoint richiamabili.

Per configurare un endpoint chiamabile, è possibile usare uno di questi tipi di trigger, che consentono alle app per la logica di ricevere le richieste in ingresso:

* [Richiesta](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Trigger del connettore gestito che hanno il [tipo ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e possono ricevere le richieste HTTPS in ingresso

> [!NOTE]
> In questi esempi viene usato il trigger request, ma è possibile usare qualsiasi trigger HTTPS basato su richiesta presente nell'elenco precedente. Tutti i principi si applicano in modo identico a questi altri tipi di trigger.

Se non si ha familiarità con le app per la logica, vedere informazioni sulle [app per la](../logic-apps/logic-apps-overview.md) logica di Azure e [avvio rapido: creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* App per la logica in cui si vuole usare il trigger per creare l'endpoint richiamabile. È possibile iniziare con un'app per la logica vuota o un'app per la logica esistente in cui si vuole sostituire il trigger corrente. Questo esempio inizia con un'app per la logica vuota.

## <a name="create-a-callable-endpoint"></a>Creare un endpoint chiamabile

1. Accedere al [portale di Azure](https://portal.azure.com). Creare e aprire un'app per la logica vuota nella finestra di progettazione dell'app per la logica.

   Questo esempio usa il trigger request, ma è possibile usare qualsiasi trigger in grado di ricevere le richieste HTTPS in ingresso. Tutti i principi si applicano in modo identico a questi trigger. Per altre informazioni sul trigger di richiesta, vedere [ricevere e rispondere alle chiamate HTTPS in ingresso usando app per la logica di Azure](../connectors/connectors-native-reqres.md).

1. Nella casella di ricerca selezionare **predefinito**. Nella casella di ricerca immettere `request` come filtro. Dall'elenco trigger selezionare **quando viene ricevuta una richiesta http**.

   ![Trovare e selezionare il trigger di richiesta](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Facoltativamente, nella casella **schema JSON del corpo della richiesta** è possibile immettere uno schema JSON che descrive il payload o i dati che si prevede vengano ricevuti dal trigger.

   La finestra di progettazione utilizza questo schema per generare token che rappresentano output dei trigger. È quindi possibile fare facilmente riferimento a questi output nel flusso di lavoro dell'app per la logica. Altre informazioni sui [token generati dagli schemi JSON](#generated-tokens).

   Per questo esempio, immettere questo schema:

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

   ![Fornire lo schema JSON per l'azione di richiesta](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   In alternativa, è possibile generare uno schema JSON fornendo un payload di esempio:

   1. Nel trigger **richiesta** selezionare **USA payload di esempio per generare lo schema**.

   1. Nella casella **immettere o incollare un payload JSON di esempio** immettere il payload di esempio, ad esempio:

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

   1. Quando si è pronti, selezionare **fine**.

      Nella casella **dello schema JSON del corpo della richiesta** è ora visualizzato lo schema generato.

1. Salvare l'app per la logica.

   La casella **URL post http** Mostra ora l'URL di callback generato che altri servizi possono usare per chiamare e attivare l'app per la logica. Questo URL include i parametri di query che specificano una chiave di firma di accesso condiviso (SAS), che viene usata per l'autenticazione.

   ![URL di callback generato per endpoint](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. Per copiare l'URL di callback, sono disponibili le opzioni seguenti:

   * A destra della casella **URL http post** selezionare **Copia URL** (icona copia file).

   * Eseguire la chiamata POST:

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * Copiare l'URL di callback dal riquadro di **Panoramica** dell'app per la logica.

     1. Scegliere **Panoramica**dal menu dell'app per la logica.

     1. Nella sezione **Riepilogo** selezionare **Visualizza cronologia trigger**.

        ![Ottenere l'URL dell'endpoint da portale di Azure](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. In **URL callback [post]** copiare l'URL:

        ![Copia l'URL dell'endpoint da portale di Azure](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>Seleziona metodo di richiesta previsto

Per impostazione predefinita, il trigger di richiesta prevede una richiesta POST. È possibile specificare un metodo diverso da prevedere, ma solo un singolo metodo.

1. Nel trigger request aprire l'elenco **Aggiungi nuovo parametro** e selezionare **Metodo**, che aggiunge questa proprietà al trigger.

   ![Aggiungere la proprietà "Method" al trigger](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Dall'elenco **Metodo** selezionare il metodo che deve essere previsto dal trigger. In alternativa, è possibile specificare un metodo personalizzato.

   Ad esempio, selezionare il metodo **Get** per poter testare l'URL dell'endpoint in un secondo momento.

   ![Selezionare il metodo di richiesta previsto dal trigger](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>Passare i parametri tramite l'URL dell'endpoint

Quando si desidera accettare valori di parametro tramite l'URL dell'endpoint, sono disponibili le opzioni seguenti:

* [Accettare i valori tramite i parametri Get](#get-parameters) o URL.

  Questi valori vengono passati come coppie nome/valore nell'URL dell'endpoint. Per questa opzione, è necessario usare il metodo GET nel trigger di richiesta. In un'azione successiva, è possibile ottenere i valori dei parametri come output del trigger tramite la `triggerOutputs()` funzione in un'espressione.

* [Accettare i valori tramite un percorso relativo](#relative-path) per i parametri nel trigger di richiesta.

  Questi valori vengono passati tramite un percorso relativo nell'URL dell'endpoint. È anche necessario selezionare in modo esplicito [il metodo](#select-method) previsto dal trigger. In un'azione successiva, è possibile ottenere i valori dei parametri come output del trigger facendo riferimento direttamente a questi output.

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>Accetta valori tramite parametri GET

1. Nel trigger di richiesta aprire l' **elenco Aggiungi nuovo parametro**, aggiungere la proprietà del **Metodo** al trigger e selezionare il metodo **Get** .

   Per altre informazioni, vedere [selezionare il metodo di richiesta previsto](#select-method).

1. Nel trigger request aggiungere l'azione in cui si desidera utilizzare il valore del parametro. Per questo esempio, aggiungere l'azione di **risposta** .

   1. Nel trigger richiesta selezionare **nuovo passaggio**  >  **Aggiungi un'azione**.
   
   1. Nella casella di ricerca di **Scegliere un'azione** immettere `response` come filtro. Nell'elenco azioni selezionare l'azione **risposta** .

1. Per compilare l' `triggerOutputs()` espressione che recupera il valore del parametro, attenersi alla procedura seguente:

   1. Fare clic all'interno della proprietà **Body** dell'azione di risposta in modo che venga visualizzato l'elenco di contenuto dinamico e selezionare **Expression**.

   1. Nella casella **espressione** immettere questa espressione, sostituendo `parameter-name` con il nome del parametro, quindi fare clic su **OK**.

      `triggerOutputs()['queries']['parameter-name']`

      ![Aggiungere l'espressione "triggerOutputs ()" da attivare](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      Nella proprietà **Body** l'espressione viene risolta nel `triggerOutputs()` token.

      ![Espressione "triggerOutputs ()" risolta](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      Se si salva l'app per la logica, uscire dalla finestra di progettazione e tornare alla finestra di progettazione, il token Visualizza il nome del parametro specificato, ad esempio:

      ![Espressione risolta per il nome del parametro](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      Nella visualizzazione codice la proprietà **Body** viene visualizzata nella definizione dell'azione di risposta, come indicato di seguito:

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      Si supponga, ad esempio, di voler passare un valore per un parametro denominato `postalCode` . La proprietà **Body** specifica la stringa, `Postal Code: ` con uno spazio finale, seguita dall'espressione corrispondente:

      ![Aggiungere l'espressione "triggerOutputs ()" di esempio da attivare](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. Per testare l'endpoint chiamabile, copiare l'URL di callback dal trigger di richiesta e incollare l'URL in un'altra finestra del browser. Nell'URL aggiungere il nome e il valore del parametro dopo il punto interrogativo ( `?` ) all'URL nel formato seguente e premere INVIO.

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   Il browser restituisce una risposta con il testo seguente:`Postal Code: 123456`

   ![Risposta dall'invio della richiesta all'URL callback](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. Per inserire il nome e il valore del parametro in una posizione diversa all'interno dell'URL, assicurarsi di utilizzare la e commerciale ( `&` ) come prefisso, ad esempio:

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   Questo esempio mostra l'URL di callback con il nome e il valore del parametro `postalCode=123456` di esempio in posizioni diverse all'interno dell'URL:

   * 1 ° posizione:`https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * seconda posizione:`https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

> [!NOTE]
> Se si desidera includere il simbolo hash o Pound ( **#** ) nell'URI, utilizzare invece questa versione codificata:`%25%23`

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>Accetta valori tramite un percorso relativo

1. Nel trigger request aprire l'elenco **Aggiungi nuovo parametro** e selezionare **percorso relativo**, che aggiunge questa proprietà al trigger.

   ![Aggiungere la proprietà "percorso relativo" al trigger](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Nella proprietà **percorso relativo** specificare il percorso relativo del parametro nello schema JSON che si desidera venga accettato dall'URL, ad esempio `/address/{postalCode}` .

   ![Specificare il percorso relativo per il parametro](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Nel trigger request aggiungere l'azione in cui si desidera utilizzare il valore del parametro. Per questo esempio, aggiungere l'azione di **risposta** .

   1. Nel trigger richiesta selezionare **nuovo passaggio**  >  **Aggiungi un'azione**.

   1. Nella casella di ricerca di **Scegliere un'azione** immettere `response` come filtro. Nell'elenco azioni selezionare l'azione **risposta** .

1. Nella proprietà **Body** dell'azione di risposta includere il token che rappresenta il parametro specificato nel percorso relativo del trigger.

   Si supponga, ad esempio, di voler restituire l'azione di risposta `Postal Code: {postalCode}` .

   1. Nella proprietà **Body** immettere `Postal Code: ` con uno spazio finale. Mantenere il cursore all'interno della casella di modifica in modo che l'elenco di contenuto dinamico rimanga aperto.

   1. Nell'elenco contenuto dinamico, nella sezione **quando viene ricevuta una richiesta http** , selezionare il token **Cap** .

      ![Aggiungere il parametro specificato al corpo della risposta](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      La proprietà **Body** include ora il parametro selezionato:

      ![Esempio di corpo della risposta con parametro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Salvare l'app per la logica.

   Nel trigger di richiesta, l'URL di callback viene aggiornato e ora include il percorso relativo, ad esempio:

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. Per testare l'endpoint richiamabile, copiare l'URL di callback aggiornato dal trigger di richiesta, incollare l'URL in un'altra finestra del browser, sostituire l'URL `{postalCode}` con `123456` e premere INVIO.

   Il browser restituisce una risposta con il testo seguente:`Postal Code: 123456`

   ![Risposta dall'invio della richiesta all'URL callback](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

> [!NOTE]
> Se si desidera includere il simbolo hash o Pound ( **#** ) nell'URI, utilizzare invece questa versione codificata:`%25%23`

## <a name="call-logic-app-through-endpoint-url"></a>Chiamare l'app per la logica tramite l'URL dell'endpoint

Dopo aver creato l'endpoint, è possibile attivare l'app per la logica inviando una `POST` richiesta HTTPS all'URL completo dell'endpoint. Le app per la logica dispongono di supporto incorporato per gli endpoint di accesso diretto.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Token generati dallo schema

Quando si specifica uno schema JSON nel trigger di richiesta, la finestra di progettazione dell'app per la logica genera i token per le proprietà nello schema. È quindi possibile usare tali token per passare dati tramite il flusso di lavoro di app per la logica.

Ad esempio, se si aggiungono altre proprietà, ad esempio `"suite"` , allo schema JSON, i token per tali proprietà sono disponibili per l'uso nei passaggi successivi per l'app per la logica. Di seguito è riportato lo schema JSON completo:

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

È possibile nidificare i flussi di lavoro nell'app per la logica aggiungendo altre app per la logica che possono ricevere richieste. Per includere queste app per la logica, seguire questa procedura:

1. Nel passaggio in cui si vuole chiamare un'altra app per la logica, selezionare **nuovo passaggio**  >  **Aggiungi un'azione**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere `logic apps` come filtro. Nell'elenco azioni selezionare **Scegli un flusso di lavoro di app per la logica**.

   ![Annidare app per la logica nell'app per la logica corrente](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   La finestra di progettazione Mostra le app per la logica idonee per la selezione.

1. Selezionare l'app per la logica da chiamare dall'app per la logica corrente.

   ![Selezionare l'app per la logica da chiamare dall'app per la logica corrente](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Riferimento al contenuto dalla richiesta in ingresso

Se il tipo di contenuto della richiesta in ingresso è `application/json` , è possibile fare riferimento alle proprietà nella richiesta in ingresso. In caso contrario, questo contenuto viene considerato come una singola unità binaria che è possibile passare ad altre API. Per fare riferimento a questo contenuto all'interno del flusso di lavoro dell'app per la logica, è necessario innanzitutto convertire il contenuto.

Se ad esempio si passa contenuto con `application/xml` tipo, è possibile usare l' [ `@xpath()` espressione](../logic-apps/workflow-definition-language-functions-reference.md#xpath) per eseguire un'estrazione XPath oppure usare l' [ `@json()` espressione](../logic-apps/workflow-definition-language-functions-reference.md#json) per la conversione di XML in JSON. Altre informazioni sull'uso dei [tipi di contenuto](../logic-apps/logic-apps-content-type.md)supportati.

Per ottenere l'output da una richiesta in ingresso, è possibile usare l' [ `@triggerOutputs` espressione](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Si supponga, ad esempio, di avere un output simile all'esempio seguente:

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

Per accedere in modo specifico alla `body` proprietà, è possibile usare l' [ `@triggerBody()` espressione](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) come collegamento.

## <a name="respond-to-requests"></a>Rispondere alle richieste

A volte si vuole rispondere a determinate richieste che attivano l'app per la logica restituendo contenuto al chiamante. Per costruire il codice di stato, l'intestazione e il corpo per la risposta, usare l'azione di risposta. Questa azione può trovarsi in qualsiasi punto nell'app per la logica, non solo alla fine del flusso di lavoro. Se l'app per la logica non include un'azione di risposta, l'endpoint risponde *immediatamente* con lo stato **202 accettato** .

Affinché il chiamante originale ottenga correttamente la risposta, tutti i passaggi necessari per la risposta devono terminare entro il [limite di timeout della richiesta](./logic-apps-limits-and-config.md) , a meno che l'app per la logica attivata non venga chiamata come app per la logica annidata. Se non viene restituita alcuna risposta entro questo limite, la richiesta in ingresso scade e riceve la risposta di **timeout del Client 408** .

Per le app per la logica annidate, l'app per la logica padre continua ad attendere una risposta fino al completamento di tutti i passaggi, indipendentemente dal tempo necessario.

### <a name="construct-the-response"></a>Costruire la risposta

Nel corpo della risposta è possibile includere più intestazioni e qualsiasi tipo di contenuto. Ad esempio, l'intestazione della risposta specifica che il tipo di contenuto della risposta è `application/json` e che il corpo contiene valori per `town` le `postalCode` proprietà e, in base allo schema JSON descritto in precedenza in questo argomento per il trigger request.

![Fornire il contenuto della risposta per l'azione di risposta HTTPS](./media/logic-apps-http-endpoint/content-for-response-action.png)

Le risposte hanno le seguenti proprietà:

| Proprietà (visualizzazione) | Proprietà (JSON) | Descrizione |
|--------------------|-----------------|-------------|
| **Codice di stato** | `statusCode` | Codice di stato HTTPS da usare nella risposta per la richiesta in ingresso. Può essere qualsiasi codice di stato valido che inizia con 2xx, 4xx o 5xx. I codici di stato 3xx non sono consentiti. |
| **Intestazioni** | `headers` | Una o più intestazioni da includere nella risposta |
| **Corpo** | `body` | Oggetto Body che può essere una stringa, un oggetto JSON o anche contenuto binario a cui si fa riferimento da un passaggio precedente |
||||

Per visualizzare la definizione JSON per l'azione di risposta e la definizione JSON completa dell'app per la logica, nella barra degli strumenti di progettazione app per la logica selezionare **visualizzazione codice**.

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

**R**: Azure genera in modo sicuro gli URL di callback delle app per la logica usando la [firma di accesso condiviso (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature). Questa firma viene passata come parametro di query e deve essere convalidata prima di poter eseguire l'app per la logica. Azure genera la firma con una combinazione univoca che include la chiave privata per ogni app per la logica, il nome del trigger e l'operazione in esecuzione. Pertanto, a meno che un utente non ottenga l'accesso alla chiave privata dell'app per la logica, non potrà generare una firma valida.

> [!IMPORTANT]
> Per i sistemi di produzione e di sicurezza più elevati, è consigliabile non chiamare l'app per la logica direttamente dal browser per i motivi seguenti:
>
> * La chiave di accesso condiviso viene visualizzata nell'URL.
> * Non è possibile gestire i criteri di contenuto di sicurezza a causa di domini condivisi in app per la logica di Azure.

#### <a name="q-can-i-configure-callable-endpoints-further"></a>D: è possibile configurare ulteriormente gli endpoint chiamabili?

**R**: Sì, gli endpoint HTTPS supportano una configurazione più avanzata tramite [gestione API di Azure](../api-management/api-management-key-concepts.md). Questo servizio offre inoltre la possibilità di gestire tutte le API in modo coerente, incluse le app per la logica, di impostare i nomi di dominio personalizzato, usare più metodi di autenticazione e altro ancora, ad esempio:

* [Impostare il metodo della richiesta](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Modificare i segmenti dell'URL della richiesta](../api-management/api-management-transformation-policies.md#RewriteURL)
* Configurare i domini di gestione API nel [portale di Azure](https://portal.azure.com/)
* Impostare la norma per verificare l'autenticazione di base

## <a name="next-steps"></a>Passaggi successivi

* [Ricevere e rispondere alle chiamate HTTPS in ingresso usando app per la logica di Azure](../connectors/connectors-native-reqres.md)
