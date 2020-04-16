---
title: LUIS e QnAMaker - integrazione bot
titleSuffix: Azure Cognitive Services
description: Man mano che la tua base di conoscenze QnA Maker cresce, diventa difficile mantenerla come un singolo set monolitico. Suddividere la Knowledge Base in blocchi logici più piccoli.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402710"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Usare un bot con QnA Maker e LUIS per distribuire la Knowledge Base
Man mano che la tua base di conoscenze QnA Maker cresce, diventa difficile mantenerla come un singolo set monolitico. Suddividere la Knowledge Base in blocchi logici più piccoli.

Anche se è semplice creare più knowledge base in QnA Maker, sarà necessaria una logica per instradare la domanda in ingresso alla Knowledge Base appropriata. A tale scopo è possibile usare LUIS.

In questo articolo viene utilizzato bot Framework v3 SDK. Se si desidera la versione SDK di Bot Framework v4 di queste informazioni, vedere [Utilizzare più modelli LUIS e QnA](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp).

## <a name="architecture"></a>Architecture

![Immagine dell'architettura di QnA Maker con La comprensione del linguaggio](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Il grafico precedente mostra che QnA Maker ottiene prima l'intento della domanda in arrivo da un modello LUIS. Quindi, QnA Maker usa tale intento per indirizzare la domanda alla knowledge base QnA Maker corretta.

## <a name="create-a-luis-app"></a>Creare un'app LUIS

1. Accedere al portale [LUIS.](https://www.luis.ai/)
1. [Creare un'app](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Aggiungere una finalità](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) per ogni knowledge base di QnA Maker. Le espressioni di esempio devono corrispondere a domande delle knowledge base di QnA Maker.
1. [Eseguire il training dell'app LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) e [pubblicare l'app LUIS.](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)
1. Nella sezione **Gestisci** prendere nota dell'ID app LUIS, della chiave dell'endpoint LUIS e del nome di [dominio personalizzato.](../../cognitive-services-custom-subdomains.md) Questi valori saranno necessari più avanti.

## <a name="create-qna-maker-knowledge-bases"></a>Creare knowledge base di QnA Maker

1. Accedere a [QnA Maker](https://qnamaker.ai).
1. [Creare](https://www.qnamaker.ai/Create) una Knowledge Base per ogni finalità nell'app LUIS.
1. Testare e pubblicare le Knowledge Base. Quando si pubblica ognuno di essi, prendere nota dell'ID, del nome della risorsa (il sottodominio personalizzato prima _di .azurewebsites.net/qnamaker_) e della chiave dell'endpoint di autorizzazione. Questi valori saranno necessari più avanti.

    In questo articolo si presuppone che le basi della conoscenza siano tutte create nella stessa sottoscrizione di Azure QnA Maker.This article assumes the knowledge bases are all created in the same Azure QnA Maker subscription.

    ![Screenshot della richiesta HTTP di QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Bot app Web

1. [Creare un bot "Basic" con il servizio Bot](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0)di Azure , che include automaticamente un'app LUIS. Selezionare il linguaggio di programmazione C.

1. Dopo aver creato il bot dell'app Web, nel portale di Azure selezionare il bot dell'app Web.After you create the web app bot, in the Azure portal, select the web app bot.
1. Nella struttura di spostamento del servizio bot dell'app Web selezionare **Impostazioni applicazione**. Quindi scorri verso il basso fino alla sezione **Impostazioni applicazione** delle impostazioni disponibili.
1. Modificare **LuisAppId** per il valore dell'app LUIS creata nella sezione precedente. Quindi selezionare **Salva**.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>Modificare il codice nel file di BasicLuisDialog.cs
1. Nella sezione **Bot Management** (Gestione bot) del menu di spostamento del bot per app Web nel portale di Azure selezionare **Compila**.
2. Selezionare **Open online code editor** (Apri editor di codice online). Viene aperta una nuova scheda del browser con l'ambiente di modifica online.
3. Nella sezione **WWWROOT** selezionare la directory **Dialogs,** quindi aprire **BasicLuisDialog.cs**.
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

5. Aggiungere le classi seguenti per deserializzare la risposta QnA Maker:Add the following classes to deserialize the QnA Maker response:

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


6. Aggiungere la classe seguente per eseguire una richiesta HTTP al servizio QnA Maker. Si noti che il valore dell'intestazione **Authorization** include la parola , `EndpointKey`, con uno spazio dopo la parola. Il risultato JSON viene deserializzato nelle classi precedenti e viene restituita la prima risposta.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
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
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
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


7. Modificare `BasicLuisDialog` la classe. Ogni finalità LUIS deve avere un metodo con un effetto dato da **LuisIntent**. Il parametro per l'effetto è il nome della finalità LUIS. Il nome del metodo decorato _deve_ essere il nome della finalità LUIS per leggibilità e manutenibilità, ma non deve essere lo stesso in fase di progettazione o di esecuzione.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

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
1. Nell'editor di codice fare clic con il pulsante destro del mouse su **build.cmd**e **scegliere Esegui dalla console**.

    ![Screenshot dell'opzione Esegui dalla console nell'editor di codice](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. La visualizzazione del codice viene sostituita con una finestra del terminale che mostra lo stato di avanzamento e i risultati della compilazione.

    ![Screenshot della build della console](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Testare il bot
Nel portale di Azure selezionare **Test in Web Chat** (Testa nella chat Web) per testare il bot. Digitare i messaggi da diverse finalità per ottenere la risposta dalla Knowledge Base corrispondente.

![Screenshot del test della chat Web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Integra la tua knowledge base con un agente in Power Virtual Agents](integrate-with-power-virtual-assistant-fallback-topic.md)
