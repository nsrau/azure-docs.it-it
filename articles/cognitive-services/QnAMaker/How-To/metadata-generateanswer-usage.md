---
title: Metadati con l'API GenerateAnswer - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker consente di aggiungere metadati, sotto forma di coppie chiave/valore, per i set di domande/risposte. Queste informazioni possono essere usate per filtrare i risultati per le query utente e per archiviare informazioni aggiuntive utilizzabili nelle conversazioni di follow-up.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: tulasim
ms.openlocfilehash: b634467381dc97e4a733e862e86632a089bf5f67
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765657"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>Ottenere una risposta della Knowledge base con l'API di GenerateAnswer e metadati

Per ottenere la risposta stimata alla domanda dell'utente, usare l'API GenerateAnswer. Quando si pubblica una knowledge base, queste informazioni per usare questa API viene visualizzate nella pagina di pubblicazione. È anche possibile configurare l'API per filtrare le risposte basate su tag di metadati e testare la knowledge base dall'endpoint con il parametro di stringa di query di test.

QnA Maker consente di aggiungere metadati, sotto forma di coppie di chiave e valore, per i set di domande/risposte. Queste informazioni possono essere usate per filtrare i risultati per le query utente e per archiviare informazioni aggiuntive utilizzabili nelle conversazioni di follow-up. Per altre informazioni, vedere [Knowledge base](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>L'archiviazione di domande e risposte con un'entità di domande e risposte

Prima di tutto, è importante comprendere come vengono archiviati da QnA Maker i dati di domanda/risposta. La figura seguente mostra un'entità di tipo Domande e risposte:

![Entità di tipo Domande e risposte](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Ogni entità di tipo Domande e risposte ha un ID univoco e persistente. L'ID può essere usato per aggiornare un'entità di tipo Domande e risposte specifica.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Ottenere le stime di risposte con l'API GenerateAnswer

Usare l'API GenerateAnswer nel bot o nell'applicazione per eseguire una query sulla knowledge base con una domanda di un utente, per ottenere la migliore corrispondenza dal set di domande/risposte.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Pubblica per ottenere l'endpoint GenerateAnswer 

Una volta pubblicata la knowledge base, dal [portale di QnA Maker](https://www.qnamaker.ai) o usando l'[API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), è possibile ottenere i dettagli dell'endpoint GenerateAnswer.

Per ottenere i dettagli dell'endpoint:
1. Accedere a [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. In **My knowledge bases** (Knowledge base personali) fare clic su **View Code (Visualizza codice)** per la knowledge base.
    ![My knowledge bases](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png) (Knowledge base personali)
1. Ottenere i dettagli dell'endpoint GenerateAnswer.

    ![Dettagli dell'endpoint](../media/qnamaker-how-to-metadata-usage/view-code.png)

È anche possibile ottenere i dettagli dell'endpoint dalla scheda **Settings** (Impostazioni) della knowledge base.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configurazione delle richieste GenerateAnswer

È possibile chiamare GenerateAnswer con una richiesta HTTP POST. Per il codice di esempio che illustra come chiamare GenerateAnswer, vedere le [guide introduttive](../quickstarts/csharp.md).

Il **URL della richiesta** ha il formato seguente: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|Proprietà della richiesta HTTP|NOME|Type|Scopo|
|--|--|--|--|
|Parametro di route di URL|ID Knowledge base|string|L'identificatore univoco globale della Knowledge Base.|
|Parametro di route di URL|Host endpoint QnAMaker|string|nome host dell'endpoint distribuito nella sottoscrizione di Azure. È disponibile nella pagina impostazioni dopo la pubblicazione della knowledge base. |
|Intestazione|Content-Type|string|tipo di supporto del corpo inviato all'API. Valore predefinito è: '|
|Intestazione|Authorization|string|la chiave endpoint (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo della richiesta POST|Oggetto JSON|JSON|La domanda con le impostazioni|


Il corpo JSON presenta diverse impostazioni:

|Proprietà del corpo JSON|Obbligatorio|Type|Scopo|
|--|--|--|--|
|`question`|Obbligatoria|string|Una domanda utente da inviare alla knowledge base.|
|`top`|Facoltativo|numero intero|numero di risultati classificati da includere nell'output. Il valore predefinito è 1.|
|`userId`|Facoltativo|string|ID univoco per l'identificazione dell'utente. Questo ID verrà registrato nei log di chat.|
|`scoreThreshold`|Facoltativo|numero intero|Verranno restituite solo le risposte con punteggio di confidenza di sopra di questa soglia. Il valore predefinito è 0.|
|`isTest`|Facoltativo|boolean|Se impostato su true, restituisce risultati da `testkb` indice di ricerca anziché indice pubblicato.|
|`strictFilters`|Facoltativo|string|se specificato, indica a QnA Maker di restituire solo le risposte contenenti i metadati specificati. Usare `none` per indicare risposta non dovrebbe avere alcun filtro di metadati. |

Un esempio del corpo JSON sarà simile a:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Proprietà di risposta GenerateAnswer

Una risposta con esito positivo restituisce uno stato 200 e una risposta JSON. 

|Proprietà di risposte (ordinati in base al punteggio)|Scopo|
|--|--|
|score|punteggio di classificazione, compreso tra 0 e 100.|
|ID|ID univoco assegnato alla risposta.|
|Domande|domande fornite dall'utente.|
|answer|la risposta alla domanda.|
|una sezione source|nome dell'origine da cui la risposta è stata estratta o salvata nella Knowledge Base.|
|metadata|metadati associati alla risposta.|
|Metadata.Name|nome dei metadati. (stringa, lunghezza massima: 100, obbligatorio)|
|Metadata.Value: valore dei metadati. (stringa, lunghezza massima: 100, obbligatorio)|


```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>Uso di metadati consente di filtrare le risposte in base al tag di metadati personalizzati

Aggiunta di metadati consente di filtrare le risposte da tali tag dei metadati. Prendere in considerazione i dati sulle domande frequenti seguenti. Aggiungere i metadati alla knowledge base facendo clic sull'icona dei metadati.

![Aggiungere metadati](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrare i risultati con strictFilters per i tag dei metadati

Considerare la domanda utente "When does this hotel close?" per informazioni su quando chiude l'hotel, dove la finalità è implicitamente riferita al ristorante "Paradise".

Poiché sono richiesti i risultati solo per il ristorante "Paradise", è possibile impostare un filtro nella chiamata a GenerateAnswer sui metadati relativi al nome del ristorante, come indicato di seguito.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usare i risultati di domande e risposte per mantenere il contesto conversazione

La risposta per il GenerateAnswer contiene le informazioni sui metadati corrispondente del set di domanda corrispondente. Queste informazioni è utilizzabile nell'applicazione client per archiviare il contesto della conversazione precedente per l'uso nelle conversazioni più avanti. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

La pagina di pubblicazione fornisce anche informazioni a [generare una risposta](../Quickstarts/get-answer-from-kb-using-postman.md) con Postman e [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Creare una knowledge base](./create-knowledge-base.md)
