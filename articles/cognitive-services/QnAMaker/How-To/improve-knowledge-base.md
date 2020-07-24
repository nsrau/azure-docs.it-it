---
title: Domande consigliate sull'apprendimento attivo-QnA Maker
description: Migliorare la qualità della Knowledge base con l'apprendimento attivo. Verifica, accetta o rifiuta, Aggiungi senza rimuovere o modificare le domande esistenti.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: bb95ddc40e122e2589d0396b94f0de921aab9350
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053926"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Accetta le domande suggerite di apprendimento attivo nella Knowledge base


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

L'apprendimento attivo modifica la Knowledge base o servizio di ricerca dopo l'approvazione del suggerimento, quindi Salva e Esegui il training. Se si approva il suggerimento, verrà aggiunto come domanda alternativa.

## <a name="turn-on-active-learning"></a>Attivare l'apprendimento attivo

Per visualizzare le domande suggerite, è necessario [attivare l'apprendimento attivo](use-active-learning.md) per la risorsa QnA Maker.

## <a name="view-suggested-questions"></a>Visualizza le domande suggerite

1. Per visualizzare le domande suggerite, nella pagina **modifica** Knowledge Base selezionare Visualizza **Opzioni**, quindi selezionare Mostra suggerimenti di **apprendimento attivi**.

    [![Nella sezione Edit (modifica) del portale selezionare Show suggestions (Mostra suggerimenti) per visualizzare le nuove alternative per la domanda relativa ad Active Learning.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrare la Knowledge base con coppie di domande e risposte per visualizzare solo i suggerimenti selezionando **Filtra per suggerimenti**.

    [![Usare l'interruttore Filtra per suggerimenti per visualizzare solo le alternative alla domanda consigliata di apprendimento attivo.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Ogni coppia di QnA suggerisce le nuove alternative di domanda con un segno di spunta, `✔` , per accettare la domanda o un `x` per rifiutare i suggerimenti. Selezionare il segno di spunta per aggiungere la domanda.

    [![Selezionare o rifiutare le alternative alla domanda consigliata di Active Learning selezionando il segno di spunta verde o il contrassegno di eliminazione rosso.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    È possibile aggiungere o eliminare _tutti i suggerimenti_ selezionando **Aggiungi tutto** o **rifiuta tutto** nella barra degli strumenti contestuale.

1. Selezionare **Save and Train** (Salva ed esegui il training) per salvare le modifiche alla knowledge base.

1. Selezionare **pubblica** per consentire la disponibilità delle modifiche dall' [API GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Quando 5 o più query simili sono in cluster, ogni 30 minuti QnA Maker suggerisce le domande alternative da accettare o rifiutare.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Flusso di architettura per l'uso di GenerateAnswer e il training di API da un bot

Un bot o un'altra applicazione client deve usare il flusso di architettura seguente per usare l'apprendimento attivo:

* Bot [ottiene la risposta dalla Knowledge base](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) con l'API GenerateAnswer, usando la `top` proprietà per ottenere una serie di risposte.
* Bot determina il feedback esplicito:
    * Usando la [logica di business personalizzata](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), filtrare i punteggi ridotti.
    * Nell'applicazione bot o client visualizzare l'elenco delle risposte possibili all'utente e ottenere la risposta selezionata dall'utente.
* Bot [Invia la risposta selezionata a QnA Maker](#bot-framework-sample-code) con l' [API Train](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Usare la proprietà Top nella richiesta GenerateAnswer per ottenere diverse risposte corrispondenti

Quando si invia una domanda a QnA Maker per una risposta, la `top` proprietà del corpo JSON imposta il numero di risposte da restituire.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Usare la proprietà Score insieme alla logica di business per ottenere l'elenco delle risposte per visualizzare l'utente

Quando l'applicazione client, ad esempio una chat bot, riceve la risposta, vengono restituite le prime tre domande. Utilizzare la `score` proprietà per analizzare la vicinanza tra i punteggi. Questo intervallo di prossimità è determinato dalla logica di business.

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

L'applicazione client Visualizza le domande con un'opzione che consente all'utente di selezionare _l'unica domanda_ che rappresenta la maggior parte della propria intenzione.

Quando l'utente seleziona una delle domande esistenti, l'applicazione client invia la scelta dell'utente come feedback usando l'API Train di QnA Maker. Questo feedback completa il ciclo di commenti e suggerimenti su Active Learning.

## <a name="train-api"></a>API Train

Il feedback di apprendimento attivo viene inviato a QnA Maker con la richiesta POST dell'API Train. La firma API è:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Proprietà della richiesta HTTP|Nome|Tipo|Scopo|
|--|--|--|--|
|Parametro di route URL|ID della knowledge Base|string|L'identificatore univoco globale della Knowledge Base.|
|Sottodominio personalizzato|Nome della risorsa QnAMaker|string|Il nome della risorsa viene usato come sottodominio personalizzato per la QnA Maker. Questa operazione è disponibile nella pagina impostazioni dopo la pubblicazione della Knowledge base. Viene elencato come `host` .|
|Intestazione|Content-Type|string|tipo di supporto del corpo inviato all'API. Il valore predefinito è:`application/json`|
|Intestazione|Autorizzazione|string|la chiave endpoint (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo post|Oggetto JSON|JSON|Il feedback di formazione|

Il corpo JSON ha diverse impostazioni:

|Proprietà corpo JSON|Tipo|Scopo|
|--|--|--|--|
|`feedbackRecords`|array|Elenco di commenti.|
|`userId`|string|ID utente della persona che accetta le domande suggerite. Il formato dell'ID utente è l'utente. Un indirizzo di posta elettronica, ad esempio, può essere un ID utente valido nell'architettura. facoltativo.|
|`userQuestion`|string|Testo esatto della query dell'utente. Obbligatorio.|
|`qnaID`|d'acquisto|ID della domanda, disponibile nella [risposta GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Un esempio di corpo JSON ha un aspetto simile al seguente:

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

Una risposta con esito positivo restituisce lo stato 204 e nessun corpo della risposta JSON.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Batch di molti record di commenti in una singola chiamata

Nell'applicazione sul lato client, ad esempio un bot, è possibile archiviare i dati, quindi inviare molti record in un singolo corpo JSON nella `feedbackRecords` matrice.

Un esempio di corpo JSON ha un aspetto simile al seguente:

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

## <a name="bot-framework-sample-code"></a>Codice di esempio per bot Framework

Il codice del Framework bot deve chiamare l'API di training, se la query dell'utente deve essere usata per l'apprendimento attivo. Esistono due frammenti di codice da scrivere:

* Determinare se la query deve essere utilizzata per l'apprendimento attivo
* Invia una query all'API Train di QnA Maker per l'apprendimento attivo

Nell' [esempio Azure bot](https://github.com/microsoft/BotBuilder-Samples)sono state programmate entrambe le attività.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Codice C# di esempio per l'API Train con bot Framework 4. x

Il codice seguente illustra come inviare informazioni a QnA Maker con l'API Train.

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Esempio di codice di Node.js per l'API Train con bot Framework 4. x

Il codice seguente illustra come inviare informazioni a QnA Maker con l'API Train.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>L'apprendimento attivo viene salvato nella Knowledge base esportata

Quando l'app è abilitata per l'apprendimento attivo e si esporta l'app, la `SuggestedQuestions` colonna nel file TSV mantiene i dati di apprendimento attivi.

La `SuggestedQuestions` colonna è un oggetto JSON di informazioni sui commenti impliciti, `autosuggested` , e espliciti `usersuggested` . Un esempio di questo oggetto JSON per un singolo argomento inviato dall'utente `help` è:

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

Quando si reimporta questa app, l'apprendimento attivo continua a raccogliere informazioni e consigli per la Knowledge base.



## <a name="best-practices"></a>Procedure consigliate

Per le procedure consigliate quando si usa l'apprendimento attivo, vedere [Procedure consigliate](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare metadati con l'API GenerateAnswer](metadata-generateanswer-usage.md)
