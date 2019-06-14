---
title: Migliorare una knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: L'apprendimento attivo consente di migliorare la qualità della knowledge base suggerendo domande alternative, basate sugli invii dell'utente, alla coppia di domanda e risposta. L'utente revisiona tali suggerimenti, aggiungendoli alle domande esistenti o rifiutandoli. La knowledge base non viene modificata automaticamente. È necessario accettare i suggerimenti per qualsiasi modifica abbia effetto. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/06/2019
ms.author: diberry
ms.openlocfilehash: f8d2f6d9fce6a249a782f959ac7672ac8e123fbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075169"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Usare apprendimento attivo per migliorare la tua knowledge base

L'apprendimento attivo consente di migliorare la qualità della knowledge base suggerendo domande alternative, basate sugli invii dell'utente, alla coppia di domanda e risposta. L'utente revisiona tali suggerimenti, aggiungendoli alle domande esistenti o rifiutandoli. 

La knowledge base non viene modificata automaticamente. Affinché le modifiche abbiano effetto, è necessario accettare i suggerimenti. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.

## <a name="what-is-active-learning"></a>Che cos'è l'apprendimento attivo?

QnA Maker impara nuove variazioni delle domande con feedback implicito ed esplicito.
 
* [Commenti e suggerimenti implicita](#how-qna-makers-implicit-feedback-works) – il permettendoti riconosce quando una domanda utente dispone di più risposte con i punteggi che sono molto simili e lo considera come commenti e suggerimenti. Non è necessario eseguire alcuna operazione per tale scopo.
* [Commenti e suggerimenti esplicita](#how-you-give-explicit-feedback-with-the-train-api) : quando più risposte con qualche piccola variazione nei punteggi vengono restituite dalla knowledge base, l'applicazione client viene chiesto all'utente di cui domanda è la domanda corretta. Commenti e suggerimenti esplicita dell'utente viene inviato a QnA Maker con i [Train API](#train-api). 

Entrambi i metodi forniscono la permettendoti con query analoghe che appartengono a un cluster.

## <a name="how-active-learning-works"></a>Funzionamento dell'apprendimento attivo

Apprendimento attivo viene attivato in base i punteggi delle risposte di alcuni principali restituite da QnA Maker. Se le differenze di punteggio sono situati all'interno di un intervallo di piccole dimensioni, la query viene considerata un suggerimento possibili (come una domanda alternativo) per ognuna delle coppie di domande e risposte possibili. Dopo avere accettato la domanda suggerita per una specifica coppia di domande e risposte, è stata rifiutata per le altre coppie. È necessario ricordarsi di salvare ed eseguire il training, dopo aver accettato i suggerimenti.

L'apprendimento attivo fornisce i migliori suggerimenti possibili nei casi in cui gli endpoint ricevono una quantità e una varietà ragionevole di query di utilizzo. Quando le query di 5 o più simili sono in cluster, ogni 30 minuti, QnA Maker vengono suggerite le domande basate sull'utente per la finestra di progettazione della knowledge base per accettare o rifiutare. Tutti i suggerimenti sono raggruppati per somiglianza e vengono visualizzati i primi suggerimenti per le domande alternative in base alla frequenza delle determinate query degli utenti finali.

Una volta domande vengono suggerite nel portale di QnA Maker, è necessario rivedere e accettare o rifiutare tali suggerimenti. Non è disponibile un'API per gestire i suggerimenti.

## <a name="how-qna-makers-implicit-feedback-works"></a>Funzionamento di commenti e suggerimenti implicita di QnA Maker

Commenti e suggerimenti implicita di QnA Maker utilizza un algoritmo per determinare prossimità di punteggio, verificare i suggerimenti di apprendimento attivo. L'algoritmo usato per determinare la prossimità non è un calcolo semplice. Gli intervalli nell'esempio seguente non sono pensati per essere corretto ma devono essere usati come guida per comprendere quale impatto avrà solo l'algoritmo.

Quando il punteggio di una domanda è altamente attendibile, ad esempio all'80%, l'intervallo dei punteggi che sono considerati per l'apprendimento attivo è ampio, entro il 10% circa. Se il punteggio di attendibilità si riduce, ad esempio al 40%, anche l'intervallo dei punteggi si riduce, entro il 4% circa. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Il Licenziatario fornisca un riscontro esplicita con l'API di training

È importante che QnA Maker ottiene così un riscontro esplicito che sulle risposte è stata la risposta migliore. Come viene determinata la risposta migliore è responsabilità dell'utente e può includere:

* Commenti degli utenti, selezionando una delle risposte.
* La logica di business, ad esempio il rilevamento accettabile assegnare un punteggio intervallo.  
* Una combinazione di entrambi utenti commenti e suggerimenti e logica di business.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Aggiornare la versione di runtime per usare l'apprendimento attivo

L'apprendimento attivo è supportato nella versione del runtime 4.4.0 e versioni successive. Se la knowledge base è stata creata in una versione precedente, [aggiornare il runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) per usare questa funzionalità. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Attivare l'apprendimento attivo visualizzare i suggerimenti

L'apprendimento attivo è disattivato per impostazione predefinita. Attivarlo per visualizzare le domande suggerite. Dopo avere attivato apprendimento attivo, è necessario inviare informazioni dall'app client per QnA Maker. Per altre informazioni, vedere [flusso dell'architettura per l'uso GenerateAnswer e le API di eseguire il training di un bot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selezionare **pubblica** per pubblicare la knowledge base. Apprendimento attivo query vengono raccolti da solo l'endpoint API GenerateAnswer stima. Le query nel riquadro di Test nel portale di QnA Maker non influiscono sull'apprendimento attivo.

1. Per attivare attivo nel portale di QnA Maker di formazione su, passare all'angolo superiore destro, seleziona i **Name**, passare a [ **le impostazioni del servizio**](https://www.qnamaker.ai/UserSettings).  

    ![Attivare le alternative suggerite domanda dell'apprendimento attivo dalla pagina Impostazioni servizio. Selezionare il nome utente nel menu superiore destro, quindi selezionare le impostazioni del servizio.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Trovare il servizio QnA Maker, quindi attivare **Active Learning** (Apprendimento attivo). 

    [![Nella pagina Impostazioni servizio, Attiva/Disattiva funzionalità di apprendimento attivo. Se non si è in grado di attivare o disattivare la funzionalità, si potrebbe essere necessario aggiornare il servizio.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Una volta **apprendimento attivo** è abilitata, la knowledge base suggerisce nuove domande a intervalli regolari in base alle domande poste dall'utente. È possibile disabilitare l'**apprendimento attivo** selezionando nuovamente l'impostazione.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Accettare un suggerimento di apprendimento attivo nella knowledge base

1. Per vedere le domande suggerite, nel **Edit** pagina della knowledge base, selezionare **opzioni di visualizzazione**, quindi selezionare **Visualizza i suggerimenti di apprendimento attivo**. 

    [![Nella sezione di modifica del portale, selezionare Mostra suggerimenti per poter vedere nuove alternative di domanda dell'apprendimento attivo.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrare la knowledge base con le coppie di domande e risposte per mostrare solo i suggerimenti selezionando **Filtra in base a suggerimenti**.

    [![Per visualizzare solo le alternative consigliate domanda dell'apprendimento attivo, usare il filtro per i suggerimenti di attivazione/disattivazione.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Ogni coppia di domande e risposte suggerisce le alternative di domanda nuovo con un segno di spunta `✔` , per accettare la domanda o un `x` per rifiutare i suggerimenti. Selezionare il segno di spunta per aggiungere la domanda. 

    [![Selezionare o rifiutare le alternative suggerite domanda dell'apprendimento attivo, selezionare il segno di spunta verde o un contrassegno di eliminazione rossa.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    È possibile aggiungere o eliminare _tutti i suggerimenti_ selezionando **aggiungere tutti** oppure **Rifiuta tutto** sulla barra degli strumenti contestuale.

1. Selezionare **Save and Train** (Salva ed esegui il training) per salvare le modifiche alla knowledge base.

1. Selezionare **Publish** per consentire le modifiche siano disponibili dalle [API GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Quando le query di 5 o più simili sono in cluster, ogni 30 minuti, QnA Maker suggerisce le domande alternative è possibile accettare o rifiutare.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Flusso dell'architettura per l'uso GenerateAnswer e le API di eseguire il training di un bot

Un bot o un'altra applicazione client deve usare il flusso dell'architettura seguente per usare l'apprendimento attivo:

* BOT [Ottiene la risposta dalla knowledge base](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) con l'API GenerateAnswer, usando il `top` proprietà da ottenere un numero di risposte.
* BOT determina esplicita commenti e suggerimenti:
    * Usare il proprio [logica di business personalizzata](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), filtrare i punteggi bassi.
    * Il bot o dell'applicazione client, visualizzare l'elenco di possibili risposte per l'utente e ottenere risposta selezionata dell'utente.
* BOT [Invia risposta selezionata al QnA Maker](#bot-framework-sample-code) con il [Train API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Usare la proprietà alto nella richiesta GenerateAnswer per ottenere risposte corrispondente diversi

Quando si invia una domanda per QnA Maker di risposta, il `top` proprietà del corpo JSON imposta il numero di risposte da restituire. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Usare la proprietà punteggio insieme alla logica di business per ottenere l'elenco di risposte per mostrare l'utente

Quando l'applicazione client (ad esempio un chat bot) riceve la risposta, vengono restituite le prime 3 domande. Usare il `score` proprietà per analizzare la prossimità tra i punteggi. Questo intervallo di prossimità è determinato dalla propria logica di business. 

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

L'applicazione client Visualizza le domande con un'opzione per l'utente selezioni _la stessa domanda_ che la maggior parte rappresenta tale intenzione. 

Una volta che l'utente seleziona una delle domande esistente, l'applicazione client invia la scelta dell'utente come commenti e suggerimenti tramite API di eseguire il training di QnA Maker. Questi commenti e suggerimenti completa attivo il ciclo di commenti e suggerimenti di apprendimento. 

## <a name="train-api"></a>API di training

Commenti e suggerimenti apprendimento attivo viene inviato per QnA Maker con la richiesta POST API Train. La firma dell'API è:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Proprietà della richiesta HTTP|Name|Type|Scopo|
|--|--|--|--|
|Parametro di route di URL|ID Knowledge base|string|L'identificatore univoco globale della Knowledge Base.|
|Host sottodominio|Nome della risorsa QnAMaker|string|Il nome host per il QnA Maker nella sottoscrizione di Azure. È disponibile nella pagina impostazioni dopo la pubblicazione della knowledge base. |
|Intestazione|Content-Type|string|tipo di supporto del corpo inviato all'API. Valore predefinito è: `application/json`|
|Intestazione|Authorization|string|la chiave endpoint (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo della richiesta POST|Oggetto JSON|JSON|I commenti e suggerimenti di training|

Il corpo JSON presenta diverse impostazioni:

|Proprietà del corpo JSON|Type|Scopo|
|--|--|--|--|
|`feedbackRecords`|array|Elenco di commenti e suggerimenti.|
|`userId`|string|L'ID utente della persona accettando le domande suggerite. Il formato dell'ID utente è responsabilità dell'utente. Ad esempio, un indirizzo di posta elettronica può essere un ID utente valido nell'architettura. facoltativo.|
|`userQuestion`|string|Testo esatto della domanda. Richiesto.|
|`qnaID`|number|ID della domanda, disponibili nel [GenerateAnswer risposta](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Un esempio del corpo JSON sarà simile a:

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

Una risposta con esito positivo restituisce uno stato 204 e alcun corpo della risposta JSON. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Codice di esempio di Bot framework

Il bot framework codice deve chiamare l'API di training, se la query di un utente deve essere utilizzata per l'apprendimento attivo. Sono presenti due porzioni di codice da scrivere:

* Determinare se query deve essere utilizzata per l'apprendimento attivo
* Inviare query all'API di QnA Maker training per apprendimento attivo

Nel [esempio di Azure Bot](https://aka.ms/activelearningsamplebot), entrambe queste attività programmati. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Esempio di C# codice per l'API di eseguire il training con Bot Framework 4.x

Il codice seguente illustra come inviare informazioni al QnA Maker con l'API di training. Ciò [esempio di codice completo](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) è disponibile in GitHub.

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
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Esempio di codice Node. js per API Training con Bot Framework 4.x 

Il codice seguente illustra come inviare informazioni al QnA Maker con l'API di training. Ciò [esempio di codice completo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) è disponibile in GitHub.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Apprendimento attivo viene salvata nella knowledge base esportata

Quando l'app ha apprendimento attivo abilitato e si esporta l'app, il `SuggestedQuestions` della colonna nel file tsv mantiene i dati di apprendimento attivo. 

Il `SuggestedQuestions` colonna è un oggetto JSON delle informazioni di impliciti `autosuggested`ed esplicite, `usersuggested` commenti e suggerimenti. Un esempio di questo oggetto JSON per una singola domanda inviato dall'utente di `help` è:

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

Quando si reimporta questa app, l'apprendimento attivo continua a raccogliere informazioni e consiglia di suggerimenti per la tua knowledge base. 

## <a name="best-practices"></a>Procedure consigliate

Per le procedure consigliate quando si usa l'apprendimento attivo, vedere [Procedure consigliate](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Passaggi successivi
 
> [!div class="nextstepaction"]
> [Usare i metadati con l'API GenerateAnswer](metadata-generateanswer-usage.md)
