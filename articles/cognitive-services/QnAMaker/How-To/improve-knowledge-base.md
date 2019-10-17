---
title: Migliorare una knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: L'apprendimento attivo consente di migliorare la qualità della knowledge base suggerendo domande alternative, basate sugli invii dell'utente, alla coppia di domanda e risposta. L'utente revisiona tali suggerimenti, aggiungendoli alle domande esistenti o rifiutandoli. La knowledge base non viene modificata automaticamente. È necessario accettare i suggerimenti per rendere effettive le modifiche. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 83d60487922e3355aab8e34f6a8409c529901d14
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72328023"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Usare l'apprendimento attivo per migliorare la knowledge base

L'apprendimento attivo consente di migliorare la qualità della knowledge base suggerendo domande alternative, basate sugli invii dell'utente, alla coppia di domanda e risposta. L'utente revisiona tali suggerimenti, aggiungendoli alle domande esistenti o rifiutandoli. 

La knowledge base non viene modificata automaticamente. Per rendere effettive le modifiche, è necessario accettare i suggerimenti. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.

## <a name="what-is-active-learning"></a>Che cos'è l'apprendimento attivo?

QnA Maker impara nuove variazioni delle domande con feedback implicito ed esplicito.
 
* [Commenti impliciti](#how-qna-makers-implicit-feedback-works) : il Ranker riconosce quando una domanda utente ha più risposte con punteggi molto vicini e li considera come feedback. Non è necessario eseguire alcuna operazione perché questo avvenga.
* [Feedback esplicito](#how-you-give-explicit-feedback-with-the-train-api) : quando dalla Knowledge base vengono restituite più risposte con variazione minima dei punteggi, l'applicazione client chiede all'utente quale domanda è la domanda corretta. Il feedback esplicito dell'utente viene inviato a QnA Maker con l' [API di training](#train-api). 

Entrambi i metodi forniscono al Ranker query analoghe in cluster.

## <a name="how-active-learning-works"></a>Funzionamento dell'apprendimento attivo

L'apprendimento attivo viene attivato in base ai punteggi delle prime risposte restituite da QnA Maker. Se le differenze tra i punteggi si trovano all'interno di un intervallo ridotto, la query viene considerata un possibile suggerimento, come domanda alternativa, per ognuna delle coppie di QnA possibili. Una volta accettata la domanda consigliata per una coppia di QnA specifica, viene rifiutata per le altre coppie. Dopo aver accettato i suggerimenti, è necessario ricordarsi di salvarli e eseguirne il training.

L'apprendimento attivo fornisce i migliori suggerimenti possibili nei casi in cui gli endpoint ricevono una quantità e una varietà ragionevole di query di utilizzo. Quando 5 o più query simili sono in cluster, ogni 30 minuti QnA Maker suggerisce le domande basate sull'utente alla finestra di progettazione della Knowledge base da accettare o rifiutare. Tutti i suggerimenti sono raggruppati per somiglianza e vengono visualizzati i primi suggerimenti per le domande alternative in base alla frequenza delle determinate query degli utenti finali.

Quando le domande sono consigliate nel portale di QnA Maker, è necessario esaminare e accettare o rifiutare tali suggerimenti. Non è disponibile un'API per gestire i suggerimenti.

## <a name="how-qna-makers-implicit-feedback-works"></a>Funzionamento del feedback implicito di QnA Maker

Il feedback implicito di QnA Maker usa un algoritmo per determinare la prossimità del punteggio, quindi crea suggerimenti di apprendimento attivi. L'algoritmo usato per determinare la prossimità non è un calcolo semplice. Gli intervalli nell'esempio seguente non sono destinati a essere corretti, ma devono essere utilizzati come guida per comprendere l'effetto solo dell'algoritmo.

Quando il punteggio di una domanda è altamente attendibile, ad esempio all'80%, l'intervallo dei punteggi che sono considerati per l'apprendimento attivo è ampio, entro il 10% circa. Se il punteggio di attendibilità si riduce, ad esempio al 40%, anche l'intervallo dei punteggi si riduce, entro il 4% circa. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Come fornire commenti espliciti con l'API Train

È importante che QnA Maker ottengano Commenti espliciti su quali risposte è stata la risposta migliore. Il modo in cui viene determinata la risposta migliore dipende dall'utente e può includere:

* Commenti e suggerimenti degli utenti, selezionando una delle risposte.
* Logica di business, ad esempio la determinazione di un intervallo di punteggi accettabile.  
* Combinazione di feedback degli utenti e logica di business.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Aggiornare la versione di runtime per usare l'apprendimento attivo

L'apprendimento attivo è supportato nella versione del runtime 4.4.0 e versioni successive. Se la knowledge base è stata creata in una versione precedente, [aggiornare il runtime](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) per usare questa funzionalità. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Attivare l'apprendimento attivo per visualizzare i suggerimenti

L'apprendimento attivo è disattivato per impostazione predefinita. Attivarlo per visualizzare le domande suggerite. Dopo aver attivato l'apprendimento attivo, è necessario inviare le informazioni dall'app client a QnA Maker. Per altre informazioni, vedere [flusso di architettura per l'uso di GenerateAnswer ed eseguire il training delle API da un bot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selezionare **pubblica** per pubblicare la Knowledge base. Le query di apprendimento attivo vengono raccolte solo dall'endpoint di stima dell'API GenerateAnswer. Le query nel riquadro di test nel portale di QnA Maker non influiscano sull'apprendimento attivo.

1. Per attivare l'apprendimento attivo nel portale di QnA Maker, passare all'angolo superiore destro, selezionare il **nome**, passare a [**Impostazioni servizio**](https://www.qnamaker.ai/UserSettings).  

    ![Attivare le alternative di domande suggerite per l'apprendimento attivo dalla pagina delle impostazioni del servizio. Selezionare il nome utente nel menu in alto a destra, quindi selezionare impostazioni del servizio.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Trovare il servizio QnA Maker, quindi attivare **Active Learning** (Apprendimento attivo). 

    [![Sulla Barra la pagina delle impostazioni del servizio, abilitare o disabilitare la funzionalità di formazione attiva. Se non si è in grado di abilitare o disabilitare la funzionalità, potrebbe essere necessario aggiornare il servizio.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > La versione esatta sull'immagine precedente viene mostrata solo come esempio. La versione può essere diversa. 

    Una volta abilitata l' **apprendimento attivo** , la Knowledge base suggerisce nuove domande a intervalli regolari in base alle domande inviate dall'utente. È possibile disabilitare l'**apprendimento attivo** selezionando nuovamente l'impostazione.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Accetta un suggerimento di apprendimento attivo nella Knowledge base

L'apprendimento attivo modifica la Knowledge base o servizio di ricerca dopo l'approvazione del suggerimento, quindi Salva e Esegui il training. Se si approva il suggerimento, verrà aggiunto come domanda alternativa.

1. Per visualizzare le domande suggerite, nella pagina **modifica** Knowledge Base selezionare Visualizza **Opzioni**, quindi selezionare Mostra suggerimenti di **apprendimento attivi**. 

    [![Sulla Barra la sezione Edit del portale, selezionare Show suggestions (Mostra suggerimenti) per visualizzare le nuove alternative della domanda di formazione attiva.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrare la Knowledge base con coppie di domande e risposte per visualizzare solo i suggerimenti selezionando **Filtra per suggerimenti**.

    [![Use l'interruttore Filtra per suggerimenti per visualizzare solo le alternative alla domanda consigliata di apprendimento attivo.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Ogni coppia di QnA suggerisce le nuove alternative di domanda con un segno di spunta, `✔`, per accettare la domanda o un `x` per rifiutare i suggerimenti. Selezionare il segno di spunta per aggiungere la domanda. 

    [![Selezionare o rifiutare le alternative della domanda consigliata di Active Learning selezionando il segno di spunta verde o il contrassegno di eliminazione rosso.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    È possibile aggiungere o eliminare _tutti i suggerimenti_ selezionando **Aggiungi tutto** o **rifiuta tutto** nella barra degli strumenti contestuale.

1. Selezionare **Save and Train** (Salva ed esegui il training) per salvare le modifiche alla knowledge base.

1. Selezionare **pubblica** per consentire la disponibilità delle modifiche dall' [API GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Quando 5 o più query simili sono in cluster, ogni 30 minuti QnA Maker suggerisce le domande alternative da accettare o rifiutare.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Flusso di architettura per l'uso di GenerateAnswer e il training di API da un bot

Un bot o un'altra applicazione client deve usare il flusso di architettura seguente per usare l'apprendimento attivo:

* Bot [ottiene la risposta dalla Knowledge base](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) con l'API GenerateAnswer, usando la proprietà `top` per ottenere una serie di risposte.
* Bot determina il feedback esplicito:
    * Usando la [logica di business personalizzata](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), filtrare i punteggi ridotti.
    * Nell'applicazione bot o client visualizzare l'elenco delle risposte possibili all'utente e ottenere la risposta selezionata dall'utente.
* Bot [Invia la risposta selezionata a QnA Maker](#bot-framework-sample-code) con l' [API Train](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Usare la proprietà Top nella richiesta GenerateAnswer per ottenere diverse risposte corrispondenti

Quando si invia una domanda a QnA Maker per una risposta, la proprietà `top` del corpo JSON imposta il numero di risposte da restituire. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Usare la proprietà Score insieme alla logica di business per ottenere l'elenco delle risposte per visualizzare l'utente

Quando l'applicazione client, ad esempio una chat bot, riceve la risposta, vengono restituite le prime tre domande. Utilizzare la proprietà `score` per analizzare la vicinanza tra i punteggi. Questo intervallo di prossimità è determinato dalla logica di business. 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
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

|Proprietà della richiesta HTTP|name|Type|Finalità|
|--|--|--|--|
|Parametro di route URL|ID Knowledge base|string|L'identificatore univoco globale della Knowledge Base.|
|Sottodominio personalizzato|Nome della risorsa QnAMaker|string|Il nome della risorsa viene usato come sottodominio personalizzato per la QnA Maker. Questa operazione è disponibile nella pagina impostazioni dopo la pubblicazione della Knowledge base. Viene elencato come `host`.|
|Intestazione|Content-Type|string|tipo di supporto del corpo inviato all'API. Il valore predefinito è: `application/json`|
|Intestazione|Authorization|string|la chiave endpoint (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo post|Oggetto JSON|JSON|Il feedback di formazione|

Il corpo JSON ha diverse impostazioni:

|Proprietà corpo JSON|Type|Finalità|
|--|--|--|--|
|`feedbackRecords`|array|Elenco di commenti.|
|`userId`|string|ID utente della persona che accetta le domande suggerite. Il formato dell'ID utente è l'utente. Un indirizzo di posta elettronica, ad esempio, può essere un ID utente valido nell'architettura. facoltativo.|
|`userQuestion`|string|Testo esatto della query dell'utente. Richiesto.|
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

Nell'applicazione sul lato client, ad esempio un bot, è possibile archiviare i dati, quindi inviare molti record in un singolo corpo JSON nella matrice `feedbackRecords`. 

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

Nell' [esempio Azure bot](https://aka.ms/activelearningsamplebot)sono state programmate entrambe le attività. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Codice C# di esempio per l'API Train con bot Framework 4. x

Il codice seguente illustra come inviare informazioni a QnA Maker con l'API Train. Questo [esempio di codice completo](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) è disponibile in GitHub.

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Codice node. js di esempio per l'API Train con bot Framework 4. x 

Il codice seguente illustra come inviare informazioni a QnA Maker con l'API Train. Questo [esempio di codice completo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) è disponibile in GitHub.

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

Quando l'app è abilitata per l'apprendimento attivo e si esporta l'app, la colonna `SuggestedQuestions` nel file TSV mantiene i dati di apprendimento attivi. 

La colonna `SuggestedQuestions` è un oggetto JSON di informazioni sui commenti impliciti, `autosuggested` e espliciti `usersuggested`. Un esempio di questo oggetto JSON per un singolo argomento inviato dall'utente `help` è:

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

È anche possibile usare l'API Scarica modifiche per esaminare queste modifiche, usando REST o uno degli SDK basati sul linguaggio:
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Quando si reimporta questa app, l'apprendimento attivo continua a raccogliere informazioni e consigli per la Knowledge base. 



## <a name="best-practices"></a>Procedure consigliate

Per le procedure consigliate quando si usa l'apprendimento attivo, vedere [Procedure consigliate](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Passaggi successivi
 
> [!div class="nextstepaction"]
> [Usare i metadati con l'API GenerateAnswer](metadata-generateanswer-usage.md)
