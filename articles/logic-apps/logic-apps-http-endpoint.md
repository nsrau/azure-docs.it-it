---
title: Chiamare, attivare o annidare app per la logica tramite endpoint HTTP - App per la logica di Azure | Microsoft Docs
description: Aggiungere e configurare endpoint HTTP per chiamare, attivare o annidare flussi di lavoro di app per la logica in Azure
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 8c4cb0aefcd7241b17c6afcf33496c1fc2e6f675
ms.lasthandoff: 03/03/2017

---

# <a name="add-http-endpoints-to-call-trigger-or-nest-logic-app-workflows"></a>Aggiungere endpoint HTTP per chiamare, attivare o annidare flussi di lavoro di app per la logica

Le app per la logica possono esporre a livello nativo gli endpoint HTTP sincroni come trigger, permettendo in tal modo la chiamata delle app stesse. È anche possibile usare una struttura di endpoint chiamabili per chiamare le app per la logica come flussi di lavoro annidati, aggiungendo l'azione **Scegliere un flusso di lavoro delle app per la logica** all'app per la logica.

È possibile usare questi trigger per la ricezione di richieste:

* Richiesta
* ApiConnectionWebhook
* HttpWebhook

Questo argomento usa il trigger **Request** come esempio, ma tutti i principi si applicano in modo analogo agli altri tipi di trigger.

## <a name="add-a-trigger-to-your-logic-app-definition"></a>Aggiungere un trigger alla definizione dell'app per la logica

1. In Progettazione app per la logica, aggiungere un trigger in grado di ricevere richieste in entrata per la definizione dell'app per la logica. Ad esempio aggiungere il trigger **Request** all'app per la logica.

