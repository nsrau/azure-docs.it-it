---
title: Migliorare una knowledge base - QnA Maker
description: Migliora la qualità della tua base di conoscenze con l'apprendimento attivo. Rivedere, accettare o rifiutare, aggiungere senza rimuovere o modificare le domande esistenti.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1eb0ed42f700c14350a5e4f1eff9b7592cbf8ef6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474891"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Accettare le domande suggerite per l'apprendimento attivo nella Knowledge Base


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Apprendimento attivo modifica la Knowledge Base o il servizio di ricerca dopo l'approvazione del suggerimento, quindi il salvataggio e il training. Se si approva il suggerimento, questo verrà aggiunto come domanda alternativa.

## <a name="turn-on-active-learning"></a>Attivare l'apprendimento attivo

Per visualizzare le domande suggerite, è necessario [attivare l'apprendimento attivo](use-active-learning.md) per la risorsa QnA Maker.

## <a name="view-suggested-questions"></a>Visualizza le domande suggerite

1. Per visualizzare le domande suggerite, nella pagina **Modifica** Knowledge Base selezionare **Opzioni di visualizzazione**, quindi **selezionare Mostra suggerimenti**di apprendimento attivi .

    [![Nella sezione Modifica del portale selezionare Mostra suggerimenti per visualizzare le nuove alternative di domanda dell'apprendimento attivo.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrare la Knowledge Base con coppie di domande e risposte per visualizzare solo i suggerimenti selezionando **Filtra per suggerimenti**.

    [![Usa l'opzione Filtra per suggerimenti per visualizzare solo le alternative alle domande suggerite dall'apprendimento attivo.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Ogni coppia QnA suggerisce le nuove alternative `✔` di domanda con `x` un segno di spunta, , per accettare la domanda o un per rifiutare i suggerimenti. Selezionare il segno di spunta per aggiungere la domanda.

    [![Selezionare o rifiutare le alternative di domanda suggerite dall'apprendimento attivo selezionando il segno di spunta verde o il segno di eliminazione rosso.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    È possibile aggiungere o eliminare _tutti i suggerimenti_ selezionando Aggiungi **tutto** o **Rifiuta tutto** nella barra degli strumenti contestuale.

1. Selezionare **Save and Train** (Salva ed esegui il training) per salvare le modifiche alla knowledge base.

1. Selezionare **Pubblica** per consentire la disponibilità delle modifiche nell'API [GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Quando sono raggruppate 5 o più query simili, ogni 30 minuti, QnA Maker suggerisce le domande alternative da accettare o rifiutare.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Flusso dell'architettura per l'uso di GenerateAnswer e del training delle API da un botArchitectural flow for using GenerateAnswer and Train APIs from a bot

Un bot o un'altra applicazione client deve usare il flusso dell'architettura seguente per usare l'apprendimento attivo:A bot or other client application should use the following architectural flow to use active learning:

* Bot [ottiene la risposta dalla Knowledge Base](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) con `top` l'API GenerateAnswer, usando la proprietà per ottenere una serie di risposte.
* Bot determina il feedback esplicito:
    * Utilizzando la propria [logica di business personalizzata,](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)filtrare i punteggi bassi.
    * Nel bot o nell'applicazione client visualizzare l'elenco delle possibili risposte all'utente e ottenere la risposta selezionata dall'utente.
* Bot [invia la risposta selezionata a QnA Maker](#bot-framework-sample-code) con l'API [Train](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Utilizzare la proprietà top nella richiesta GenerateAnswer per ottenere diverse risposte corrispondentiUse the top property in the GenerateAnswer request to get several matching answers

Quando si invia una domanda a QnA `top` Maker per una risposta, la proprietà del corpo JSON imposta il numero di risposte da restituire.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Usare la proprietà score insieme alla logica di business per ottenere un elenco di risposte da mostrare all'utente

Quando l'applicazione client (ad esempio un bot di chat) riceve la risposta, vengono restituite le prime 3 domande. Utilizzare `score` la proprietà per analizzare la prossimità tra i punteggi. Questo intervallo di prossimità è determinato dalla propria logica aziendale.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Follow-up dell'applicazione client in caso di domande con punteggi simili

L'applicazione client visualizza le domande con un'opzione per l'utente di selezionare _la singola domanda_ che più rappresenta la loro intenzione.

Una volta che l'utente seleziona una delle domande esistenti, l'applicazione client invia la scelta dell'utente come feedback utilizzando l'API Train di QnA Maker. Questo feedback completa il ciclo di feedback di apprendimento attivo.

## <a name="train-api"></a>API Train

Il feedback di apprendimento attivo viene inviato a QnA Maker con la richiesta Train API POST. La firma dell'API è:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Proprietà di richiesta HTTP|Nome|Type|Scopo|
|--|--|--|--|
|Parametro route URL|ID della knowledge Base|string|L'identificatore univoco globale della Knowledge Base.|
|Sottodominio personalizzato|Nome risorsa QnAMaker|string|Il nome della risorsa viene utilizzato come sottodominio personalizzato per QnA Maker. Questa opzione è disponibile nella pagina Impostazioni dopo la pubblicazione della Knowledge Base. È elencato `host`come file .|
|Intestazione|Content-Type|string|tipo di supporto del corpo inviato all'API. Il valore predefinito è:`application/json`|
|Intestazione|Autorizzazione|string|la chiave endpoint (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo post|Oggetto JSON|JSON|Il feedback della formazione|

Il corpo JSON ha diverse impostazioni:The JSON body has several settings:

|Proprietà del corpo JSON|Type|Scopo|
|--|--|--|--|
|`feedbackRecords`|array|Elenco di feedback.|
|`userId`|string|ID utente della persona che accetta le domande suggerite. Il formato dell'ID utente è a voi. Ad esempio, un indirizzo di posta elettronica può essere un ID utente valido nell'architettura. Facoltativa.|
|`userQuestion`|string|Testo esatto della query dell'utente. Obbligatorio.|
|`qnaID`|d'acquisto|ID della domanda, trovato nella [risposta GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Un corpo JSON di esempio è simile al seguente:An example JSON body looks like:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Una risposta corretta restituisce lo stato 204 e nessun corpo della risposta JSON.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Batch di molti record di feedback in una singola chiamata

Nell'applicazione lato client, ad esempio un bot, è possibile archiviare i dati, quindi inviare molti record in un singolo corpo JSON nella `feedbackRecords` matrice.

Un corpo JSON di esempio è simile al seguente:An example JSON body looks like:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Codice di esempio del framework botBot framework sample code

Il codice del framework del bot deve chiamare l'API Train, se la query dell'utente deve essere usata per l'apprendimento attivo. Ci sono due pezzi di codice da scrivere:

* Determinare se la query deve essere utilizzata per l'apprendimento attivoDetermine if query should be used for active learning
* Inviare una query all'API Train di QnA Maker per l'apprendimento attivo

[Nell'esempio Bot](https://aka.ms/activelearningsamplebot)di Azure entrambe le attività sono state programmate.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Esempio di codice in C'è per l'API Train con Bot Framework 4.x

The following code illustrates how to send information back to QnA Maker with the Train API.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Esempio di codice Node.js per l'API Train con Bot Framework 4.xExample Node.js code for Train API with Bot Framework 4.x

The following code illustrates how to send information back to QnA Maker with the Train API.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>L'apprendimento attivo viene salvato nella Knowledge Base esportata

Quando l'app ha attivato l'apprendimento attivo `SuggestedQuestions` e si esporta l'app, la colonna nel file tsv mantiene i dati di apprendimento attivi.

La `SuggestedQuestions` colonna è un oggetto JSON `autosuggested`di informazioni `usersuggested` di feedback implicito, ed esplicito. Un esempio di questo oggetto JSON per `help` una singola domanda inviata dall'utente è:An example of this JSON object for a single user-submitted question of is:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

È anche possibile usare l'API di modifiche di download per esaminare queste modifiche, usando REST o uno degli SDK basati sul linguaggio:You can also use the download editations API to review these alterations, using REST or any of the language-based SDKs:
* [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Quando reimporti questa app, l'apprendimento attivo continua a raccogliere informazioni e consiglia suggerimenti per la tua Knowledge Base.



## <a name="best-practices"></a>Procedure consigliate

Per le procedure consigliate quando si usa l'apprendimento attivo, vedere [Procedure consigliate](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare metadati con l'API GenerateAnswer](metadata-generateanswer-usage.md)
