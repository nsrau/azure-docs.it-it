---
title: Metadati con l'API GenerateAnswer - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker consente di aggiungere metadati, sotto forma di coppie chiave/valore, per i set di domande/risposte. È possibile filtrare i risultati per le query utente e archiviare informazioni aggiuntive che possono essere usate nelle conversazioni di follow-up.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: 6bfcb531d0e4e8073a5553f7bc84a25e4f8a92a9
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785689"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Ottenere una risposta con l'API di GenerateAnswer e metadati

Per ottenere la risposta stimata alla domanda dell'utente, usare l'API GenerateAnswer. Quando si pubblica una knowledge base, è possibile visualizzare informazioni su come usare questa API nella **pubblica** pagina. È anche possibile configurare l'API per filtrare le risposte basate su tag di metadati e testare la knowledge base dall'endpoint con il parametro di stringa di query di test.

QnA Maker consente di aggiungere i metadati, sotto forma di coppie chiave-valore, nei set di domande e risposte. È quindi possibile usare queste informazioni per filtrare i risultati per le query utente e archiviare informazioni aggiuntive che possono essere usate nelle conversazioni di follow-up. Per altre informazioni, vedere [Knowledge base](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Store domande e risposte con un'entità di domande e risposte

È importante comprendere come QnA Maker archivia i dati di domande e risposte. La figura seguente mostra un'entità di tipo Domande e risposte:

![Illustrazione di un'entità di domande e risposte](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Ogni entità di tipo Domande e risposte ha un ID univoco e persistente. È possibile usare l'ID per eseguire gli aggiornamenti a una particolare entità domande e risposte.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Ottenere le stime di risposte con l'API GenerateAnswer

Si utilizza il [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) del bot o dell'applicazione per eseguire una query la knowledge base con una domanda utente, per ottenere la migliore corrispondenza dalla domanda e la risposta set.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Pubblica per ottenere l'endpoint GenerateAnswer 

Dopo aver pubblicato la knowledge base, dal [portale di QnA Maker](https://www.qnamaker.ai), o tramite il [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), è possibile ottenere i dettagli dell'endpoint GenerateAnswer.

Per ottenere i dettagli dell'endpoint:
1. Accedere a [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Nelle **mia knowledge base**, selezionare **Visualizza codice** per la knowledge base.
    ![Schermata di My knowledge base](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Ottenere i dettagli dell'endpoint GenerateAnswer.

    ![Schermata dei dettagli di endpoint](../media/qnamaker-how-to-metadata-usage/view-code.png)

È anche possibile ottenere i dettagli dell'endpoint dalla scheda **Settings** (Impostazioni) della knowledge base.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configurazione delle richieste GenerateAnswer

È possibile chiamare GenerateAnswer con una richiesta HTTP POST. Per il codice di esempio che illustra come chiamare GenerateAnswer, vedere le [guide introduttive](../quickstarts/csharp.md). 

Usa la richiesta POST:

* Obbligatorio [parametri URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Obbligatorio [proprietà header](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, per la sicurezza
* Obbligatorio [body proprietà](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

L'URL GenerateAnswer ha il formato seguente: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Ricordarsi di impostare la proprietà di intestazione HTTP del `Authorization` con un valore della stringa `EndpointKey` con una parentesi finale spazio quindi la chiave di endpoint trovata nel **impostazioni** pagina.

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

Il [risposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) è un oggetto JSON, incluse tutte le informazioni necessarie per visualizzare la risposta e la successiva istruzione attiva alla conversazione, se disponibile.

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

## <a name="use-qna-maker-with-a-bot-in-c"></a>Usa QnA Maker con un bot inC#

Bot framework consente di accedere alle proprietà di QnA Maker:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Il bot di supporto ha [un esempio](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) con questo codice.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Usa QnA Maker con un bot in Node. js

Bot framework consente di accedere alle proprietà di QnA Maker:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Il bot di supporto ha [un esempio](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) con questo codice.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Usare i metadati per filtrare le risposte in base al tag di metadati personalizzati

Aggiunta di metadati consente di filtrare le risposte da tali tag dei metadati. La colonna di metadati da aggiungere il **opzioni di visualizzazione** menu. Aggiungere i metadati per la knowledge base selezionando i metadati **+** icona per l'aggiunta di una coppia di metadati. Questa coppia è costituita da una chiave e un valore.

![Screenshot dell'aggiunta di metadati](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrare i risultati con strictFilters per i tag dei metadati

Prendere in considerazione la domanda utente "Non vengono eseguiti vicino questo hotel?", dove l'intento è implicito per il ristorante "Paradise."

Poiché i risultati sono necessari solo per il ristorante "Paradise", è possibile impostare un filtro nella chiamata GenerateAnswer sui metadati "Ristorante Name". L'esempio seguente illustra questo:

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

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usare i risultati di domande e risposte per mantenere il contesto conversazione

La risposta per il GenerateAnswer contiene le informazioni sui metadati corrispondente del set di domande e risposte corrispondenti. È possibile usare queste informazioni nell'applicazione client per archiviare il contesto della conversazione precedente per l'uso nelle conversazioni più avanti. 

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

## <a name="match-questions-only-by-text"></a>Domande, solo per testo corrispondono

Per impostazione predefinita, QnA Maker di Cerca con domande e risposte. Se si desidera cercare solo le domande, per generare una risposta, utilizzare il `RankerType=QuestionOnly` nel corpo del POST della richiesta GenerateAnswer.

È possibile cercare la Knowledge Base pubblicata, usando `isTest=false`, o nel test kb usando `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>Passaggi successivi

Il **Publish** pagina inoltre fornisce informazioni per generare una risposta con [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) e [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Creare una knowledge base](./create-knowledge-base.md)
