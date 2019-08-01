---
title: Bot LUIS (Language Understanding) C# v4
titleSuffix: Azure Cognitive Services
description: Usando C#, creare un chat bot integrato con Language Understanding (LUIS). Il bot viene creato con Bot Framework versione 4 e il servizio bot app Web di Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 210724e8a8b9b585a3e308b8e321d809e4e897a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560663"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Esercitazione: Usare un bot app Web abilitato con Language Understanding in C#

Usare C# per creare un chatbot integrato con Language Understanding (LUIS). Il bot viene creato con la risorsa [bot app Web](https://docs.microsoft.com/azure/bot-service/) di Azure e con [Bot Framework versione 4](https://github.com/Microsoft/botbuilder-dotnet).

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
|Nessuna|Qualsiasi elemento all'esterno del dominio dell'app.|

## <a name="test-the-bot-in-web-chat"></a>Testare il bot nella chat Web

1. Sempre nel portale di Azure per il nuovo bot, selezionare **Test in Web Chat** (Testa nella chat Web). 
1. Nella casella di testo **Type your message** (Digita il tuo messaggio) immettere il testo `hello`. Il bot risponde con informazioni sul relativo framework e con query di esempio per il modello LUIS specifico, ad esempio la prenotazione di un volo per Parigi. 

    ![Screenshot del portale di Azure, immettere il testo "hello".](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

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

1. Aprire il file **LuisHelper.cs**. Qui è dove viene inviata a LUIS l'espressione utente inserita nel bot. La risposta di LUIS viene restituita dal metodo come oggetto **BookDetails**. Quando si crea un bot personalizzato, è necessario creare anche l'oggetto per la restituzione dei dettagli di LUIS. 


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
        }
    }
    ```

1. Aprire **BookingDetails.cs** per visualizzare il modo in cui l'oggetto astrae le informazioni relative a LUIS. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. Aprire **Dialogs -> BookingDialog.cs** per comprendere come viene usato l'oggetto BookingDetails per gestire il flusso della conversazione. Vengono richiesti i dettagli del viaggio in più passaggi, quindi l'intera prenotazione viene confermata e infine ripetuta all'utente. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>Avviare il codice del bot in Visual Studio

Avviare il bot in Visual Studio. Verrà visualizzata una finestra del browser con il sito Web dell'app Web del bot all'indirizzo `http://localhost:3978/`. Nella home page vengono visualizzate informazioni relative al bot.

![Nella home page vengono visualizzate informazioni relative al bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usare l'emulatore per testare il bot

1. Avviare l'emulatore del bot e selezionare **Open Bot** (Apri bot).
1. Nella finestra di dialogo popup **Open a bot** (Apri un bot) immettere l'URL del bot, ad esempio `http://localhost:3978/api/messages`. La route `/api/messages` corrisponde all'indirizzo Web del bot.
1. Immettere l'**ID dell'app Microsoft** e la **password dell'app Microsoft**, che si trovano nel file **appsettings.json** nella radice del codice del bot scaricato.

    Facoltativamente, è possibile creare una nuova configurazione del bot e copiare i valori di `appId` e `appPassword` dal file **appsettings.json** nel progetto di Visual Studio relativo al bot. Il nome del file di configurazione del bot deve essere identico a quello del bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. Nell'emulatore del bot immettere `Hello` e ottenere la stessa risposta per il bot di base ricevuto in **Test in Web Chat** (Testa nella chat Web).

    [![Risposta del bot di base nell'emulatore](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Porre una domanda al bot per la finalità della prenotazione del volo

1. Nell'emulatore del bot prenotare un volo specificando l'espressione seguente: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    L'emulatore del bot chiede di confermare. 

1. Selezionare **Sì**. Il bot risponde con un riepilogo delle sue azioni. 
1. Nel log dell'emulatore del bot selezionare la riga che include `Luis Trace`. Verrà visualizzata la risposta JSON restituita da LUIS relativa alla finalità e alle entità dell'espressione.

    [![Risposta del bot di base nell'emulatore](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passaggi successivi

Vedere altri [esempi](https://github.com/microsoft/botframework-solutions) con i bot di conversazione. 

> [!div class="nextstepaction"]
> [Creare un'app di Language Understanding con un dominio soggetto personalizzato](luis-quickstart-intents-only.md)
