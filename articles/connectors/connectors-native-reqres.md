---
title: Ricevere e rispondere alle chiamate tramite HTTPS
description: Gestire le richieste HTTPS in ingresso da servizi esterni tramite App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
tags: connectors
ms.openlocfilehash: ae34840c04c3a1d2fb3646046792c97ed6f521a0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289442"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Ricevere e rispondere alle richieste HTTPS in ingresso in App per la logica di Azure

Con [App per la logica di Azure](../logic-apps/logic-apps-overview.md), assieme al trigger di richiesta e all'azione di risposta predefiniti, è possibile creare attività e flussi di lavoro automatizzati che ricevono le richieste HTTPS in ingresso e rispondono. Ad esempio, è possibile far sì che l'app per la logica:

* Riceva e risponda a una richiesta HTTPS di dati in un database locale.

* Attivi un flusso di lavoro quando si verifica un evento del webhook esterno.

* Riceva e risponda a una chiamata HTTPS da un'altra app per la logica.

Il trigger di richiesta supporta [Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) per autorizzare le chiamate in ingresso all'app per la logica. Per altre informazioni sull'abilitazione di questa autenticazione, vedere [Proteggere l'accesso e i dati in App per la logica di Azure: abilitare l'autenticazione OAuth di Azure AD](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Conoscenze di base di [app per la logica](../logic-apps/logic-apps-overview.md). Se non si ha familiarità con le app per la logica, vedere [come creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

* Le chiamate in ingresso supportano *solo* Transport Layer Security (TLS) 1,2. Se si verificano errori di handshake TLS, assicurarsi di usare TLS 1.2. Per altre informazioni, vedere [Risoluzione del problema relativo a TLS 1.0](/security/solving-tls1-problem). Le chiamate in uscita supportano TLS 1,0, 1,1 e 1,2, in base alla funzionalità dell'endpoint di destinazione.

* Le chiamate in ingresso supportano questi pacchetti di crittografia:

  * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384

  * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

  * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384

  * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256

  * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384

  * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256

  * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

  * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Aggiungere un trigger di richiesta

Questo trigger predefinito crea un endpoint HTTPS richiamabile manualmente che può ricevere *solo* richieste HTTPS in ingresso. Quando si verifica questo evento, il trigger viene attivato ed esegue l'app per la logica. Per altre informazioni sulla definizione JSON sottostante al trigger e su come chiamare questo trigger, vedere [Tipo di trigger di richiesta](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [Chiamare, attivare o annidare flussi di lavoro con endpoint HTTPS in App per la logica di Azure](../logic-apps/logic-apps-http-endpoint.md).

1. Accedere al [portale di Azure](https://portal.azure.com). Creare un'app per la logica vuota.

1. Nella casella di ricerca della finestra Progettazione app per la logica, immettere `http request` come filtro. Dall'elenco di trigger, selezionare il trigger **Alla ricezione di una richiesta HTTP**, che rappresenta il primo passaggio del flusso di lavoro dell'app per la logica.

   ![Selezionare Trigger di richiesta](./media/connectors-native-reqres/select-request-trigger.png)

   Il trigger di richiesta mostra le proprietà seguenti:

   ![Trigger di richiesta](./media/connectors-native-reqres/request-trigger.png)

   | Nome proprietà | Nome proprietà JSON | Obbligatoria | Description |
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

      ![Generare uno schema dal payload](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Immettere il payload di esempio e selezionare **Fine**.

      ![Generare uno schema dal payload](./media/connectors-native-reqres/enter-payload.png)

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

   | Nome proprietà | Nome proprietà JSON | Obbligatoria | Description |
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
   > Se si desidera includere il simbolo hash o Pound ( **#** ) nell'URI quando si effettua una chiamata al trigger request, utilizzare invece questa versione codificata:`%25%23`

1. Per attivare l'app per la logica, inviare un POST HTTP all'URL generato.

   Ad esempio, per inviare il POST HTTP è possibile usare uno strumento come [Postman](https://www.getpostman.com/). Se [Azure Active Directory Open Authentication](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) (Azure AD OAuth) è abilitato per autorizzare le chiamate in ingresso al trigger di richiesta, chiamare il trigger usando un [URL di firma di accesso condiviso](../logic-apps/logic-apps-securing-a-logic-app.md#sas) o usando un token di autenticazione. Non è tuttavia possibile usare entrambi. Il token di autenticazione deve specificare il tipo `Bearer` nell'intestazione dell'autorizzazione. Per altre informazioni, vedere [Proteggere l'accesso e i dati in App per la logica di Azure: accesso ai trigger basati su richiesta](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers).

Per altre informazioni sulla definizione JSON sottostante al trigger e su come chiamare questo trigger, vedere gli argomenti [Tipo di trigger di richiesta](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [Chiamare, attivare o annidare flussi di lavoro con endpoint HTTP in App per la logica di Azure](../logic-apps/logic-apps-http-endpoint.md).

### <a name="trigger-outputs"></a>Output dei trigger

Di seguito sono riportate altre informazioni sugli output del trigger di richiesta:

| Nome proprietà JSON | Tipo di dati | Descrizione |
|--------------------|-----------|-------------|
| `headers` | Oggetto | Oggetto JSON che descrive le intestazioni della richiesta |
| `body` | Oggetto | Oggetto JSON che descrive il contenuto del corpo della richiesta |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Aggiungere un'azione di risposta

È possibile usare l'azione di risposta per rispondere con un payload (dati) a una richiesta HTTPS in ingresso, ma solo in un'app per la logica attivata da una richiesta HTTPS. È possibile aggiungere l'azione di risposta in qualsiasi punto del flusso di lavoro. Per altre informazioni sulla definizione JSON sottostante per questo trigger, vedere il [Tipo di azione di risposta](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

L'app per la logica mantiene aperta la richiesta in ingresso solo per un [periodo di tempo limitato](../logic-apps/logic-apps-limits-and-config.md#request-limits). Supponendo che il flusso di lavoro dell'app per la logica includa un'azione di risposta, se l'app per la logica non restituisce una risposta una volta trascorso questo intervallo di tempo, restituisce un `504 GATEWAY TIMEOUT` al chiamante. In caso contrario, se l'app per la logica non include un'azione di risposta, viene restituita immediatamente una risposta `202 ACCEPTED` al chiamante.

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

1. Nella casella di ricerca **Scegliere un'azione**, immettere "risposta" come filtro e selezionare l'azione **Risposta**.

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

* [Connettori per App per la logica](../connectors/apis-list.md)

