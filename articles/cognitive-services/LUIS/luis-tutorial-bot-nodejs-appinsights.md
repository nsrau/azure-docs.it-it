---
title: Application Insights, Node.js
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
ms.openlocfilehash: 5459fb5d8304a35b3f009354c446514a2831c513
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155289"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Aggiungere risultati LUIS ad Application Insights da un bot in Node.js
Questa esercitazione aggiunge le informazioni relative a bot e Language Understanding all'archivio dei dati di telemetria di [Application Insights](https://azure.microsoft.com/services/application-insights/). Dopo aver acquisito i dati, è possibile sottoporli a query con il linguaggio Kusto o con Power BI per analizzare, aggregare e registrare le finalità e le entità delle espressioni in tempo reale. Questa analisi consente di determinare se è necessario aggiungere o modificare le finalità e le entità dell'app LUIS.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Acquisire dati di bot e Language Understanding in Application Insights
> * Eseguire query su Application Insights per trovare dati di Language Understanding

## <a name="prerequisites"></a>Prerequisiti

* Un bot del servizio Azure Bot, creato con Application Insights abilitato.
* Codice del bot scaricato dalla precedente **[esercitazione](luis-nodejs-tutorial-bf-v4.md)** . 
* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Tutto il codice di questa esercitazione è disponibile nel [repository GitHub Azure-Samples Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Aggiungere Application Insights a un progetto di bot app Web
Attualmente, il servizio Application Insights usato in questo bot app Web raccoglie dati generali di telemetria relativa allo stato per il bot. Non raccoglie informazioni relative a LUIS. 

Per acquisire queste informazioni, è necessario che per il bot app Web sia installato e configurato il pacchetto NPM **[Microsoft.ApplicationInsights](https://www.npmjs.com/package/applicationinsights)** .  

1. Nel terminale integrato di VSCode, alla radice del progetto di bot, aggiungere i pacchetti NPM seguenti usando il comando illustrato: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    Il pacchetto **underscore** si usa per rendere flat la struttura JSON LUIS in modo da semplificarne la visualizzazione e l'uso in Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Acquisire i risultati di query LUIS e inviarli ad Application Insights

1. In VSCode creare un nuovo file **appInsightsLog.js** e aggiungere il codice seguente:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Questo file rende flat il contesto del bot e la risposta di LUIS e li inserisce in un evento **Traccia** in Application Insights. Il nome dell'evento è **LUIS**. 

1. Aprire la cartella **dialogs** e quindi il file **luisHelper.js**. Includere il nuovo file **appInsightsLog.js** come obbligatorio e acquisire il contesto del bot e la risposta di LUIS. Il codice completo per questo file è: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Aggiungere la chiave di strumentazione di Application Insights 

Per aggiungere dati in Application Insights, è necessaria la chiave di strumentazione.

1. In un browser, nel [portale di Azure](https://portal.azure.com), trovare la risorsa **Application Insights** del bot. Il nome della risorsa include la maggior parte del nome del bot e termina con caratteri casuali, ad esempio `luis-nodejs-bot-johnsmithxqowom`. 
1. Nella pagina **Panoramica** della risorsa di Application Insights copiare il valore di **Chiave di strumentazione**.
1. In VSCode aprire il file con estensione **env** alla radice del progetto di bot. Il file contiene tutte le variabili di ambiente.  
1. Aggiungere una nuova variabile, `MicrosoftApplicationInsightsInstrumentationKey`, con il valore della chiave di strumentazione. Non racchiudere il valore tra virgolette. 

## <a name="start-the-bot"></a>Avviare il bot

1. Nel terminale integrato di VSCode avviare il bot:
    
    ```console
    npm start
    ```

1. Avviare l'emulatore di bot e aprire il bot. Questo [passaggio](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) viene descritto nell'esercitazione precedente.

1. Porre una domanda al bot. Questo [passaggio](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) viene descritto nell'esercitazione precedente.

## <a name="view-luis-entries-in-application-insights"></a>Visualizzare le voci LUIS in Application Insights

Aprire Application Insights per visualizzare le voci LUIS. Per visualizzare i dati in Application Insights possono essere necessari alcuni minuti.

1. Nel [portale di Azure](https://portal.azure.com) aprire la risorsa di Application Insights del bot. 
1. Quando la risorsa si apre, selezionare **Cerca** e cercare tutti i dati degli ultimi **30 minuti** con il tipo di evento **Traccia**. Selezionare la traccia denominata **LUIS**. 
1. Le informazioni relative a bot e LUIS sono disponibili in **Proprietà personalizzate**. 

    ![Esaminare le proprietà personalizzate di LUIS archiviate in Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Eseguire query su Application Insights per finalità, punteggio ed espressione
Application Insights consente di eseguire query sui dati con il linguaggio [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics), oltre che di esportarli in [Power BI](https://powerbi.microsoft.com). 

1. Selezionare **Log Analytics**. Verrà aperta una nuova finestra con una finestra di query nella parte superiore e una finestra di tabella di dati al di sotto. Per chi ha già usato database in precedenza, questa disposizione risulta familiare. La query rappresenta i dati filtrati in precedenza. La colonna **CustomDimensions** include le informazioni relative a bot e LUIS.
1. Per estrarre finalità principale, punteggio ed espressione, aggiungere quanto segue sopra l'ultima riga (`|top...`) nella finestra di query:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Eseguire la query. Saranno disponibili le nuove colonne relative a finalità principale, punteggio ed espressione. Selezionare la colonna topIntent da ordinare.

Leggere altre informazioni sul [linguaggio di query Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) o su come [esportare i dati in Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Passaggi successivi

Tra le altre informazioni che è possibile aggiungere ai dati di Application Insights ci sono ID app, ID versione, data dell'ultima modifica del modello, data dell'ultimo training, data dell'ultima pubblicazione. Questi valori possono essere recuperati dall'URL dell'endpoint (ID app e ID versione) o da una chiamata a un'API di creazione e quindi configurati e recuperati dalle impostazioni del bot app Web.  

Se si sta usando la stessa sottoscrizione di endpoint per più app LUIS, è anche necessario includere l'ID sottoscrizione e una proprietà che indica che si tratta di una chiave condivisa. 

> [!div class="nextstepaction"]
> [Altre informazioni sulle espressioni di esempio](luis-how-to-add-example-utterances.md)
