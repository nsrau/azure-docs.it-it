---
title: Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP - App per la logica di Azure | Microsoft Docs
description: Configurare endpoint HTTP per chiamare, attivare o annidare flussi di lavoro di app per la logica di Azure
services: logic-apps
keywords: flussi di lavoro, endpoint HTTP
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: de4f4ee086fbf3799fcac1f1b008d9237b5e7a09
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP in app per la logica

È possibile esporre a livello nativo gli endpoint HTTP sincroni come trigger sulle app per la logica, permettendo in tal modo di attivare o di chiamare le app per la logica tramite un URL. È inoltre possibile annidare i flussi di lavoro nell'app per la logica usando un modello di endpoint richiamabile.

Per creare gli endpoint HTTP, è possibile aggiungere questi trigger in modo che le app per la logica possano ricevere le richieste in ingresso:

* [Richiesta](../connectors/connectors-native-reqres.md)

* [Webhook di connessione API](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [Webhook HTTP](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Nonostante questi esempi usino il trigger **Richiesta** è possibile usare uno dei trigger HTTP elencati e tutti i principi si applicano in modo identico agli altri tipi di trigger.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Configurare un endpoint HTTP per un'app per la logica

Per creare un endpoint HTTP, aggiungere un trigger in grado di ricevere le richieste in ingresso.

1. Accedere al [Portale di Azure](https://portal.azure.com "Portale di Azure"). Andare in app per la logica e aprire Progettazione app per la logica.

2. Aggiungere un trigger che consente all'app per la logica di ricevere richieste in ingresso. Ad esempio aggiungere il trigger **Request** all'app per la logica.

3.  In **Schema JSON del corpo della richiesta** è possibile immettere uno schema JSON per il payload (dati) che si prevede il trigger possa ricevere.

    La finestra di progettazione usa questo schema per generare token di cui l'app per la logica si può servire per usare, analizzare e passare dati dal trigger attraverso il flusso di lavoro. 
    Altre informazioni sui [token generati da schemi JSON](#generated-tokens).

    In questo esempio, immettere lo schema indicato nella finestra di progettazione:

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![Aggiungere l'azione Request][1]

    > [!TIP]
    > 
    > È possibile generare uno schema per un payload JSON di esempio da uno strumento come [jsonschema.net](http://jsonschema.net/), o nel trigger **Richiesta** scegliendo **Usare il payload di esempio per generare lo schema**. 
    > Immettere il payload di esempio e scegliere **Fine**.

    Ad esempio, questo payload di esempio:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    genera questo schema:

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Salvare l'app per la logica. In **HTTP POST in questo URL**, è necessario individuare un URL di callback generato, come in questo esempio:

    ![URL di callback generato per endpoint](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    L'URL contiene una chiave di firma di accesso condiviso (SAS) nei parametri di query usati per l'autenticazione. 
    È inoltre possibile ottenere l'URL dell'endpoint HTTP dalla panoramica dell'app per la logica nel portale di Azure. In **Cronologia trigger** selezionare il trigger:

    ![Ottenere l'URL dell'endpoint HTTP GET dal portale di Azure][2]

    Oppure è possibile ottenere l'URL mediante questa chiamata:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Modificare il metodo HTTP per il trigger

Per impostazione predefinita, il trigger **Richiesta** prevede una richiesta HTTP POST, ma è possibile usare un metodo HTTP diverso. 

> [!NOTE]
> È possibile specificare solo un tipo di metodo.

1. Nel trigger **Richiesta** scegliere **Mostra opzioni avanzate**.

2. Aprire l'elenco **Metodo**. Per questo esempio, selezionare **GET** (OTTIENI) in modo che sia possibile testare l'URL dell'endpoint HTTP in un secondo momento.

    > [!NOTE]
    > È possibile selezionare qualsiasi altro metodo HTTP o specificare un metodo personalizzato per la propria app per la logica.

    ![Modificare il metodo HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Accettare i parametri tramite l'URL dell'endpoint HTTP

Quando si desidera che l'URL dell'endpoint HTTP accetti i parametri, personalizzare il percorso relativo al trigger.

1. Nel trigger **Richiesta** scegliere **Mostra opzioni avanzate**. 

2. In **Metodo** specificare il metodo HTTP che la richiesta deve usare. Per questo esempio, selezionare il metodo **GET** (OTTIENI), se non lo si è già fatto, in modo che sia possibile testare l'URL dell'endpoint HTTP.

      > [!NOTE]
      > Quando si specifica un percorso relativo per il trigger, è necessario specificare anche in modo esplicito un metodo HTTP per il trigger.

3. In **Percorso relativo** specificare il percorso relativo per il parametro che l'URL deve accettare, ad esempio, `customers/{customerID}`.

    ![Specificare il percorso relativo e il metodo HTTP per il parametro](./media/logic-apps-http-endpoint/relativeurl.png)

4. Per usare il parametro, aggiungere un'azione **Risposta** all'app per la logica. (Nel trigger scegliere **Nuovo passaggio** > **Aggiungi un'azione** > **Risposta**) 

5. Nel **corpo** della risposta includere il token per il parametro specificato nel percorso relativo del trigger.

    Ad esempio, per restituire `Hello {customerID}`, aggiornare il **corpo** della risposta con `Hello {customerID token}`. 
    Viene visualizzato l'elenco del contenuto dinamico che mostra il token `customerID` da selezionare.

    ![Aggiungere un parametro al corpo della risposta](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    Il **corpo** dovrebbe essere simile al seguente:

    ![Corpo della risposta con parametri](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Salvare l'app per la logica. 

    L'URL dell'endpoint HTTP include ora il percorso relativo, ad esempio: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Per testare l'endpoint HTTP, copiare e incollare l'URL aggiornato in un'altra finestra del browser, ma sostituire `{customerID}` con `123456` e premere Invio.

    Il browser deve mostrare questo testo: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Token generati da schemi JSON per l'app per la logica

Quando si specifica uno schema JSON nel trigger **Richiesta**, la finestra di progettazione app per la logica genera i token per le proprietà nello schema. È quindi possibile usare tali token per passare dati tramite il flusso di lavoro di app per la logica.

Per questo esempio, se si aggiungono le proprietà `title` e `name` allo schema JSON, i token sono ora disponibili per essere usati nei passaggi successivi del flusso di lavoro. 

Di seguito è riportato lo schema JSON completo:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Creare flussi di lavoro annidati per le app per la logica

È possibile nidificare i flussi di lavoro nell'app per la logica aggiungendo altre app per la logica che possono ricevere richieste. Per includere queste app per la logica, aggiungere l'azione **Azure Logic Apps - Choose a Logic Apps workflow** (App per la logica di Azure - Scegliere un flusso di lavoro delle app per la logica) al trigger. È quindi possibile selezionare dalle app per la logica idonee.

![Aggiungere un'altra app per la logica](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Chiamare o avviare le app per la logica tramite endpoint HTTP

Dopo aver creato l'endpoint HTTP, è possibile attivare l'app per la logica tramite un metodo `POST` per l'URL completo. Le app per la logica dispongono di supporto incorporato per gli endpoint di accesso diretto.

> [!NOTE] 
> Per eseguire manualmente un'app per la logica in qualsiasi momento, sulla barra degli strumenti Progettazione app per la logica o Visualizzazione codice app per la logica scegliere **Esegui**.

## <a name="reference-content-from-an-incoming-request"></a>Riferimento al contenuto dalla richiesta in ingresso

Se il tipo di contenuto è `application/json`, è possibile fare riferimento a proprietà dalla richiesta in ingresso. In caso contrario, il contenuto viene considerato come una singola unità binaria che è possibile passare ad altre API. Per fare riferimento a questo contenuto all'interno del flusso di lavoro è necessario convertire il contenuto. Se ad esempio si passa il contenuto `application/xml`, è possibile usare `@xpath()` per eseguire un'estrazione XPath o `@json()` per la conversione da XML a JSON. Informazioni sull'[uso dei tipi di contenuto](../logic-apps/logic-apps-content-type.md).

Per ottenere l'output da una richiesta in ingresso, è possibile usare la funzione `@triggerOutputs()`. L'output potrebbe essere simile al seguente:

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

È possibile accedere in modo specifico alla proprietà `body` mediante il collegamento `@triggerBody()`. 

## <a name="respond-to-requests"></a>Rispondere alle richieste

Per alcune richieste che avviano un'app per la logica, può risultare utile rispondere restituendo contenuto al chiamante. Per creare il codice di stato, l'intestazione e il corpo della risposta, è possibile usare l'azione **Risposta**. Questa azione può trovarsi in qualsiasi punto nell'app per la logica, non solo alla fine del flusso di lavoro.

> [!NOTE] 
> Se l'app per la logica non include una **Risposta**, l'endpoint HTTP risponde *immediatamente* con uno stato **202 -Accettato**. Nell'app per la logica, per far sì che la richiesta originale ottenga la risposta, tutti i passaggi necessari devono essere completati entro i [limiti di tempo della richiesta](./logic-apps-limits-and-config.md), salvo se si chiama il flusso di lavoro come app per la logica annidata. Se non si ottiene alcuna azione di risposta entro questo limite, si verifica il timeout della richiesta in ingresso, che riceverà una risposta HTTP **408 Client timeout** (408 - Timeout client). Per le app per la logica annidate, l'app per la logica padre resta in attesa di una risposta fino al completamento, indipendentemente dal tempo necessario.

### <a name="construct-the-response"></a>Costruire la risposta

È possibile includere più di un'intestazione e qualsiasi tipo di contenuto nel corpo della risposta. Nell'esempio di risposta l'intestazione specifica che la risposta contiene il tipo di contenuto `application/json`, mentre il corpo contiene `title` e `name`, in base allo schema JSON aggiornato in precedenza per il trigger **Richiesta**.

![Azione di risposta HTTP][3]

Le risposte hanno le seguenti proprietà:

| Proprietà | DESCRIZIONE |
| --- | --- |
| statusCode |Specifica il codice di stato HTTP per la risposta alla richiesta in ingresso. Può essere qualsiasi codice di stato valido che inizia con 2xx, 4xx o 5xx. I codici di stato 3xx non sono consentiti. |
| headers |Definisce un numero illimitato di intestazioni da includere nella risposta. |
| Corpo |Specifica un oggetto body che può essere una stringa, un oggetto JSON o anche contenuto binario a cui si fa riferimento da un passaggio precedente. |

Di seguito viene riportato l'aspetto che lo schema JSON dovrebbe avere ora per l'azione **Risposta**:

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Per visualizzare la definizione JSON completa per l'app per la logica, nella finestra di progettazione dell'app per la logica, scegliere **Visualizzazione codice**.

## <a name="q--a"></a>Domande e risposte

#### <a name="q-what-about-url-security"></a>D: Come viene garantita la sicurezza degli URL?

R: Azure genera in modo sicuro gli URL di callback dell'app per la logica mediante una firma di accesso condiviso (SAS). La firma viene trasmessa come parametro di query e deve essere convalidata prima dell'attivazione dell'app per la logica. Azure genera la firma con una combinazione univoca che include la chiave privata per ogni app per la logica, il nome del trigger e l'operazione in esecuzione. Pertanto, a meno che un utente non ottenga l'accesso alla chiave privata dell'app per la logica, non potrà generare una firma valida.

   > [!IMPORTANT]
   > Per i sistemi di produzione e protezione, è consigliabile evitare la chiamata dell'app per la logica direttamente dal browser in quanto:
   > 
   > * La chiave di accesso condiviso viene visualizzata nell'URL.
   > * Non è possibile gestire i criteri di contenuto protetti a causa di domini condivisi tra i clienti di App per la logica.

#### <a name="q-can-i-configure-http-endpoints-further"></a>D: È possibile configurare ulteriormente gli endpoint HTTP?

R: Sì, gli endpoint HTTP supportano configurazioni più avanzate tramite la [**Gestione API**](../api-management/api-management-key-concepts.md). Questo servizio offre inoltre la possibilità di gestire tutte le API in modo coerente, incluse le app per la logica, di impostare i nomi di dominio personalizzato, usare più metodi di autenticazione e altro ancora, ad esempio:

* [Impostare il metodo della richiesta](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Modificare i segmenti dell'URL della richiesta](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Configurare i domini di Gestione API nel [portale di Azure](https://portal.azure.com/ "portale di Azure")
* Impostare la norma per verificare l'autenticazione di base

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>D: Che cosa è cambiato con la migrazione dello schema dall'anteprima del 1 dicembre 2014?

R: Di seguito è riportato un riepilogo di queste modifiche:

| Anteprima del 1 dicembre 2014 | 1 giugno 2016 |
| --- | --- |
| Fare clic sull'app per le API **Listener HTTP** |Fare clic su **Attivazione manuale**. Non è necessaria un'app per le API. |
| Impostazione di Listener HTTP "*Send response automatically*" (Invia risposta automaticamente) |Includere o meno un'azione **Risposta** nella definizione del flusso di lavoro |
| Configurare l'autenticazione di base o OAuth |tramite Gestione API |
| Configurare il metodo HTTP |In **Mostra opzioni avanzate** scegliere un metodo HTTP |
| Configurare il percorso relativo |In **Mostra opzioni avanzate** aggiungere un percorso relativo |
| Fare riferimento all'oggetto body in ingresso tramite `@triggerOutputs().body.Content` |Fare riferimento tramite `@triggerOutputs().body` |
| **Send HTTP response** (Invia risposta HTTP) nel Listener HTTP |Fare clic su **Respond to HTTP request** (Rispondi alla richiesta HTTP). Non è necessaria un'app per le API. |

## <a name="get-help"></a>Ottenere aiuto

Per porre domande, fornire risposte e ottenere informazioni sulle attività degli altri utenti di App per la logica di Azure, vedere il [forum su App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Per contribuire al miglioramento delle App per la logica di Azure e dei connettori, votare o inviare idee al [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Creare definizioni di app per la logica](./logic-apps-author-definitions.md)
* [Gestire errori ed eccezioni](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png