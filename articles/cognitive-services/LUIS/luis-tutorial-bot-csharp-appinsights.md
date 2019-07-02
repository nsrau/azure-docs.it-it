---
title: Application Insights, C#
titleSuffix: Azure Cognitive Services
description: Questa esercitazione aggiunge le informazioni relative a bot e Language Understanding all'archivio dei dati di telemetria di Application Insights.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: fa7147dd1b5f22ead17a60042c1c35c4b770cd18
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154912"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>Aggiungere risultati LUIS ad Application Insights da un bot in C#

Questa esercitazione aggiunge le informazioni relative a bot e Language Understanding all'archivio dei dati di telemetria di [Application Insights](https://azure.microsoft.com/services/application-insights/). Dopo aver acquisito i dati, è possibile sottoporli a query con il linguaggio Kusto o con Power BI per analizzare, aggregare e registrare le finalità e le entità delle espressioni in tempo reale. Questa analisi consente di determinare se è necessario aggiungere o modificare le finalità e le entità dell'app LUIS.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Acquisire dati di bot e Language Understanding in Application Insights
> * Eseguire query su Application Insights per trovare dati di Language Understanding

## <a name="prerequisites"></a>Prerequisiti

* Un bot del servizio Azure Bot, creato con Application Insights abilitato.
* Codice del bot scaricato dalla precedente **[esercitazione](luis-csharp-tutorial-bf-v4.md)** . 
* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Tutto il codice di questa esercitazione è disponibile nel [repository GitHub Azure-Samples Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Aggiungere Application Insights a un progetto di bot app Web

Attualmente, il servizio Application Insights usato in questo bot app Web raccoglie dati generali di telemetria relativa allo stato per il bot. Non raccoglie informazioni relative a LUIS. 

Per acquisire queste informazioni, è necessario che per il bot app Web sia installato e configurato il pacchetto NuGet **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** .  

1. In Visual Studio aggiungere la dipendenza alla soluzione. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Gestisci pacchetti NuGet**. Gestione pacchetti NuGet mostra un elenco dei pacchetti installati. 
1. Selezionare **Sfoglia** e quindi cercare **Microsoft.ApplicationInsights**.
1. Installare il pacchetto. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Acquisire i risultati di query LUIS e inviarli ad Application Insights

1. Aprire il file `LuisHelper.cs` e sostituirne il contenuto con il codice seguente. Il metodo **LogToApplicationInsights** acquisisce i dati di bot e LUIS e li invia ad Application Insights come evento di traccia denominato `LUIS`.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Aggiungere la chiave di strumentazione di Application Insights 

Per aggiungere dati in Application Insights, è necessaria la chiave di strumentazione.

1. In un browser, nel [portale di Azure](https://portal.azure.com), trovare la risorsa **Application Insights** del bot. Il nome della risorsa include la maggior parte del nome del bot e termina con caratteri casuali, ad esempio `luis-csharp-bot-johnsmithxqowom`. 
1. Nella pagina **Panoramica** della risorsa di Application Insights copiare il valore di **Chiave di strumentazione**.
1. In Visual Studio aprire il file **appsettings.json** alla radice del progetto di bot. Il file contiene tutte le variabili di ambiente.
1. Aggiungere una nuova variabile, `BotDevAppInsightsKey`, con il valore della chiave di strumentazione. Il valore deve essere racchiuso tra virgolette. 

## <a name="build-and-start-the-bot"></a>Creare e avviare il bot

1. In Visual Studio creare ed eseguire il bot. 
1. Avviare l'emulatore di bot e aprire il bot. Questo [passaggio](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) viene descritto nell'esercitazione precedente.

1. Porre una domanda al bot. Questo [passaggio](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) viene descritto nell'esercitazione precedente.

## <a name="view-luis-entries-in-application-insights"></a>Visualizzare le voci LUIS in Application Insights

Aprire Application Insights per visualizzare le voci LUIS. Per visualizzare i dati in Application Insights possono essere necessari alcuni minuti.

1. Nel [portale di Azure](https://portal.azure.com) aprire la risorsa di Application Insights del bot. 
1. Quando la risorsa si apre, selezionare **Cerca** e cercare tutti i dati degli ultimi **30 minuti** con il tipo di evento **Traccia**. Selezionare la traccia denominata **LUIS**. 
1. Le informazioni relative a bot e LUIS sono disponibili in **Proprietà personalizzate**. 

    ![Esaminare le proprietà personalizzate di LUIS archiviate in Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Eseguire query su Application Insights per finalità, punteggio ed espressione
Application Insights consente di eseguire query sui dati con il linguaggio [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics), oltre che di esportarli in [Power BI](https://powerbi.microsoft.com). 

1. Selezionare **Log Analytics**. Verrà aperta una nuova finestra con una finestra di query nella parte superiore e una finestra di tabella di dati al di sotto. Per chi ha già usato database in precedenza, questa disposizione risulta familiare. La query rappresenta i dati filtrati in precedenza. La colonna **CustomDimensions** include le informazioni relative a bot e LUIS.
1. Per estrarre finalità principale, punteggio ed espressione, aggiungere quanto segue sopra l'ultima riga (`|top...`) nella finestra di query:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Eseguire la query. Saranno disponibili le nuove colonne relative a finalità principale, punteggio ed espressione. Selezionare la colonna topIntent da ordinare.

Leggere altre informazioni sul [linguaggio di query Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) o su come [esportare i dati in Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Ulteriori informazioni su Bot Framework

Ulteriori informazioni su [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Passaggi successivi

Tra le altre informazioni che è possibile aggiungere ai dati di Application Insights ci sono ID app, ID versione, data dell'ultima modifica del modello, data dell'ultimo training, data dell'ultima pubblicazione. Questi valori possono essere recuperati dall'URL dell'endpoint (ID app e ID versione) o da una chiamata a un'API di creazione e quindi configurati e recuperati dalle impostazioni del bot app Web.  

Se si sta usando la stessa sottoscrizione di endpoint per più app LUIS, è anche necessario includere l'ID sottoscrizione e una proprietà che indica che si tratta di una chiave condivisa.

> [!div class="nextstepaction"]
> [Altre informazioni sulle espressioni di esempio](luis-how-to-add-example-utterances.md)
