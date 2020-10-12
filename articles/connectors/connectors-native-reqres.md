---
title: Ricevere e rispondere alle chiamate tramite HTTPS
description: Gestire le richieste HTTPS in ingresso da servizi esterni tramite App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: 05ce944d195cf43f860fc2b39975a736a4454c05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89226515"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Ricevere e rispondere alle richieste HTTPS in ingresso in App per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e il trigger di richiesta e l'azione di risposta predefiniti è possibile creare attività e flussi di lavoro automatizzati in grado di ricevere richieste in ingresso tramite HTTPS. Per inviare le richieste in uscita, usare il [trigger http incorporato o l'azione http](../connectors/connectors-native-http.md).

Ad esempio, è possibile far sì che l'app per la logica:

* Riceva e risponda a una richiesta HTTPS di dati in un database locale.

* Attivi un flusso di lavoro quando si verifica un evento del webhook esterno.

* Riceva e risponda a una chiamata HTTPS da un'altra app per la logica.

Questo articolo illustra come usare il trigger di richiesta e l'azione di risposta in modo che l'app per la logica possa ricevere e rispondere alle chiamate in ingresso.

Per informazioni su crittografia, sicurezza e autorizzazione per le chiamate in ingresso all'app per la logica, ad esempio [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), precedentemente noto come Secure Sockets Layer (SSL) o [Azure Active Directory Open Authentication (Azure ad OAuth)](../active-directory/develop/index.yml), vedere [accesso protetto e accesso ai dati per le chiamate in ingresso a trigger basati su richiesta](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, vedere informazioni sulle [app per la logica di Azure](../logic-apps/logic-apps-overview.md)

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Aggiungere un trigger di richiesta

Questo trigger predefinito crea un endpoint richiamabile manualmente in grado di gestire *solo* le richieste in ingresso su HTTPS. Quando un chiamante invia una richiesta a questo endpoint, il [trigger di richiesta](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) viene attivato ed esegue l'app per la logica. Per altre informazioni su come chiamare questo trigger, vedere [chiamare, attivare o annidare flussi di lavoro con endpoint HTTPS in app per la logica di Azure](../logic-apps/logic-apps-http-endpoint.md).

L'app per la logica mantiene aperta una richiesta in ingresso solo per un [periodo di tempo limitato](../logic-apps/logic-apps-limits-and-config.md#request-limits). Supponendo che l'app per la logica includa un' [azione di risposta](#add-response), se l'app per la logica non invia una risposta al chiamante dopo che questo tempo viene superato, l'app per la logica restituisce uno `504 GATEWAY TIMEOUT` stato al chiamante. Se l'app per la logica non include un'azione di risposta, 
> l'app per la logica restituisce immediatamente uno `202 ACCEPTED` stato al chiamante.

