---
title: 'Esercitazione: Bot con Language Understanding in Node.js v4'
titleSuffix: Azure Cognitive Services
description: Usando Node.js, creare un chat bot integrato con Language Understanding (LUIS). Questo bot chat utilizza l'app Risorse umane per implementare rapidamente una soluzione di bot. Il bot viene compilato con la versione Bot Framework 4 e il bot per app Web di Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 9a38f43b24e5db6a60ff38cd0f1d9b59b9875bba
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492682"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Esercitazione: Usare un bot app Web abilitato con Language Understanding in Node. js 

Usare Node.js per creare un chatbot integrato con Language Understanding (LUIS). Il bot viene creato con la risorsa [bot app Web](https://docs.microsoft.com/azure/bot-service/) di Azure e con [Bot Framework versione 4](https://github.com/Microsoft/botbuilder-dotnet).

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

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

1. Nella casella di ricerca cercare e selezionare **Web App Bot** (Bot app Web). Selezionare **Create** (Crea).

1. In **Bot Service** (Servizio bot) fornire le informazioni necessarie:

    |Impostazione|Scopo|Impostazione consigliata|
    |--|--|--|
    |Nome bot|Nome risorsa|`luis-nodejs-bot-` + `<your-name>`, ad esempio, `luis-nodejs-bot-johnsmith`|
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
    |Versione dell'SDK|Versione di Bot Framework|**SDK v4**|
    |Linguaggio SDK|Linguaggio di programmazione di bot|**Node.js**|
    |Bot|Tipo di bot|**Bot di base**|
    
1. Selezionare **Create** (Crea). Il servizio bot viene creato e distribuito in Azure. Parte di questo processo permette di creare una nuova app denominata LUIS`luis-nodejs-bot-XXXX`. Questo nome è basato sul nome dell'app del servizio Azure Bot.

    [![Creare un bot app Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Attendere il completamento della creazione del servizio bot prima di continuare.

## <a name="the-bot-has-a-language-understanding-model"></a>Bot con modello di Language Understanding

Il processo di creazione del servizio bot crea anche una nuova app LUIS con finalità ed espressioni di esempio. Il bot fornisce il mapping delle finalità alla nuova app LUIS per le seguenti finalità: 

|Finalità LUIS di bot di base|espressione di esempio|
|--|--|
|Prenotazione volo|`Travel to Paris`|
|Annulla|`bye`|
|GetWeather|`what's the weather like?`|
|Nessuna|Qualsiasi elemento all'esterno del dominio dell'app.|

## <a name="test-the-bot-in-web-chat"></a>Testare il bot nella chat Web

1. Sempre nel portale di Azure per il nuovo bot, selezionare **Test in Web Chat** (Testa nella chat Web). 
1. Nella casella di testo **Type your message** (Digita il tuo messaggio) immettere il testo `Book a flight from Seattle to Berlin tomorrow`. Il bot risponde chiedendo conferma se si vuole prenotare un volo. 

    ![Screenshot del portale di Azure, immettere il testo "hello".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    È possibile usare la funzionalità di test per testare rapidamente il bot. Per un test completo, incluso il debug, scaricare il codice del bot e usare Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Scaricare il codice sorgente del bot app Web
Per sviluppare il codice di bot app Web, scaricare il codice da usare sul computer locale. 

1. Nel portale di Azure, selezionare **Build** (Compila) dalla sezione **Bot Management** (Gestione bot). 

1. Selezionare **Scarica il codice sorgente del bot**. 

    [![Scaricare il codice sorgente del bot app Web per il bot di base](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando la finestra di dialogo popup visualizza il messaggio **Include app settings in the downloaded zip file?** (Includere le impostazioni dell'app nel file ZIP scaricato?) selezionare **Yes** (Sì). In questo modo vengono fornite le impostazioni LUIS. 

1. Quando il codice sorgente è compresso, un messaggio fornirà un collegamento per scaricare il codice. Selezionare il collegamento. 

1. Salvare il file zip nel computer locale ed estrarre i file. Aprire il progetto con Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Esaminare il codice per inviare l'espressione a LUIS e ottenere la risposta

1. Per inviare l'espressione utente all'endpoint di stima LUIS, aprire il file **dialogs -> flightBookingRecognizer.js**. Qui è dove viene inviata a LUIS l'espressione utente inserita nel bot. La risposta di LUIS viene restituita dal metodo **executeLuisQuery**.  

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. **dialogs -> mainDialog** acquisisce l'espressione e la invia a executeLuisQuery nel metodo actStep.


    ````javascript
    class MainDialog extends ComponentDialog {

        constructor(luisRecognizer, bookingDialog) {
            ...
            this.luisRecognizer = luisRecognizer;
            ...
        }


        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
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
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usare l'emulatore per testare il bot

Porre una domanda al bot per la finalità della prenotazione del volo.

1. Avviare l'emulatore del bot e selezionare **Open Bot** (Apri bot).
1. Nella finestra di dialogo popup **Open a bot** (Apri un bot) immettere l'URL del bot, ad esempio `http://localhost:3978/api/messages`. La route `/api/messages` corrisponde all'indirizzo Web del bot.
1. Immettere l'**ID dell'app Microsoft** e la **password dell'app Microsoft**, che si trovano nel file con estensione **env** nella radice del codice del bot scaricato.

1. Nell'emulatore del bot immettere `Book a flight from Seattle to Berlin tomorrow` e ottenere la stessa risposta per il bot di base ricevuto in **Test in Web Chat** (Testa nella chat Web).

    [![Risposta del bot di base nell'emulatore](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selezionare **Sì**. Il bot risponde con un riepilogo delle sue azioni. 
1. Nel log dell'emulatore del bot selezionare la riga che include `Luis Trace`. Verrà visualizzata la risposta JSON restituita da LUIS relativa alla finalità e alle entità dell'espressione.

    [![Risposta del bot di base nell'emulatore](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passaggi successivi

Vedere altri [esempi](https://github.com/microsoft/botframework-solutions) con i bot di conversazione. 

> [!div class="nextstepaction"]
> [Creare un'app di Language Understanding con un dominio soggetto personalizzato](luis-quickstart-intents-only.md)