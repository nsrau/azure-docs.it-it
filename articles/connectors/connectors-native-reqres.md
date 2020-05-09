---
title: Ricevere e rispondere alle chiamate tramite HTTPS
description: Gestire le richieste HTTPS in ingresso da servizi esterni usando app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
tags: connectors
ms.openlocfilehash: c6d8dc087e6306173fc4d55368cd3c4c624d5302
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978570"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Ricevere e rispondere alle richieste HTTPS in ingresso in app per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e l'azione di risposta e trigger di richiesta incorporata, è possibile creare attività e flussi di lavoro automatizzati che ricevono e rispondono alle richieste HTTPS in ingresso. Ad esempio, è possibile avere l'app per la logica:

* Ricevere e rispondere a una richiesta HTTPS per i dati in un database locale.
* Attiva un flusso di lavoro quando si verifica un evento del webhook esterno.
* Ricevere e rispondere a una chiamata HTTPS da un'altra app per la logica.

Il trigger request supporta [Azure Active Directory Open Authentication](../active-directory/develop/about-microsoft-identity-platform.md) (Azure ad OAuth) per autorizzare le chiamate in ingresso all'app per la logica. Per altre informazioni sull'abilitazione di questa autenticazione, vedere [proteggere l'accesso e i dati in app per la logica di Azure-abilitare Azure ad autenticazione OAuth](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth).

> [!NOTE]
> Il trigger request supporta *solo* Transport Layer Security (TLS) 1,2 per le chiamate in ingresso. Le chiamate in uscita supportano TLS 1,0, 1,1 e 1,2. Per ulteriori informazioni, vedere [la pagina relativa alla risoluzione del problema TLS 1,0](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Se si verificano errori di handshake TLS, assicurarsi di usare TLS 1,2. 
> Per le chiamate in ingresso, di seguito sono riportati i pacchetti di crittografia supportati:
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

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per ottenere un account Azure gratuito](https://azure.microsoft.com/free/).

* Informazioni di base sulle app per la [logica](../logic-apps/logic-apps-overview.md). Se non si ha familiarità con le app per la logica, scoprire [come creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Aggiungi trigger di richiesta

Questo trigger predefinito crea un endpoint HTTPS richiamabile manualmente che può ricevere *solo* le richieste HTTPS in ingresso. Quando si verifica questo evento, il trigger viene attivato ed esegue l'app per la logica. Per altre informazioni sulla definizione JSON sottostante del trigger e su come chiamare questo trigger, vedere il [tipo di trigger di richiesta](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [chiamare, attivare o annidare i flussi di lavoro con endpoint HTTPS in app per la logica di Azure](../logic-apps/logic-apps-http-endpoint.md).

1. Accedere al [portale di Azure](https://portal.azure.com). Creare un'app per la logica vuota.

1. Dopo l'apertura di progettazione app per la logica, nella casella `http request` di ricerca immettere come filtro. Dall'elenco trigger selezionare il trigger **quando viene ricevuta una richiesta http** , che è il primo passaggio del flusso di lavoro dell'app per la logica.

   ![Seleziona trigger di richiesta](./media/connectors-native-reqres/select-request-trigger.png)

   Il trigger di richiesta Mostra queste proprietà:

   ![Trigger di richiesta](./media/connectors-native-reqres/request-trigger.png)

   | Nome proprietà | Nome proprietà JSON | Obbligatoria | Descrizione |
   |---------------|--------------------|----------|-------------|
   | **URL POST HTTP** | {none} | Sì | L'URL dell'endpoint che viene generato dopo il salvataggio dell'app per la logica e viene usato per chiamare l'app per la logica |
   | **Schema JSON del corpo della richiesta** | `schema` | No | Schema JSON che descrive le proprietà e i valori nel corpo della richiesta in ingresso |
   |||||

1. Nella casella **dello schema JSON del corpo della richiesta** , immettere facoltativamente uno schema JSON che descrive il corpo della richiesta in ingresso, ad esempio:

   ![Schema JSON di esempio](./media/connectors-native-reqres/provide-json-schema.png)

   La finestra di progettazione utilizza questo schema per generare token per le proprietà nella richiesta. In questo modo, l'app per la logica può analizzare, utilizzare e passare i dati dalla richiesta tramite il trigger nel flusso di lavoro.

   Di seguito è riportato lo schema di esempio:

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

   Quando si immette uno schema JSON, la finestra di progettazione Mostra un promemoria `Content-Type` per includere l'intestazione nella richiesta e impostare tale valore `application/json`di intestazione su. Per altre informazioni, vedere [gestire i tipi di contenuto](../logic-apps/logic-apps-content-type.md).

   ![Promemoria per includere l'intestazione "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Ecco come appare questa intestazione in formato JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Per generare uno schema JSON basato sul payload previsto (dati), è possibile usare uno strumento come [JSONSchema.NET](https://jsonschema.net)oppure è possibile seguire questa procedura:

   1. Nel trigger di richiesta selezionare **Usare il payload di esempio per generare lo schema**.

      ![Genera schema dal payload](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Immettere il payload di esempio e selezionare **fine**.

      ![Genera schema dal payload](./media/connectors-native-reqres/enter-payload.png)

      Ecco il payload di esempio:

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

1. Per specificare altre proprietà, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri che si desidera aggiungere.

   | Nome proprietà | Nome proprietà JSON | Obbligatoria | Descrizione |
   |---------------|--------------------|----------|-------------|
   | **Metodo** | `method` | No | Metodo che la richiesta in ingresso deve usare per chiamare l'app per la logica |
   | **Percorso relativo** | `relativePath` | No | Percorso relativo del parametro che l'URL dell'endpoint dell'app per la logica può accettare |
   |||||

   In questo esempio viene aggiunta la proprietà **Method** :

   ![Aggiungi parametro del metodo](./media/connectors-native-reqres/add-parameters.png)

   La proprietà **Method** viene visualizzata nel trigger, in modo che sia possibile selezionare un metodo nell'elenco.

   ![Select (metodo)](./media/connectors-native-reqres/select-method.png)

1. Aggiungere ora un'altra azione come passaggio successivo nel flusso di lavoro. Sotto il trigger selezionare **passaggio successivo** in modo che sia possibile trovare l'azione che si desidera aggiungere.

   Ad esempio, è possibile rispondere alla richiesta [aggiungendo un'azione di risposta](#add-response), che è possibile usare per restituire una risposta personalizzata ed è descritta più avanti in questo argomento.

   L'app per la logica mantiene aperta la richiesta in ingresso solo per un minuto. Supponendo che il flusso `504 GATEWAY TIMEOUT` di lavoro dell'app per la logica includa un'azione di risposta, se l'app per la logica non restituisce una risposta dopo che questo tempo viene superato, l'app per la logica restituisce al chiamante. In caso contrario, se l'app per la logica non include un'azione di risposta, l' `202 ACCEPTED` app per la logica restituisce immediatamente una risposta al chiamante.

1. Al termine, salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Questo passaggio genera l'URL da usare per l'invio della richiesta che attiva l'app per la logica. Per copiare questo URL, selezionare l'icona di copia accanto all'URL.

   ![URL da usare per l'attivazione dell'app per la logica](./media/connectors-native-reqres/generated-url.png)

1. Per attivare l'app per la logica, inviare un POST HTTP all'URL generato.

   Ad esempio, è possibile usare uno strumento come [subpost](https://www.getpostman.com/) per inviare il post http. Se è stata [abilitata Azure Active Directory Open Authentication](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) (Azure ad OAuth) per autorizzare le chiamate in ingresso al trigger di richiesta, chiamare il trigger usando un [URL di firma di accesso condiviso (SAS)](../logic-apps/logic-apps-securing-a-logic-app.md#sas) o usando un token di autenticazione, ma non è possibile usare entrambi. Il token di autenticazione deve specificare `Bearer` il tipo nell'intestazione dell'autorizzazione. Per altre informazioni, vedere [proteggere l'accesso e i dati in app per la logica di Azure-accesso ai trigger basati su richiesta](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers).

Per altre informazioni sulla definizione JSON sottostante del trigger e su come chiamare questo trigger, vedere questi argomenti, [richiedere il tipo di trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [chiamare, attivare o annidare flussi di lavoro con endpoint HTTP in app per la logica di Azure](../logic-apps/logic-apps-http-endpoint.md).

### <a name="trigger-outputs"></a>Output dei trigger

Di seguito sono riportate altre informazioni sugli output del trigger di richiesta:

| Nome proprietà JSON | Tipo di dati | Descrizione |
|--------------------|-----------|-------------|
| `headers` | Oggetto | Oggetto JSON che descrive le intestazioni della richiesta |
| `body` | Oggetto | Oggetto JSON che descrive il contenuto del corpo dalla richiesta |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Aggiungere un'azione di risposta

È possibile usare l'azione di risposta per rispondere con un payload (dati) a una richiesta HTTPS in ingresso, ma solo in un'app per la logica attivata da una richiesta HTTPS. È possibile aggiungere l'azione di risposta in qualsiasi punto del flusso di lavoro. Per ulteriori informazioni sulla definizione JSON sottostante per questo trigger, vedere il [tipo di azione risposta](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

L'app per la logica mantiene aperta la richiesta in ingresso solo per un minuto. Supponendo che il flusso `504 GATEWAY TIMEOUT` di lavoro dell'app per la logica includa un'azione di risposta, se l'app per la logica non restituisce una risposta dopo che questo tempo viene superato, l'app per la logica restituisce al chiamante. In caso contrario, se l'app per la logica non include un'azione di risposta, l' `202 ACCEPTED` app per la logica restituisce immediatamente una risposta al chiamante.

> [!IMPORTANT]
> Se un'azione di risposta include queste intestazioni, app per la logica rimuove le intestazioni dal messaggio di risposta generato senza visualizzare alcun avviso o errore:
>
> * `Allow`
> * `Content-*`con le eccezioni seguenti `Content-Disposition`: `Content-Encoding`, e`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Sebbene le app per la logica non interrompano il salvataggio di app per la logica con un'azione di risposta con queste intestazioni, le app per la logica ignorano queste intestazioni.

1. Nella finestra di progettazione dell'app per la logica, sotto il passaggio in cui si vuole aggiungere un'azione di risposta, selezionare **nuovo passaggio**.

   Ad esempio, usando il trigger di richiesta precedente:

   ![Aggiungi nuovo passaggio](./media/connectors-native-reqres/add-response.png)

   Per aggiungere un'azione tra i passaggi, spostare il puntatore sulla freccia tra questi passaggi. Selezionare il segno più (**+**) visualizzato, quindi selezionare **Aggiungi un'azione**.

1. In **scegliere un'azione**, nella casella di ricerca, immettere "risposta" come filtro e selezionare l'azione **risposta** .

   ![Selezionare l'azione di risposta](./media/connectors-native-reqres/select-response-action.png)

   Il trigger di richiesta è compresso in questo esempio per semplicità.

1. Aggiungere i valori necessari per il messaggio di risposta. 

   In alcuni campi, quando si fa clic all'interno delle caselle viene aperto l'elenco di contenuto dinamico. È quindi possibile selezionare i token che rappresentano gli output disponibili dei passaggi precedenti del flusso di lavoro. Le proprietà dello schema specificato nell'esempio precedente vengono ora visualizzate nell'elenco di contenuto dinamico.

   Ad esempio, per la casella **intestazioni** , includere `Content-Type` come nome chiave e impostare il valore della chiave su `application/json` come indicato in precedenza in questo argomento. Per la casella **corpo** è possibile selezionare l'output del corpo del trigger dall'elenco di contenuto dinamico.

   ![Dettagli azione risposta](./media/connectors-native-reqres/response-details.png)

   Per visualizzare le intestazioni in formato JSON, selezionare **passa a visualizzazione testo**.

   ![Intestazioni-passa alla visualizzazione testo](./media/connectors-native-reqres/switch-to-text-view.png)

   Di seguito sono riportate altre informazioni sulle proprietà che è possibile impostare nell'azione di risposta. 

   | Nome proprietà | Nome proprietà JSON | Obbligatoria | Descrizione |
   |---------------|--------------------|----------|-------------|
   | **Codice di stato** | `statusCode` | Sì | Codice di stato da restituire nella risposta |
   | **Intestazioni** | `headers` | No | Oggetto JSON che descrive una o più intestazioni da includere nella risposta |
   | **Corpo** | `body` | No | Il corpo della risposta |
   |||||

1. Per specificare proprietà aggiuntive, ad esempio uno schema JSON per il corpo della risposta, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri che si desidera aggiungere.

1. Al termine, salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**. 

## <a name="next-steps"></a>Passaggi successivi

* [Connettori per App per la logica](../connectors/apis-list.md)