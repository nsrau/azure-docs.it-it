---
title: 'Esercitazione: Bot con Language Understanding in Node.js v4'
description: In questa esercitazione verrà creato un chatbot integrato con LUIS (Language Understanding) tramite Node.js. Questo bot chat utilizza l'app Risorse umane per implementare rapidamente una soluzione di bot. Il bot viene compilato con la versione Bot Framework 4 e il bot per app Web di Azure.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: d6ef0b0d437674d78064e6c5c5a91e1b2b89074a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324672"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Esercitazione: Usare un bot app Web abilitato con Language Understanding in Node. js

Usare Node.js per creare un chatbot integrato con Language Understanding (LUIS). Il bot viene creato con la risorsa [bot app Web](https://docs.microsoft.com/azure/bot-service/) di Azure e con [Bot Framework versione 4](https://github.com/Microsoft/botbuilder-dotnet).

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Creare un bot app Web. Questo processo permette di creare una nuova app LUIS.
> * Scaricare il progetto del bot creato dal servizio per i bot Web
> * Avviare bot ed emulatore localmente nel computer in uso
> * Visualizzare i risultati di espressione nel bot

## <a name="prerequisites"></a>Prerequisiti

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

## <a name="create-a-web-app-bot-resource"></a>Creare una risorsa bot app Web

1. Nel [portale di Azure](https://portal.azure.com), selezionare **Crea nuova risorsa**.

1. Nella casella di ricerca cercare e selezionare **Web App Bot** (Bot app Web). Selezionare **Crea**.

1. In **Bot Service** (Servizio bot) fornire le informazioni necessarie:

    |Impostazione|Scopo|Impostazione consigliata|
    |--|--|--|
    |Handle di bot|Nome risorsa|`luis-nodejs-bot-` + `<your-name>`, ad esempio, `luis-nodejs-bot-johnsmith`|
    |Subscription|Sottoscrizione in cui creare bot.|Chiave di sottoscrizione primaria.
    |Resource group|Gruppo logico di risorse di Azure|Creare un nuovo gruppo per archiviare tutte le risorse usate con questo bot, denominare il gruppo `luis-nodejs-bot-resource-group`.|
    |Location|Area di Azure - non deve essere la stessa della creazione o dell'area di pubblicazione di LUIS.|`westus`|
    |Piano tariffario|Utilizzato per la fatturazione e i limiti delle richieste di servizio.|`F0` è il livello gratuito.
    |Nome app|Il nome viene usato come sottodominio durante la distribuzione del bot nel cloud, (ad esempio humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, ad esempio, `luis-nodejs-bot-johnsmith`|
    |Modello del bot|Impostazioni di Bot framework - vedere la tabella seguente|
    |Percorso app LUIS|Deve essere lo stesso dell'area risorse di LUIS|`westus`|
    |Piano di servizio app/Località|Non modificare il valore predefinito.|
    |Application Insights|Non modificare il valore predefinito.|
    |ID e password dell'app Microsoft|Non modificare il valore predefinito.|

1. In **Bot template** (Modello di bot) selezionare le opzioni seguenti e quindi scegliere il pulsante **Select** (Seleziona) sotto queste impostazioni:

    |Impostazione|Scopo|Selezione|
    |--|--|--|
    |Linguaggio SDK|Linguaggio di programmazione di bot|**Node.js**|
    |Bot|Tipo di bot|**Bot di base**|

1. Selezionare **Crea**. Il servizio bot viene creato e distribuito in Azure. Parte di questo processo permette di creare una nuova app denominata LUIS`luis-nodejs-bot-XXXX`. Questo nome è basato sul nome dell'app del servizio Azure Bot.

    > [!div class="mx-imgBorder"]
    > [![Creare un bot app Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Attendere il completamento della creazione del servizio bot prima di continuare.

1. Selezionare `Go to resource` nella notifica per passare alla pagina del bot app Web.

## <a name="the-bot-has-a-language-understanding-model"></a>Bot con modello di Language Understanding

Il processo di creazione del servizio bot crea anche una nuova app LUIS con finalità ed espressioni di esempio. Il bot fornisce il mapping delle finalità alla nuova app LUIS per le seguenti finalità:

|Finalità LUIS di bot di base|espressione di esempio|
|--|--|
|Prenotazione volo|`Travel to Paris`|
|Annulla|`bye`|
|GetWeather|`what's the weather like?`|
|nessuno|Qualsiasi elemento all'esterno del dominio dell'app.|

## <a name="test-the-bot-in-web-chat"></a>Testare il bot nella chat Web

1. Sempre nel portale di Azure per il nuovo bot, selezionare **Test in Web Chat** (Testa nella chat Web).
1. Nella casella di testo **Type your message** (Digita il tuo messaggio) immettere il testo `Book a flight from Seattle to Berlin tomorrow`. Il bot risponde chiedendo conferma se si vuole prenotare un volo.

    ![Screenshot del portale di Azure, immettere il testo "hello".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    È possibile usare la funzionalità di test per testare rapidamente il bot. Per un test completo, incluso il debug, scaricare il codice del bot e usare Visual Studio Code.

## <a name="download-the-web-app-bot-source-code"></a>Scaricare il codice sorgente del bot app Web
Per sviluppare il codice di bot app Web, scaricare il codice da usare sul computer locale.

1. Nel portale di Azure, selezionare **Build** (Compila) dalla sezione **Bot Management** (Gestione bot).

1. Selezionare **Scarica il codice sorgente del bot**.

    [![Scaricare il codice sorgente del bot app Web per il bot di base](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando la finestra di dialogo popup visualizza il messaggio **Include app settings in the downloaded zip file?** (Includere le impostazioni dell'app nel file ZIP scaricato?) selezionare **Yes** (Sì). In questo modo vengono fornite le impostazioni LUIS.

1. Quando il codice sorgente è compresso, un messaggio fornirà un collegamento per scaricare il codice. Selezionare il collegamento.

1. Salvare il file zip nel computer locale ed estrarre i file. Aprire la cartella del progetto con Visual Studio Code.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Esaminare il codice per inviare l'espressione a LUIS e ottenere la risposta

1. Per inviare l'espressione utente all'endpoint di stima LUIS, aprire il file **dialogs -> flightBookingRecognizer.js**. Qui è dove viene inviata a LUIS l'espressione utente inserita nel bot. La risposta di LUIS viene restituita dal metodo **executeLuisQuery**.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                // Set the recognizer options depending on which endpoint version you want to use e.g v2 or v3.
                // More details can be found in https://docs.microsoft.com/en-gb/azure/cognitive-services/luis/luis-migration-api-v3
                const recognizerOptions = {
                    apiVersion: 'v3'
                };

                this.recognizer = new LuisRecognizer(config, recognizerOptions);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ```

1. **dialogs -> mainDialog** acquisisce l'espressione e la invia a executeLuisQuery nel metodo actStep.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { MessageFactory, InputHints } = require('botbuilder');
    const { LuisRecognizer } = require('botbuilder-ai');
    const { ComponentDialog, DialogSet, DialogTurnStatus, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

    const MAIN_WATERFALL_DIALOG = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor(luisRecognizer, bookingDialog) {
            super('MainDialog');

            if (!luisRecognizer) throw new Error('[MainDialog]: Missing parameter \'luisRecognizer\' is required');
            this.luisRecognizer = luisRecognizer;

            if (!bookingDialog) throw new Error('[MainDialog]: Missing parameter \'bookingDialog\' is required');

            // Define the main dialog and its related components.
            // This is a sample "book a flight" dialog.
            this.addDialog(new TextPrompt('TextPrompt'))
                .addDialog(bookingDialog)
                .addDialog(new WaterfallDialog(MAIN_WATERFALL_DIALOG, [
                    this.introStep.bind(this),
                    this.actStep.bind(this),
                    this.finalStep.bind(this)
                ]));

            this.initialDialogId = MAIN_WATERFALL_DIALOG;
        }

        /**
         * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
         * If no dialog is active, it will start the default dialog.
         * @param {*} turnContext
         * @param {*} accessor
         */
        async run(turnContext, accessor) {
            const dialogSet = new DialogSet(accessor);
            dialogSet.add(this);

            const dialogContext = await dialogSet.createContext(turnContext);
            const results = await dialogContext.continueDialog();
            if (results.status === DialogTurnStatus.empty) {
                await dialogContext.beginDialog(this.id);
            }
        }

        /**
         * First step in the waterfall dialog. Prompts the user for a command.
         * Currently, this expects a booking request, like "book me a flight from Paris to Berlin on march 22"
         * Note that the sample LUIS model will only recognize Paris, Berlin, New York and London as airport cities.
         */
        async introStep(stepContext) {
            if (!this.luisRecognizer.isConfigured) {
                const messageText = 'NOTE: LUIS is not configured. To enable all capabilities, add `LuisAppId`, `LuisAPIKey` and `LuisAPIHostName` to the .env file.';
                await stepContext.context.sendActivity(messageText, null, InputHints.IgnoringInput);
                return await stepContext.next();
            }

            const messageText = stepContext.options.restartMsg ? stepContext.options.restartMsg : 'What can I help you with today?\nSay something like "Book a flight from Paris to Berlin on March 22, 2020"';
            const promptMessage = MessageFactory.text(messageText, messageText, InputHints.ExpectingInput);
            return await stepContext.prompt('TextPrompt', { prompt: promptMessage });
        }

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {
            const bookingDetails = {};

            if (!this.luisRecognizer.isConfigured) {
                // LUIS is not configured, we just run the BookingDialog path.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt)
            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);
            switch (LuisRecognizer.topIntent(luisResult)) {
            case 'BookFlight': {
                // Extract the values for the composite entities from the LUIS result.
                const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                const toEntities = this.luisRecognizer.getToEntities(luisResult);

                // Show a warning for Origin and Destination if we can't resolve them.
                await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);

                // Initialize BookingDetails with any entities we may have found in the response.
                bookingDetails.destination = toEntities.airport;
                bookingDetails.origin = fromEntities.airport;
                bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));

                // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            case 'GetWeather': {
                // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                const getWeatherMessageText = 'TODO: get weather flow here';
                await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                break;
            }

            default: {
                // Catch all for unhandled intents
                const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
            }
            }

            return await stepContext.next();
        }

        /**
         * Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
         * In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
         * will be empty if those entity values can't be mapped to a canonical item in the Airport.
         */
        async showWarningForUnsupportedCities(context, fromEntities, toEntities) {
            const unsupportedCities = [];
            if (fromEntities.from && !fromEntities.airport) {
                unsupportedCities.push(fromEntities.from);
            }

            if (toEntities.to && !toEntities.airport) {
                unsupportedCities.push(toEntities.to);
            }

            if (unsupportedCities.length) {
                const messageText = `Sorry but the following airports are not supported: ${ unsupportedCities.join(', ') }`;
                await context.sendActivity(messageText, messageText, InputHints.IgnoringInput);
            }
        }

        /**
         * This is the final step in the main waterfall dialog.
         * It wraps up the sample "book a flight" interaction with a simple confirmation.
         */
        async finalStep(stepContext) {
            // If the child dialog ("bookingDialog") was cancelled or the user failed to confirm, the Result here will be null.
            if (stepContext.result) {
                const result = stepContext.result;
                // Now we have all the booking details.

                // This is where calls to the booking AOU service or database would go.

                // If the call to the booking service was successful tell the user.
                const timeProperty = new TimexProperty(result.travelDate);
                const travelDateMsg = timeProperty.toNaturalLanguage(new Date(Date.now()));
                const msg = `I have you booked to ${ result.destination } from ${ result.origin } on ${ travelDateMsg }.`;
                await stepContext.context.sendActivity(msg, msg, InputHints.IgnoringInput);
            }

            // Restart the main dialog with a different message the second time around
            return await stepContext.replaceDialog(this.initialDialogId, { restartMsg: 'What else can I do for you?' });
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="start-the-bot-code"></a>Avviare il codice del bot

1. Aprire una console di Windows o un terminale di Linux o macOS.

1. Passare alla directory con il codice del bot e immettere il comando seguente per installare la dipendenza dotenv:

    ```console
    npm install dotenv --save
    ```

1. Immettere il comando seguente per avviare il bot:

    ```console
    node index.js
    ```

Verrà visualizzata una finestra del browser con il sito Web dell'app Web del bot all'indirizzo `http://localhost:3978/`. Nella home page vengono visualizzate informazioni relative al bot.

![Nella home page vengono visualizzate informazioni relative al bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usare l'emulatore per testare il bot

Porre una domanda al bot per la finalità della prenotazione del volo.

1. Avviare l'emulatore del bot e selezionare **Open Bot** (Apri bot).
1. Nella finestra di dialogo popup **Open a bot** (Apri un bot) immettere l'URL del bot, ad esempio `http://localhost:3978/api/messages`. La route `/api/messages` corrisponde all'indirizzo Web del bot.
1. Immettere l'**ID dell'app Microsoft** e la **password dell'app Microsoft**, che si trovano nel file con estensione **env** nella radice del codice del bot scaricato.

1. Nell'emulatore del bot immettere `Book a flight from Seattle to Berlin tomorrow` e ottenere la stessa risposta per il bot di base ricevuto in **Test in Web Chat** (Testa nella chat Web).

    [![Risposta del bot di base nell'emulatore](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selezionare **Sì**. Il bot risponde con un riepilogo delle sue azioni.
1. Nel log dell'emulatore del bot selezionare la riga che include `<- trace LuisV3 Trace`. Verrà visualizzata la risposta JSON restituita da LUIS relativa alla finalità e alle entità dell'espressione.

    [![Risposta del bot di base nell'emulatore](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passaggi successivi

Vedere altri [esempi](https://github.com/microsoft/botframework-solutions) con i bot di conversazione.

> [!div class="nextstepaction"]
> [Creare un'app di Language Understanding con un dominio soggetto personalizzato](luis-quickstart-intents-only.md)
