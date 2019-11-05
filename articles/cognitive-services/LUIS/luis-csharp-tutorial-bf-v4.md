---
title: 'Esercitazione: Bot LUIS (Language Understanding) C# v4'
titleSuffix: Azure Cognitive Services
description: Usando C#, creare un chat bot integrato con Language Understanding (LUIS). Il bot viene creato con Bot Framework versione 4 e il servizio bot app Web di Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 213449a78baf8fc2b7cb6c74709efeaf2a50d5b2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495459"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Esercitazione: Usare un bot app Web abilitato con Language Understanding in C#

Usare C# per creare un chatbot integrato con Language Understanding (LUIS). Il bot viene creato con la risorsa [bot app Web](https://docs.microsoft.com/azure/bot-service/) di Azure e con [Bot Framework versione 4](https://github.com/Microsoft/botbuilder-dotnet).

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]


**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Creare un bot app Web. Questo processo permette di creare una nuova app LUIS.
> * Scaricare il progetto del bot creato dal servizio per i bot Web
> * Avviare bot ed emulatore localmente nel computer in uso
> * Visualizzare i risultati di espressione nel bot

## <a name="prerequisites"></a>Prerequisiti

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Creare una risorsa bot app Web

1. Nel [portale di Azure](https://portal.azure.com), selezionare **Crea nuova risorsa**.

1. Nella casella di ricerca cercare e selezionare **Web App Bot** (Bot app Web). Selezionare **Create** (Crea).

1. In **Bot Service** (Servizio bot) fornire le informazioni necessarie:

    |Impostazione|Scopo|Impostazione consigliata|
    |--|--|--|
    |Nome bot|Nome risorsa|`luis-csharp-bot-` + `<your-name>`, ad esempio, `luis-csharp-bot-johnsmith`|
    |Subscription|Sottoscrizione in cui creare bot.|Chiave di sottoscrizione primaria.
    |Resource group|Gruppo logico di risorse di Azure|Creare un nuovo gruppo per archiviare tutte le risorse usate con questo bot, denominare il gruppo `luis-csharp-bot-resource-group`.|
    |Location|Area di Azure - non deve essere la stessa della creazione o dell'area di pubblicazione di LUIS.|`westus`|
    |Piano tariffario|Utilizzato per la fatturazione e i limiti delle richieste di servizio.|`F0` è il livello gratuito.
    |Nome app|Il nome viene usato come sottodominio durante la distribuzione del bot nel cloud, (ad esempio humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, ad esempio, `luis-csharp-bot-johnsmith`|
    |Modello del bot|Impostazioni di Bot framework - vedere la tabella seguente|
    |Percorso app LUIS|Deve essere lo stesso dell'area risorse di LUIS|`westus`|
    |Piano di servizio app/Località|Non modificare il valore predefinito.|
    |Application Insights|Non modificare il valore predefinito.|
    |ID e password dell'app Microsoft|Non modificare il valore predefinito.|

1. In **Bot template** (Modello di bot) selezionare le opzioni seguenti e quindi scegliere il pulsante **Select** (Seleziona) sotto queste impostazioni:

    |Impostazione|Scopo|Selezione|
    |--|--|--|
    |Versione dell'SDK|Versione di Bot Framework|**SDK v4**|
    |Linguaggio SDK|Linguaggio di programmazione di bot|**C#**|
    |Bot|Tipo di bot|**Bot di base**|
    
1. Selezionare **Create** (Crea). Il servizio bot viene creato e distribuito in Azure. Parte di questo processo permette di creare una nuova app denominata LUIS`luis-csharp-bot-XXXX`. Questo nome è basato sul nome dell'app del servizio Azure Bot.

    [![Creare un bot app Web](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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

1. Quando la finestra di dialogo popup visualizza il messaggio **Include app settings in the downloaded zip file?** (Includere le impostazioni dell'app nel file ZIP scaricato?) selezionare **Yes** (Sì).

1. Quando il codice sorgente è compresso, un messaggio fornirà un collegamento per scaricare il codice. Selezionare il collegamento. 

1. Salvare il file zip nel computer locale ed estrarre i file. Aprire il progetto con Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Esaminare il codice per inviare l'espressione a LUIS e ottenere la risposta

1. Per inviare l'espressione utente all'endpoint di stima LUIS, aprire il file **FlightBookingRecognizer.cs**. Qui è dove viene inviata a LUIS l'espressione utente inserita nel bot. La risposta di LUIS viene restituita dal metodo **RecognizeAsync**.  

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    
    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;
    
            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);
    
                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }
    
            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;
    
            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);
    
            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. Aprire **Dialogs -> MainDialog.cs** per acquisire l'espressione e inviarla a executeLuisQuery nel metodo actStep. 

    ```csharp
    public class MainDialog : ComponentDialog
    {
        private readonly FlightBookingRecognizer _luisRecognizer;

        ...

        public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                    : base(nameof(MainDialog))
        {
            _luisRecognizer = luisRecognizer;
            ...
        }

        private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            if (!_luisRecognizer.IsConfigured)
            {
                // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
            var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
            switch (luisResult.TopIntent().intent)
            {
                case FlightBooking.Intent.BookFlight:
                    await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                    // Initialize BookingDetails with any entities we may have found in the response.
                    var bookingDetails = new BookingDetails()
                    {
                        // Get destination and origin from the composite entities arrays.
                        Destination = luisResult.ToEntities.Airport,
                        Origin = luisResult.FromEntities.Airport,
                        TravelDate = luisResult.TravelDate,
                    };

                    // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                case FlightBooking.Intent.GetWeather:
                    // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                    var getWeatherMessageText = "TODO: get weather flow here";
                    var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                    break;

                default:
                    // Catch all for unhandled intents
                    var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                    var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                    break;
            }

            return await stepContext.NextAsync(null, cancellationToken);
        }
        
        ...

    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Avviare il codice del bot in Visual Studio

Avviare il bot in Visual Studio 2019. Verrà visualizzata una finestra del browser con il sito Web dell'app Web del bot all'indirizzo `http://localhost:3978/`. Nella home page vengono visualizzate informazioni relative al bot.

![Nella home page vengono visualizzate informazioni relative al bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usare l'emulatore per testare il bot

1. Avviare l'emulatore del bot e selezionare **Open Bot** (Apri bot).
1. Nella finestra di dialogo popup **Open a bot** (Apri un bot) immettere l'URL del bot, ad esempio `http://localhost:3978/api/messages`. La route `/api/messages` corrisponde all'indirizzo Web del bot.
1. Immettere l'**ID dell'app Microsoft** e la **password dell'app Microsoft**, che si trovano nel file **appsettings.json** nella radice del codice del bot scaricato.


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