1. Accedere al [portale di Azure](https://portal.azure.com). Creare un'app per la logica vuota.

1. Nella casella di ricerca della finestra Progettazione app per la logica, immettere `http request` come filtro. Dall'elenco trigger selezionare il trigger **quando viene ricevuta una richiesta http** .

   ![Selezionare Trigger di richiesta](./media/connectors-native-reqres/select-request-trigger.png)

   Il trigger di richiesta mostra le proprietà seguenti:

   ![Trigger di richiesta](./media/connectors-native-reqres/request-trigger.png)

   | Nome proprietà | Nome proprietà JSON | Obbligatoria | Descrizione |
   |---------------|--------------------|----------|-------------|
   | **URL POST HTTP** | {none} | Sì | URL dell'endpoint che viene generato dopo il salvataggio dell'app per la logica e viene usato per chiamare l'app per la logica |
   | **Corpo della richiesta: Schema JSON** | `schema` | No | Schema JSON che descrive le proprietà e i valori nel corpo della richiesta in ingresso |
   |||||

1. Nella casella **Corpo della richiesta: schema JSON**, immettere facoltativamente uno schema JSON che descrive il corpo della richiesta in ingresso, ad esempio:

   ![Schema JSON di esempio](./media/connectors-native-reqres/provide-json-schema.png)

   La finestra di progettazione usa questo schema per generare token per le proprietà nella richiesta. In questo modo, l'app per la logica può analizzare, usare e passare i dati dalla richiesta tramite il trigger nel flusso di lavoro.

   Di seguito viene riportato lo schema di esempio:

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

   Quando si immette uno schema JSON, la finestra di progettazione mostra un promemoria per includere l'intestazione `Content-Type` nella richiesta e impostare tale valore di intestazione su `application/json`. Per altre informazioni, vedere [Gestire i tipi di contenuti](../logic-apps/logic-apps-content-type.md).

   ![Promemoria per includere l'intestazione "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Ecco come appare questa intestazione in formato JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Per generare uno schema JSON basato sul payload previsto (dati), è possibile usare uno strumento come [JSONSchema.net](https://jsonschema.net)oppure seguire questa procedura:

   1. Nel trigger di richiesta selezionare **Usare il payload di esempio per generare lo schema**.

      ![Schermata con "USA payload di esempio per generare lo schema" selezionato](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Immettere il payload di esempio e selezionare **Fine**.

      ![Immettere il payload di esempio per generare lo schema](./media/connectors-native-reqres/enter-payload.png)

      Di seguito viene riportato il payload di esempio:

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

1. Per verificare che la chiamata in ingresso disponga di un corpo della richiesta corrispondente allo schema specificato, attenersi alla seguente procedura:

   1. Nella barra del titolo del trigger di richiesta selezionare il pulsante con i puntini di sospensione (**...**).

   1. Nelle impostazioni del trigger attivare la **convalida dello schema**e selezionare **fine**.

      Se il corpo della richiesta della chiamata in ingresso non corrisponde allo schema, il trigger restituisce un `HTTP 400 Bad Request` errore.

1. Per specificare proprietà aggiuntive, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri che si desidera aggiungere.

   | Nome proprietà | Nome proprietà JSON | Obbligatoria | Descrizione |
   |---------------|--------------------|----------|-------------|
   | **Metodo** | `method` | No | Metodo che le richieste in ingresso devono usare per chiamare l'app per la logica |
   | **Percorso relativo** | `relativePath` | No | Percorso relativo per il parametro che l'URL dell'endpoint dell'app per la logica può accettare |
   |||||

   In questo esempio viene aggiunta la proprietà **Metodo**:

   ![Aggiungere il parametro Metodo](./media/connectors-native-reqres/add-parameters.png)

   La proprietà **Metodo** viene visualizzata nel trigger, in modo che sia possibile selezionare un metodo nell'elenco.

   ![Selezionare il metodo](./media/connectors-native-reqres/select-method.png)

1. Aggiungere ora un'altra azione come passaggio successivo nel flusso di lavoro. Nel trigger, selezionare **Passaggio successivo** in modo che sia possibile trovare l'azione che si desidera aggiungere.

   È ad esempio possibile rispondere alla richiesta [aggiungendo un'azione di risposta](#add-response), che è possibile usare per restituire una risposta personalizzata ed è descritta più avanti in questo argomento.

   L'app per la logica mantiene aperta la richiesta in ingresso solo per un [periodo di tempo limitato](../logic-apps/logic-apps-limits-and-config.md#request-limits). Supponendo che il flusso di lavoro dell'app per la logica includa un'azione di risposta, se l'app per la logica non restituisce una risposta una volta trascorso questo intervallo di tempo, restituisce un `504 GATEWAY TIMEOUT` al chiamante. In caso contrario, se l'app per la logica non include un'azione di risposta, viene restituita immediatamente una risposta `202 ACCEPTED` al chiamante.

1. Al termine, salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Questo passaggio genera l'URL da usare per l'invio della richiesta che attiva l'app per la logica. Per copiare questo URL, selezionare l'icona di copia accanto all'URL.

   ![URL da usare per l'attivazione dell'app per la logica](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > Se si desidera includere il simbolo hash o Pound ( **#** ) nell'URI quando si effettua una chiamata al trigger request, utilizzare invece questa versione codificata: `%25%23`

1. Per attivare l'app per la logica, inviare un POST HTTP all'URL generato.

   Ad esempio, per inviare il POST HTTP è possibile usare uno strumento come [Postman](https://www.getpostman.com/). Per altre informazioni sulla definizione JSON sottostante al trigger e su come chiamare questo trigger, vedere gli argomenti [Tipo di trigger di richiesta](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [Chiamare, attivare o annidare flussi di lavoro con endpoint HTTP in App per la logica di Azure](../logic-apps/logic-apps-http-endpoint.md).

## <a name="trigger-outputs"></a>Output dei trigger

Di seguito sono riportate altre informazioni sugli output del trigger di richiesta:

| Nome proprietà JSON | Tipo di dati | Descrizione |
|--------------------|-----------|-------------|
| `headers` | Oggetto | Oggetto JSON che descrive le intestazioni della richiesta |
| `body` | Oggetto | Oggetto JSON che descrive il contenuto del corpo della richiesta |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Aggiungere un'azione di risposta

Quando si utilizza il trigger Request per gestire le richieste in ingresso, è possibile modellare la risposta e inviare i risultati del payload al chiamante utilizzando l' [azione di risposta](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)predefinita. È possibile usare l'azione di risposta *solo* con il trigger di richiesta. Questa combinazione con il trigger di richiesta e l'azione di risposta crea il [modello di richiesta-risposta](https://en.wikipedia.org/wiki/Request%E2%80%93response). Ad eccezione dei cicli all'interno di cicli foreach e until e dei rami paralleli, è possibile aggiungere l'azione di risposta in qualsiasi punto del flusso di lavoro.

> [!IMPORTANT]
> Se un'azione di risposta include queste intestazioni, App per la logica rimuove le intestazioni dal messaggio di risposta generato senza visualizzare alcun avviso o errore:
>
> * `Allow`
> * `Content-*` con le eccezioni seguenti: `Content-Disposition`, `Content-Encoding` e `Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Sebbene App per la logica non interrompano il salvataggio di app per la logica con un'azione di risposta con queste intestazioni, le app per la logica ignorano queste intestazioni.

1. Nella finestra di progettazione di App per la logica, sotto il passaggio in cui si vuole aggiungere un'azione di risposta, selezionare **Nuovo passaggio**.

   Ad esempio, usando il trigger di richiesta precedente:

   ![Aggiungere un nuovo passaggio](./media/connectors-native-reqres/add-response.png)

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) visualizzato e quindi **Aggiungi un'azione**.

1. In **scegliere un'azione**, nella casella di ricerca immettere `response` come filtro, quindi selezionare l'azione **risposta** .

   ![Selezionare l'azione di risposta](./media/connectors-native-reqres/select-response-action.png)

   In questo esempio, il trigger di richiesta è compresso per semplicità.

1. Aggiungere i valori necessari per il messaggio di risposta.

   In alcuni campi, facendo clic sulle relative caselle si apre un elenco di contenuti dinamico. È quindi possibile selezionare i token che rappresentano gli output disponibili dei passaggi precedenti del flusso di lavoro. Le proprietà dello schema specificato nell'esempio precedente appaiono ora nell'elenco dei contenuti dinamici.

   Per esempio, per la casella **Intestazioni**, includere `Content-Type` come nome chiave e impostare il valore chiave su `application/json`, come menzionato in precedenza in questo argomento. Per la casella **Corpo**, è possibile selezionare l'output del corpo del trigger dall'elenco dei contenuti dinamici.

   ![Dettagli dell'azione di risposta](./media/connectors-native-reqres/response-details.png)

   Per visualizzare le intestazioni in formato JSON, selezionare **Passa alla visualizzazione Testo**.

   ![Intestazioni - Passa alla visualizzazione Testo](./media/connectors-native-reqres/switch-to-text-view.png)

   Di seguito sono riportate altre informazioni sulle proprietà che è possibile impostare nell'azione di risposta.

   | Nome proprietà | Nome proprietà JSON | Obbligatoria | Descrizione |
   |---------------|--------------------|----------|-------------|
   | **Codice di stato** | `statusCode` | Sì | Codice di stato da restituire nella risposta |
   | **Intestazioni** | `headers` | No | Oggetto JSON che descrive una o più intestazioni da includere nella risposta |
   | **Corpo** | `body` | No | Il corpo della risposta |
   |||||

1. Per specificare proprietà aggiuntive, come ad esempio uno schema JSON per il corpo della risposta, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri da aggiungere.

1. Al termine, salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

* [Accesso protetto e accesso ai dati per le chiamate in ingresso a trigger basati su richiesta](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Connettori per App per la logica](../connectors/apis-list.md)