---
title: Metadati con l'API GenerateAnswer - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker consente di aggiungere metadati, sotto forma di coppie chiave/valore, per i set di domande/risposte. È possibile filtrare i risultati per le query utente e archiviare informazioni aggiuntive che possono essere usate nelle conversazioni di completamento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/12/2019
ms.author: diberry
ms.openlocfilehash: 8f00ffeff4eb353fa70aa7df60b14c97d4b8e724
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554866"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Ottenere una risposta con l'API e i metadati di GenerateAnswer

Per ottenere la risposta prevista alla domanda di un utente, usare l'API GenerateAnswer. Quando si pubblica una Knowledge base, è possibile visualizzare informazioni su come usare questa API nella pagina **pubblica** . È anche possibile configurare l'API per filtrare le risposte in base ai tag dei metadati e testare la Knowledge base dall'endpoint con il parametro della stringa di query di test.

QnA Maker consente di aggiungere ai set di domande e risposte i metadati, sotto forma di coppie chiave-valore. È quindi possibile usare queste informazioni per filtrare i risultati per le query utente e per archiviare informazioni aggiuntive che possono essere usate nelle conversazioni di completamento. Per altre informazioni, vedere [Knowledge base](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Archiviare domande e risposte con un'entità QnA

È importante comprendere in che modo QnA Maker archivia i dati relativi a domande e risposte. La figura seguente mostra un'entità di tipo Domande e risposte:

![Illustrazione di un'entità QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Ogni entità di tipo Domande e risposte ha un ID univoco e persistente. È possibile usare l'ID per apportare aggiornamenti a una particolare entità QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Ottenere le stime delle risposte con l'API GenerateAnswer

Usare l' [API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) nel bot o nell'applicazione per eseguire una query sulla Knowledge base con una domanda per l'utente, per ottenere la migliore corrispondenza tra i set di domande e risposte.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Pubblica per ottenere l'endpoint GenerateAnswer 

Dopo aver pubblicato la Knowledge base, dal portale di [QnA Maker](https://www.qnamaker.ai)o usando l' [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), è possibile ottenere i dettagli dell'endpoint GenerateAnswer.

Per ottenere i dettagli dell'endpoint:
1. Accedere a [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. In **Knowledge**base selezionare **Visualizza codice** per la Knowledge base.
    ![Screenshot delle Knowledge base ](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Ottenere i dettagli dell'endpoint GenerateAnswer.

    ![Schermata dei dettagli dell'endpoint](../media/qnamaker-how-to-metadata-usage/view-code.png)

È anche possibile ottenere i dettagli dell'endpoint dalla scheda **Settings** (Impostazioni) della knowledge base.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configurazione della richiesta GenerateAnswer

È possibile chiamare GenerateAnswer con una richiesta HTTP POST. Per il codice di esempio che illustra come chiamare GenerateAnswer, vedere le [guide introduttive](../quickstarts/create-publish-kb-csharp-sdk.md#generate-an-answer-from-the-knowledge-base). 

La richiesta POST USA:

* [Parametri URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters) obbligatori
* [Proprietà dell'intestazione](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer)obbligatoria, `Authorization`, per la sicurezza
* [Proprietà del corpo](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)obbligatorie. 

L'URL di GenerateAnswer ha il formato seguente: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Ricordarsi di impostare la proprietà dell'intestazione HTTP di `Authorization` con un valore della stringa `EndpointKey` con uno spazio finale, quindi la chiave dell'endpoint trovata nella pagina **Impostazioni** .

Un esempio di corpo JSON ha un aspetto simile al seguente:

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

Altre informazioni su [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Il codice JSON precedente ha richiesto solo le risposte al 30% o oltre il Punteggio di soglia. 

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Proprietà della risposta GenerateAnswer

La [risposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) è un oggetto JSON che include tutte le informazioni necessarie per visualizzare la risposta e il successivo turno nella conversazione, se disponibile.

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

Il codice JSON precedente ha risposto con una risposta con un punteggio pari al 38,5%. 

## <a name="use-qna-maker-with-a-bot-in-c"></a>Usare QnA Maker con un bot inC#

Bot Framework fornisce l'accesso alle proprietà del QnA Maker con l' [API getanswer](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

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

Il codice JSON precedente ha richiesto solo le risposte al 30% o oltre il Punteggio di soglia. 

Il bot di supporto presenta [un esempio](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) di questo codice.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Usare QnA Maker con un bot in node. js

Bot Framework fornisce l'accesso alle proprietà del QnA Maker con l' [API getanswer](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

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

Il codice JSON precedente ha richiesto solo le risposte al 30% o oltre il Punteggio di soglia. 

Il bot di supporto presenta [un esempio](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) di questo codice.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Usare i metadati per filtrare le risposte in base ai tag dei metadati personalizzati

L'aggiunta di metadati consente di filtrare le risposte in base a questi tag dei metadati. Aggiungere la colonna metadati dal menu **Opzioni di visualizzazione** . Aggiungere metadati alla Knowledge base selezionando l'icona di **+** dei metadati per aggiungere una coppia di metadati. Questa coppia è costituita da una chiave e un valore.

![Screenshot dell'aggiunta di metadati](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrare i risultati con strictFilters per i tag dei metadati

Si consideri la domanda utente "quando l'hotel si chiude?", dove lo scopo è implicito per il ristorante "Paradise".

Poiché i risultati sono necessari solo per il ristorante "Paradise", è possibile impostare un filtro nella chiamata GenerateAnswer sui metadati "nome ristorante". Nell'esempio seguente viene illustrato quanto segue:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usare i risultati di domande e risposte per gestire il contesto di conversazione

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

## <a name="match-questions-only-by-text"></a>Corrisponde solo alle domande, testo

Per impostazione predefinita, QnA Maker cerca le domande e le risposte. Se si desidera cercare solo le domande, per generare una risposta, utilizzare il `RankerType=QuestionOnly` nel corpo POST della richiesta GenerateAnswer.

È possibile eseguire ricerche nella KB pubblicata, usando `isTest=false` o nella KB di test usando `isTest=true`.

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
|2xx|Success|
|400|I parametri della richiesta non sono corretti, vale a dire che i parametri obbligatori mancano, non solo validi o sono troppo grandi|
|400|Il corpo della richiesta non è corretto, vale a dire che la stringa JSON manca, non è valida o è troppo grande|
|401|Chiave non valida|
|403|Operazione non consentita: non si dispone delle autorizzazioni corrette|
|404|La knowledge base non esiste|
|410|L'API è deprecata e non è più disponibile|

## <a name="next-steps"></a>Passaggi successivi

La pagina di **pubblicazione** fornisce anche le informazioni per generare una risposta con [postazione](../Quickstarts/get-answer-from-kb-using-postman.md) e [curl](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Creazione di un bot della Knowledge base](../tutorials/integrate-qnamaker-luis.md)
