---
title: Metadati con l'API GenerateAnswer - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker consente di aggiungere metadati, sotto forma di coppie chiave/valore, per i set di domande/risposte. È possibile filtrare i risultati in base alle query utente e archiviare informazioni aggiuntive che possono essere utilizzate nelle conversazioni di follow-up.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 6a8cbabfd4e47c50d2c2e6f4a23c50a931e645a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220644"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Ottenere una risposta con l'API GenerateAnswer e i metadatiGet an answer with the GenerateAnswer API and metadata

To get the predicted answer to a user's question, use the GenerateAnswer API. Quando si pubblica una Knowledge Base, è possibile visualizzare informazioni su come utilizzare questa API nella pagina **Pubblica.When** you publish a knowledge base, you can see information about how to use this API on the Publish page. È inoltre possibile configurare l'API per filtrare le risposte in base ai tag di metadati e testare la Knowledge Base dall'endpoint con il parametro della stringa di query di test.

QnA Maker consente di aggiungere metadati, sotto forma di coppie chiave/valore, ai set di domande e risposte. È quindi possibile utilizzare queste informazioni per filtrare i risultati in base alle query utente e per archiviare informazioni aggiuntive che possono essere utilizzate nelle conversazioni di completamento. Per altre informazioni, vedere [Knowledge base](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Archiviare domande e risposte con un'entità QnA

È importante comprendere in che modo QnA Maker archivia i dati di domande e risposte. La figura seguente mostra un'entità di tipo Domande e risposte:

![Illustrazione di un'entità QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Ogni entità di tipo Domande e risposte ha un ID univoco e persistente. È possibile utilizzare l'ID per apportare aggiornamenti a una determinata entità QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Ottenere stime delle risposte con l'API GenerateAnswerGet answer predictions with the GenerateAnswer API

Utilizzare [l'API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) nel bot o nell'applicazione per interrogare la Knowledge Base con una domanda utente, per ottenere la corrispondenza migliore dai set di domande e risposte.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Pubblica per ottenere l'endpoint GenerateAnswer

Dopo aver pubblicato la Knowledge Base, dal [portale QnA Maker](https://www.qnamaker.ai)o tramite l'API , è possibile ottenere i dettagli dell'endpoint GenerateAnswer. [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)

Per ottenere i dettagli dell'endpoint:
1. Accedere a [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. In **Le mie conoscenze**selezionare **Visualizza codice** per la Knowledge Base.
    ![Screenshot delle basi di conoscenza personali](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Ottenere i dettagli dell'endpoint GenerateAnswer.

    ![Screenshot dei dettagli dell'endpoint](../media/qnamaker-how-to-metadata-usage/view-code.png)

È anche possibile ottenere i dettagli dell'endpoint dalla scheda **Settings** (Impostazioni) della knowledge base.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configurazione della richiesta GenerateAnswer

È possibile chiamare GenerateAnswer con una richiesta HTTP POST. Per il codice di esempio che illustra come chiamare GenerateAnswer, vedere le [guide introduttive](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

La richiesta POST utilizza:

* Parametri [URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters) obbligatori
* Proprietà di `Authorization`intestazione obbligatoria, , per la sicurezza
* Proprietà [del corpo obbligatorie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

L'URL GenerateAnswer ha il seguente formato:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Ricordarsi di impostare `Authorization` la proprietà dell'intestazione HTTP di con un valore della stringa `EndpointKey` con uno spazio finale, quindi la chiave dell'endpoint trovata nella pagina **Impostazioni.**

Un corpo JSON di esempio è simile al seguente:An example JSON body looks like:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Ulteriori informazioni su [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Il codice JSON precedente ha richiesto solo le risposte che si trovano al 30% o al di sopra del punteggio di soglia.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Proprietà di risposta GenerateAnswer

La [risposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) è un oggetto JSON che include tutte le informazioni necessarie per visualizzare la risposta e il turno successivo nella conversazione, se disponibile.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
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

Il codice JSON precedente ha risposto con una risposta con un punteggio del 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>Usare QnA Maker con un bot in CUse QnA Maker with a bot in C #

Il framework del bot fornisce l'accesso alle proprietà di QnA Maker con [l'API getAnswer](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Il codice JSON precedente ha richiesto solo le risposte che si trovano al 30% o al di sopra del punteggio di soglia.

Il bot di supporto ha [un esempio](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) con questo codice.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Usare QnA Maker con un bot in Node.jsUse QnA Maker with a bot in Node.js

Il framework del bot fornisce l'accesso alle proprietà di QnA Maker con [l'API getAnswer](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Il codice JSON precedente ha richiesto solo le risposte che si trovano al 30% o al di sopra del punteggio di soglia.

Il bot di supporto ha [un esempio](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) con questo codice.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Usare i metadati per filtrare le risposte in base a tag di metadati personalizzatiUse metadata to filter answers by custom metadata tags

L'aggiunta di metadati consente di filtrare le risposte in base a questi tag di metadati. Aggiungere la colonna dei metadati dal menu **Opzioni di visualizzazione.** Aggiungere metadati alla Knowledge Base **+** selezionando l'icona dei metadati per aggiungere una coppia di metadati. Questa coppia è costituita da una chiave e un valore.

![Screenshot dell'aggiunta di metadati](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrare i risultati con strictFilters per i tag dei metadati

Si consideri la domanda dell'utente "Quando chiude questo hotel?", dove l'intento è implicito per il ristorante "Paradiso".

Poiché i risultati sono necessari solo per il ristorante "Paradise", è possibile impostare un filtro nella chiamata GenerateAnswer sui metadati "Restaurant Name". Nell'esempio seguente viene illustrato quanto segue:The following example shows this:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usa i risultati di domande e risposte per mantenere il contesto della conversazione

La risposta a GenerateAnswer contiene le informazioni sui metadati corrispondenti del set di domande e risposte corrispondenti. È possibile utilizzare queste informazioni nell'applicazione client per archiviare il contesto della conversazione precedente da utilizzare nelle conversazioni successive.

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

## <a name="match-questions-only-by-text"></a>Corrispondenza solo domande, in base al testo

Per impostazione predefinita, QnA Maker esegue ricerche tra domande e risposte. Se si desidera cercare solo le domande, per `RankerType=QuestionOnly` generare una risposta, utilizzare il nel corpo POST della richiesta GenerateAnswer.

È possibile eseguire una ricerca `isTest=false`nella kb pubblicata, utilizzando o nella kb di test utilizzando `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Errori HTTP comuni

|Codice|Spiegazione|
|:--|--|
|2xx|Operazione completata|
|400|I parametri della richiesta non sono corretti, vale a dire che i parametri obbligatori mancano, non solo validi o sono troppo grandi|
|400|Il corpo della richiesta non è corretto, vale a dire che la stringa JSON manca, non è valida o è troppo grande|
|401|Chiave non valida|
|403|Operazione non consentita: non si dispone delle autorizzazioni corrette|
|404|La knowledge base non esiste|
|410|L'API è deprecata e non è più disponibile|

## <a name="next-steps"></a>Passaggi successivi

La pagina **Pubblica** fornisce inoltre informazioni per [generare](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) una risposta con Postman o cURL.

> [!div class="nextstepaction"]
> [Creare un bot della knowledge base](../tutorials/integrate-qnamaker-luis.md)
