---
title: 'Guida introduttiva: Usare cURL e REST per gestire una knowledge base - QnA Maker'
description: Questo argomento di avvio rapido illustra come creare, pubblicare e sottoporre a query una knowledge base usando le API REST.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 04/13/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: 11a7bd8655d1b5606c3b53ed78e796bc42f85b2e
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777427"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Guida introduttiva: Usare cURL e REST per gestire una knowledge base

Questo argomento di avvio rapido illustra come creare, pubblicare e sottoporre a query una knowledge base. QnA Maker estrae automaticamente domande e risposte da contenuto semistrutturato, come le domande frequenti, delle [origini dati](../Concepts/knowledge-base.md). Il modello per la knowledge base è definito nel codice JSON inviato nel corpo della richiesta API.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* La versione corrente di [cURL](https://curl.haxx.se/). Negli argomenti di avvio rapido vengono usate diverse opzioni della riga di comando, indicate nella [documentazione di cURL](https://curl.haxx.se/docs/manpage.html).
* È necessario avere una [risorsa QnA Maker](../How-To/set-up-qnamaker-service-azure.md), per usare la chiave e il nome della risorsa. Durante la creazione della risorsa si è immesso il **nome**, quindi la chiave è stata creata automaticamente. Il nome della risorsa viene usato come sottodominio per l'endpoint. Per recuperare la chiave e il nome della risorsa, selezionare **Avvio rapido** nel portale di Azure per la risorsa. Il nome della risorsa è il primo sottodominio dell'URL dell'endpoint:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> Negli esempi BASH seguenti viene usato il carattere di continuazione riga `\`. Se la console o il terminale usa un carattere di continuazione riga diverso, usare questo carattere.

## <a name="create-a-knowledge-base"></a>Creare una knowledge base

Per creare una knowledge base con le API REST e cURL, è necessario avere le informazioni seguenti:

|Informazioni|Configurazione di cURL|Scopo|
|--|--|--|
|Nome della risorsa di QnA Maker|URL|Usato per creare l'URL|
|Chiave della risorsa di QnA Maker|Parametro `-h` per l'intestazione `Ocp-Apim-Subscription-Key`|Eseguire l'autenticazione per il servizio QnA Maker|
|JSON che descrive la knowledge base|Parametro `-d`|[Esempi](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) di JSON|
|Dimensioni di JSON in byte|Parametro `-h` per l'intestazione `Content-Size`||

Il comando cURL viene eseguito da una shell BASH. Modificare questo comando con il nome e la chiave della risorsa e con i valori e le dimensioni di JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

La risposta cURL di QnA Maker include `operationId`, che è necessario per [ottenere lo stato dell'operazione](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>Ottenere lo stato dell'operazione

Quando si crea una knowledge base, poiché l'operazione è asincrona, la risposta include informazioni per determinare lo stato.

|Informazioni|Configurazione di cURL|Scopo|
|--|--|--|
|Nome della risorsa di QnA Maker|URL|Usato per creare l'URL|
|ID operazione|Route dell'URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Chiave della risorsa di QnA Maker|Parametro `-h` per l'intestazione `Ocp-Apim-Subscription-Key`|Eseguire l'autenticazione per il servizio QnA Maker|

Il comando cURL viene eseguito da una shell BASH. Modificare questo comando con il nome e la chiave della risorsa e con l'ID operazione.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

La risposta cURL include lo stato. Se lo stato indica che l'operazione è riuscita, `resourceLocation` include l'ID della knowledge base.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Pubblicare una knowledge base

Prima di eseguire una query sulla knowledge base, è necessario:
* Pubblicare una knowledge base
* Ottenere la chiave dell'endpoint di runtime

Questa attività pubblica la knowledge base. Il recupero della chiave dell'endpoint di runtime è un'[attività separata](#get-published-knowledge-bases-runtime-endpoint-key).

|Informazioni|Configurazione di cURL|Scopo|
|--|--|--|
|Nome della risorsa di QnA Maker|URL|Usato per creare l'URL|
|Chiave della risorsa di QnA Maker|Parametro `-h` per l'intestazione `Ocp-Apim-Subscription-Key`|Eseguire l'autenticazione per il servizio QnA Maker|
|ID knowledge base|Route dell'URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Il comando cURL viene eseguito da una shell BASH. Modificare questo comando con il nome e la chiave della risorsa e con l'ID knowledge base.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Lo stato della risposta è 204 senza risultati. Usare il parametro della riga di comando `-v` per visualizzare l'output dettagliato del comando cURL, che include lo stato HTTP.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Ottenere la chiave dell'endpoint di runtime della knowledge base pubblicata

Prima di eseguire una query sulla knowledge base, è necessario:
* Pubblicare una knowledge base
* Ottenere la chiave dell'endpoint di runtime

Questa attività ottiene la chiave dell'endpoint di runtime. La pubblicazione della knowledge base è un'[attività separata](#publish-knowledge-base).

La chiave dell'endpoint di runtime è la stessa chiave per tutte le knowledge base che usano la risorsa di QnA Maker.

|Informazioni|Configurazione di cURL|Scopo|
|--|--|--|
|Nome della risorsa di QnA Maker|URL|Usato per creare l'URL|
|Chiave della risorsa di QnA Maker|Parametro `-h` per l'intestazione `Ocp-Apim-Subscription-Key`|Eseguire l'autenticazione per il servizio QnA Maker|

Il comando cURL viene eseguito da una shell BASH. Modificare questo comando con il nome e la chiave della risorsa.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


La risposta cURL include le chiavi dell'endpoint di runtime. Usare solo una delle chiavi durante l'esecuzione di una query per ottenere una risposta dalla knowledge base.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>Eseguire un query per ottenere una risposta dalla knowledge base pubblicata

Il recupero di una risposta dalla knowledge base si esegue da un runtime separato rispetto a quello per la gestione della knowledge base. Poiché si tratta di un runtime separato, è necessario eseguire l'autenticazione con una chiave di runtime.

|Informazioni|Configurazione di cURL|Scopo|
|--|--|--|
|Nome della risorsa di QnA Maker|URL|Usato per creare l'URL|
|Chiave di runtime di QnA Maker|Parametro `-h` per l'intestazione `Authorization`|La chiave fa parte di una stringa che include la parola `Endpointkey `. Eseguire l'autenticazione per il servizio QnA Maker|
|ID knowledge base|Route dell'URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON che descrive la query|Parametro `-d`|[Parametri del corpo della richiesta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) ed [esempi](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) di JSON|
|Dimensioni di JSON in byte|Parametro `-h` per l'intestazione `Content-Size`||

Il comando cURL viene eseguito da una shell BASH. Modificare questo comando con il nome e la chiave della risorsa e con l'ID knowledge base.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Una risposta con esito positivo include la risposta migliore insieme ad altre informazioni che un'applicazione client, ad esempio un chatbot, deve visualizzare in una risposta all'utente.

## <a name="delete-knowledge-base"></a>Eliminare una knowledge base

Dopo aver completato le operazioni con la knowledge base, eliminarla.

|Informazioni|Configurazione di cURL|Scopo|
|--|--|--|
|Nome della risorsa di QnA Maker|URL|Usato per creare l'URL|
|Chiave della risorsa di QnA Maker|Parametro `-h` per l'intestazione `Ocp-Apim-Subscription-Key`|Eseguire l'autenticazione per il servizio QnA Maker|
|ID knowledge base|Route dell'URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Il comando cURL viene eseguito da una shell BASH. Modificare questo comando con il nome e la chiave della risorsa e con l'ID knowledge base.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Lo stato della risposta è 204 senza risultati. Usare il parametro della riga di comando `-v` per visualizzare l'output dettagliato del comando cURL, che include lo stato HTTP.

## <a name="additional-resources"></a>Risorse aggiuntive

* Documentazione di riferimento sulla [creazione](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* Documentazione di riferimento sul [runtime](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/)
* [Script BASH di esempio con cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
