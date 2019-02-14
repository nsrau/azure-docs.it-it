---
title: LUIS e QnAMaker - integrazione di Bot
titleSuffix: Azure Cognitive Services
description: Quando le dimensioni della Knowledge Base di QnA Maker aumentano, diventa difficile gestirla come una singola unità monolitica ed è necessario suddividerla in parti logiche più piccole.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: fa79f519c8f3eb8baeaab04870f22a1cfefa59ab
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884325"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Usare bot con QnA Maker e LUIS per distribuire la knowledge base
Quando le dimensioni della Knowledge Base di QnA Maker aumentano, diventa difficile gestirla come una singola unità monolitica ed è necessario suddividerla in parti logiche più piccole.

Sebbene sia molto semplice creare più Knowledge Base in QnA Maker, sarà necessario definire la logica per indirizzare la domanda in ingresso alla Knowledge Base appropriata. A tale scopo è possibile usare LUIS.

Questo articolo usa Bot Framework v3 SDK. Vedere questo [articolo su Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp) per informazioni su Bot Framework v4 SDK.

## <a name="architecture"></a>Architettura

![Architettura di QnA Maker per LUIS](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Nello scenario precedente, QnA Maker innanzitutto ottiene la finalità della domanda in ingresso da un modello LUIS e quindi la usa per instradare la domanda alla Knowledge Base corretta di QnA Maker.

## <a name="create-a-luis-app"></a>Creare un'app LUIS

1. Accedere al portale [LUIS](https://www.luis.ai/).
1. [Creare un'app](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Aggiungere una finalità](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) per ogni knowledge base di QnA Maker. Le espressioni di esempio devono corrispondere a domande delle knowledge base di QnA Maker.
1. [Eseguire il training dell'app LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) e [pubblicare l'app LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. Nella sezione **Manage** (Gestisci) prendere nota dell'ID dell'app LUIS, la chiave di endpoint LUIS e l'area host. Questi valori saranno necessari più avanti. 

## <a name="create-qna-maker-knowledge-bases"></a>Creare knowledge base di QnA Maker

1. Accedere a [QnA Maker](https://qnamaker.ai).
1. [Creare](https://www.qnamaker.ai/Create) knowledge base per ogni finalità nell'app LUIS.
1. Testare e pubblicare le Knowledge Base. Quando si pubblica ogni knowledge base, assicurarsi di annotare l'ID della knowledge base, l'host (sottodominio prima di _.azurewebsites.net/qnamaker_) e la chiave dell'endpoint di autorizzazione. Questi valori saranno necessari più avanti. 

    Questo articolo presuppone che tutte le knowledge base siano create nella stessa sottoscrizione QnA Maker di Azure.

    ![Richiesta HTTP QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Bot dell'app Web

1. [Creare un bot per app Web](https://docs.microsoft.com/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample) con il modello LUIS. Selezionare l'SDK 3.x e il linguaggio di programmazione C#.

1. Dopo aver creato il bot per app Web, selezionarlo nel portale di Azure.
1. Selezionare **Impostazione applicazione** nel menu di spostamento del servizio bot per app Web, quindi scorrere verso il basso fino alla sezione **Impostazioni applicazione** per accedere alle impostazioni disponibili.
1. Cambiare **LuisAppId** nel valore dell'app LUIS creata nella sezione precedente e quindi selezionare **Salva**.


## <a name="change-code-in-basicluisdialogcs"></a>Cambiare il codice in BasicLuisDialog.cs
1. Nella sezione **Bot Management** (Gestione bot) del menu di spostamento del bot per app Web nel portale di Azure selezionare **Compila**.
2. Selezionare **Open online code editor** (Apri editor di codice online). Viene aperta una nuova scheda del browser con l'ambiente di modifica online. 
3. Nella sezione **WWWROOT** selezionare la directory **Dialogs**, quindi aprire **BasicLuisDialog.cs**.
4. Aggiungere le dipendenze all'inizio del file **BasicLuisDialog.cs**:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Aggiungere le classi seguenti per deserializzare la risposta di QnA Maker:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Aggiungere la classe seguente per eseguire una richiesta HTTP al servizio QnA Maker. Si noti che il valore dell'intestazione **Authorization** include la parola `EndpointKey` con uno spazio dopo la parola. Il risultato JSON viene deserializzato nelle classi elencate sopra e viene restituita la prima risposta.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceHostName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string hostName, string kbId, string endpointkey)
        {
            qnaServiceHostName = hostName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Modificare la classe BasicLuisDialog. Ogni finalità LUIS deve avere un metodo con un effetto dato da **LuisIntent**. Il parametro per l'effetto è il nome della finalità LUIS. Il nome del metodo che presenta l'effetto _dovrebbe_ corrispondere al nome della finalità LUIS per migliorare la leggibilità e la facilità di gestione, ma non deve necessariamente essere lo stesso in fase di progettazione o di esecuzione.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_endpointDomain = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_endpointDomain + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Compilare il bot
1. Nell'editor di codice fare clic con il pulsante destro del mouse su `build.cmd` e selezionare **Run from Console** (Esegui dalla console).

    ![Esecuzione dalla console](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. La visualizzazione del codice viene sostituita con una finestra del terminale che mostra lo stato di avanzamento e i risultati della compilazione.

    ![Compilazione dalla console](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testare il bot
Nel portale di Azure selezionare **Test in Web Chat** (Testa nella chat Web) per testare il bot. Digitare i messaggi da diverse finalità per ottenere la risposta dalla Knowledge Base corrispondente.

![Test nella chat Web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un piano di continuità aziendale per QnA Maker](../How-To/business-continuity-plan.md)
