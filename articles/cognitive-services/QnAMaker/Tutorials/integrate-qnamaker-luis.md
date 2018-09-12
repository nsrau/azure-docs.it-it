---
title: Integrare QnA Maker e LUIS- Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Un'esercitazione dettagliata sull'integrazione di QnA Maker e LUIS
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 18eae69867dc9774f63b11c762b22df4595bdce6
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781748"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Integrare QnA Maker e LUIS per distribuire la Knowledge Base
Quando le dimensioni della Knowledge Base di QnA Maker aumentano, diventa difficile gestirla come una singola unità monolitica ed è necessario suddividerla in parti logiche più piccole.

Sebbene sia molto semplice creare più Knowledge Base in QnA Maker, sarà necessario definire la logica per indirizzare la domanda in ingresso alla Knowledge Base appropriata. A tale scopo è possibile usare LUIS.

## <a name="architecture"></a>Architettura

![Architettura di QnA Maker per LUIS](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Nello scenario precedente, QnA Maker innanzitutto ottiene la finalità della domanda in ingresso da un modello LUIS e quindi la usa per instradare la domanda alla Knowledge Base corretta di QnA Maker.

## <a name="prerequisites"></a>Prerequisiti
- Accedere al portale [LUIS](https://www.luis.ai/) e [creare un'app](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app).
- [Aggiungere le finalità](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents) in base allo specifico scenario.
- [Eseguire il training](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train) e [pubblicare](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp) l'app LUIS.
- Accedere a [QnA Maker](https://qnamaker.ai) e [creare le Knowledge Base](https://www.qnamaker.ai/Create) in base allo specifico scenario.
- Testare e pubblicare le Knowledge Base.

## <a name="qna-maker--luis-bot"></a>Bot QnA Maker e LUIS
1. Creare innanzitutto un bot app Web con il modello LUIS, collegarlo con l'app LUIS creata in precedenza e modificare le finalità. Vedere la procedura dettagliata [qui](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Aggiungere dipendenze nella parte superiore del file, con le altre dipendenze:

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Aggiungere la classe seguente per la chiamata al servizio QnA Maker:

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
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
        /* END - QnA Maker Response Class */
    ```

3. Passare a https://qnamaker.ai -> My knowledge bases (Knowledge Base personali) -> View code (Visualizza codice) per la Knowledge Base corrispondente. Ottenere le informazioni seguenti:

    ![Richiesta HTTP QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. Creare un'istanza della classe QnAMakerService per ognuna delle Knowledge Base:
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. Chiamare la Knowledge Base corrispondente per la finalità.
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
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