2.    In **Schema JSON del corpo della richiesta** è possibile immettere uno schema JSON per il payload che si prevede di ricevere. Se non si dispone di uno schema pronto è possibile scegliere **Usare il payload di esempio per generare lo schema** per generare uno schema JSON da un payload di esempio.

    La finestra di progettazione usa questo schema per generare token che consentono di usare, analizzare e passare dati dal trigger manuale attraverso il flusso di lavoro.

    ![Aggiungere l'azione Request][2]

2.    Dopo aver salvato la definizione dell'app per la logica, in **HTTP POST in questo URL** si ottiene un URL di callback generato, come in questo esempio:

    ``` text
    https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
    ```

    L'URL contiene una chiave di firma di accesso condiviso (SAS) nei parametri di query usati per l'autenticazione. 
    È anche possibile ottenere questo endpoint nel portale di Azure:

    ![Endpoint dell'URL][1]

    Oppure chiamando:

    ``` text
    POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
    ```

### <a name="change-the-http-method-for-your-trigger"></a>Modificare il metodo HTTP per il trigger

Per impostazione predefinita il trigger Request prevede una richiesta HTTP POST. Per configurare un altro metodo HTTP scegliere **Mostra opzioni avanzate**.

> [!NOTE]
> È consentito un solo tipo di metodo.

### <a name="customize-the-relative-trigger-url"></a>Personalizzare l'URL trigger relativo

È anche possibile personalizzare il percorso relativo in modo che l'URL della richiesta accetti parametri.

1. Nel trigger **Request** scegliere **Mostra opzioni avanzate**. In **Percorso relativo**, immettere `customer/{customerId}`.

    ![Trigger URL relativo](./media/logic-apps-http-endpoint/relativeurl.png)

2.    Per usare il parametro, aggiornare l'azione **Response**.

    *    Nella selezione token viene visualizzato `customerId`.
    *    Per ottenere la restituzione di `Hello {customerId}` aggiornare il corpo dell'azione **Response**.

    ![Risposta URL relativo](./media/logic-apps-http-endpoint/relativeurlresponse.png)

3. Salvare l'app per la logica. L'URL della richiesta viene aggiornato con il percorso relativo.

4. Copiare il nuovo URL della richiesta e incollarlo in una nuova finestra del browser. Sostituire `{customerId}` con `123` e premere Invio.

    Viene restituito il seguente testo: `Your customer Id is 123`

### <a name="security-for-the-trigger-url"></a>Sicurezza per l'URL trigger

Gli URL di callback delle app per la logica vengono generati in modo sicuro mediante una firma di accesso condiviso (SAS, Shared Access Signature). La firma viene trasmessa come parametro di query e deve essere convalidata prima dell'attivazione dell'app per la logica. La firma viene generata con una combinazione univoca che include la chiave privata per ogni app per la logica, il nome del trigger e l'operazione in esecuzione. A meno che un utente non ottenga l'accesso alla chiave privata dell'app per la logica, non potrà generare una firma valida.

## <a name="call-your-logic-app-triggers-endpoint"></a>Chiamare l'endpoint del trigger dell'app per la logica

Dopo aver creato l'endpoint del trigger, è possibile attivare l'app per la logica tramite un `POST` per l'URL completo. È possibile includere nel corpo intestazioni aggiuntive e qualsiasi altro contenuto. Se il tipo del contenuto è `application/json` è possibile fare riferimento a proprietà dall'interno della richiesta. Negli altri casi il contenuto viene considerato come singola unità binaria che può essere passata ad altre API, ma alla quale è possibile fare riferimento nel flusso di lavoro solo se si converte il contenuto. Se ad esempio si passa il contenuto `application/xml` è possibile usare `@xpath()` per eseguire un'estrazione XPath o `@json()` per la conversione da XML a JSON. Informazioni sull'[uso dei tipi di contenuto](../logic-apps/logic-apps-content-type.md).

È anche possibile specificare uno schema JSON nella definizione. Lo schema consente alla finestra di progettazione di generare i token che sarà possibile passare alla procedura. Nell'esempio seguente i token `title` e `name` vengono resi disponibili nella finestra di progettazione:

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="reference-the-content-from-the-incoming-request"></a>Creare un riferimento al contenuto dalla richiesta in ingresso

La funzione `@triggerOutputs()` restituisce il contenuto della richiesta in ingresso. L'output è simile all'esempio seguente:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

È possibile accedere in modo specifico alla proprietà `body` mediante il collegamento `@triggerBody()`. 

## <a name="respond-to-the-request"></a>Rispondere alla richiesta

Per alcune richieste che avviano un'app per la logica, può risultare utile rispondere inviando contenuto al chiamante. Per costruire il codice di stato, il corpo e le intestazioni della risposta è possibile usare il tipo di azione **Response**. Se non è inclusa un'azione **Response**, l'endpoint dell'app per la logica risponde *immediatamente* con **202 - Accettato**.

![Azione di risposta HTTP][3]

``` json
"Response": {
        "conditions": [],
        "inputs": {
            "body": {
                "name": "@{triggerBody()['name']}", 
                    "title": "@{triggerBody()['title']}"
            },
            "headers": {
                "content-type": "application/json"
            },
            "statusCode": 200
        },
        "type": "Response"
}
```

Le risposte hanno le seguenti proprietà:

| Proprietà | Descrizione |
| --- | --- |
| statusCode |Codice di stato HTTP per la risposta alla richiesta in ingresso. Può essere qualsiasi codice di stato valido che inizia con 2xx, 4xx o 5xx. I codici di stato 3xx non sono consentiti. |
| body |Oggetto body che può essere una stringa, un oggetto JSON o anche contenuto binario a cui si fa riferimento da un passaggio precedente. |
| headers |È possibile definire un numero illimitato di intestazioni da includere nella risposta. |

Nell'app per la logica, per far sì che la richiesta originale ottenga la risposta, tutti i passaggi necessari devono essere completati entro *60 secondi*, *salvo se si chiama il flusso di lavoro come app per la logica annidata*. Se non si ottiene alcuna azione di risposta entro 60 secondi, si verifica il timeout della richiesta in ingresso, che riceverà una risposta HTTP **408 - Timeout richiesta**. Per le app per la logica annidate, l'app per la logica padre resta in attesa di una risposta fino al completamento, indipendentemente dal tempo necessario.

## <a name="advanced-endpoint-configuration"></a>Configurazione dell'endpoint avanzata

Le app per la logica dispongono di supporto integrato per l'endpoint di accesso diretto e usano sempre il metodo `POST` per avviare l'esecuzione. In precedenza l'app per le API **Listener HTTP** supportava anche la modifica dei segmenti dell'URL e del metodo HTTP. È anche possibile configurare sicurezza aggiuntiva o un dominio personalizzato aggiungendo questi elementi all'host dell'app per le API, ovvero l'app Web che ospita l'app per le API. 

Questa funzionalità è disponibile tramite **Gestione API**:

* [Impostare il metodo della richiesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Modificare i segmenti dell'URL della richiesta](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurare i domini di Gestione API nella scheda **Configura** del portale di Azure classico.
* Impostare i criteri per verificare l'autenticazione di base.

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Riepilogo della migrazione da 2014-12-01-preview

| 2014-12-01-preview | 2016-06-01 |
| --- | --- |
| Fare clic sull'app per le API **Listener HTTP** |Fare clic su **Attivazione manuale**. Non è necessaria un'app per le API. |
| Impostazione di Listener HTTP "*Send response automatically*" (Invia risposta automaticamente) |Includere o meno un'azione **Response** nella definizione del flusso di lavoro. |
| Configurare l'autenticazione di base o OAuth |tramite Gestione API |
| Configurare il metodo HTTP |tramite Gestione API |
| Configurare il percorso relativo |tramite Gestione API |
| Fare riferimento all'oggetto body in ingresso tramite `@triggerOutputs().body.Content` |Riferimento tramite `@triggerOutputs().body` |
| **Send HTTP response** (Invia risposta HTTP) nel Listener HTTP |Fare clic su **Respond to HTTP request** (Rispondi alla richiesta HTTP). Non è necessaria un'app per le API. |

[1]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[2]: ./media/logic-apps-http-endpoint/manualtrigger.png
[3]: ./media/logic-apps-http-endpoint/response.png

