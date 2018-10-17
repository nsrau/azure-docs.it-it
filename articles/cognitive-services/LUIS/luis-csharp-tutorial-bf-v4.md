---
title: Bot LUIS con C# - Esercitazione - Bot per app Web - Bot Framework SDK 4.0
titleSuffix: Azure Cognitive Services
description: Usando C#, creare un chat bot integrato con Language Understanding (LUIS). Questo bot chat utilizza l'app Risorse umane per implementare rapidamente una soluzione di bot. Il bot viene compilato con la versione Bot Framework 4 e il bot per app Web di Azure.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/25/2018
ms.author: diberry
ms.openlocfilehash: ce5b704a7ac251621698352608ea3eefa4629aea
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886575"
---
# <a name="tutorial-luis-bot-in-c"></a>Esercitazione: Bot LUIS in C#
Usando C#, è possibile creare un chat bot integrato con Language Understanding (LUIS). Questo bot Usa l'app HomeAutomation per implementare una soluzione di bot. Il bot viene compilato con il [bot per app Web](https://docs.microsoft.com/azure/bot-service/) di Azure e la [versione Bot Framework](https://github.com/Microsoft/botbuilder-js) 4.

**In questa esercitazione si apprenderà come:**

> [!div class="checklist"]
> * Creare un bot app Web. Questo processo permette di creare una nuova app LUIS.
> * Aggiungere un dominio predefinito per il nuovo modello di LUIS
> * Scaricare il progetto creato dal servizio Web bot
> * Avviare bot ed emulatore localmente nel computer in uso
> * Modificare il codice di bot per le nuove finalità di LUIS
> * Visualizzare i risultati di espressione nel bot

## <a name="prerequisites"></a>Prerequisiti

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>Creare bot app Web

1. Nel [portale di Azure](https://portal.azure.com), selezionare **Crea nuova risorsa**.

2. Nella casella di ricerca cercare e selezionare **Web App Bot** (Bot app Web). Selezionare **Create**.

3. In **Bot Service** (Servizio bot) fornire le informazioni necessarie:

    |Impostazione|Scopo|Impostazione consigliata|
    |--|--|--|
    |Nome bot|Nome risorsa|`luis-csharp-bot-` + `<your-name>`, ad esempio, `luis-csharp-bot-johnsmith`|
    |Sottoscrizione|Sottoscrizione in cui creare bot.|Chiave di sottoscrizione primaria.
    |Gruppo di risorse|Gruppo logico di risorse di Azure|Creare un nuovo gruppo per archiviare tutte le risorse usate con questo bot, denominare il gruppo `luis-csharp-bot-resource-group`.|
    |Località|Area di Azure - non deve essere la stessa della creazione o dell'area di pubblicazione di LUIS.|`westus`|
    |Piano tariffario|Utilizzato per la fatturazione e i limiti delle richieste di servizio.|`F0` è il livello gratuito.
    |Nome app|Il nome viene usato come sottodominio durante la distribuzione del bot nel cloud, (ad esempio humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, ad esempio, `luis-csharp-bot-johnsmith`|
    |Modello del bot|Impostazioni di Bot framework - vedere la tabella seguente|
    |Percorso app LUIS|Deve essere lo stesso dell'area risorse di LUIS|`westus`|

4. In **impostazioni del modello di Bot**, selezionare le opzioni seguenti, quindi scegliere il pulsante **seleziona** sotto queste impostazioni:

    |Impostazione|Scopo|Selezione|
    |--|--|--|
    |Versione dell'SDK|Versione di Bot Framework|**SDK v4**|
    |Linguaggio SDK|Linguaggio di programmazione di bot|**C#**|
    |Bot echo/di base|Tipo di bot|**Bot di base**|
    
5. Selezionare **Create**. Il servizio bot viene creato e distribuito in Azure. Parte di questo processo permette di creare una nuova app denominata LUIS`luis-csharp-bot-XXXX`. Questo nome è basato sul nome del bot e dell'app nella sezione precedente.

    [ ![Creare bot app Web](./media/bfv4-csharp/create-web-app-service.png) ](./media/bfv4-csharp/create-web-app-service.png#lightbox)

6. Lasciare aperta la scheda del browser. Per eventuali passaggi con il portale di LUIS, aprire una nuova scheda del browser. Quando viene distribuito il nuovo servizio bot, passare alla sezione successiva.

## <a name="add-prebuilt-domain-to-model"></a>Aggiungere dominio predefinito al modello
Parte della distribuzione del servizio bot crea una nuova app LUIS con le finalità ed espressioni di esempio. Il bot fornisce il mapping delle finalità alla nuova app LUIS per le seguenti finalità: 

|Finalità LUIS di bot di base|espressione di esempio|
|--|--|
|Annulla|`stop`|
|Saluti|`hello`|
|Guida|`help`|
|Nessuna|Qualsiasi elemento all'esterno del dominio dell'app.|

Aggiungere l'app HomeAutomation predefinita al modello per la gestione delle espressioni come: `Turn off the living room lights`

1. Passare al portale [LUIS](https://www.luis.ai) e accedere.
2. Sulla pagina **My Apps**, selezionare la colonna **Created date** (Data creazione) per ordinare in base alla data in cui l'app è stata creata. Il servizio Azure Bot ha permesso di creare una nuova app nella sezione precedente. Il suo nome è `luis-csharp-bot-` + `<your-name>` + 4 caratteri casuali.
3. Aprire l'app e selezionare la sezione **compilazione** nel riquadro di spostamento superiore.
4. Selezionare **Prebuilt Domains** (Domini predefiniti) dal riquadro di navigazione a sinistra.
5. Selezionare il dominio **HomeAutomation** selezionando **Add domain** (Aggiungi dominio) nella relativa scheda.
6. Selezionare **Train** (Esegui il training) nel menu in alto a destra.
7. Selezionare **Publish** (Pubblica) nel menu in alto a destra. 

    L'app creata dal servizio Azure Bot ora ha finalità nuove:

    |Nuove finalità del bot di base|espressione di esempio|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>Scaricare il bot dell'app Web 
Per sviluppare il codice di bot app Web, scaricare il codice da usare sul computer locale. 

1. Nel portale di Azure, sempre nella risorsa bot app Web, selezionare le **impostazioni applicazione** e copiare i valori di **botFilePath** e **botFileSecret**. È necessario aggiungerli a un file di ambiente in un secondo momento. 

2. Nel portale di Azure, selezionare **Build** (Compila) dalla sezione **Bot Management** (Gestione bot). 

3. Selezionare **Scarica il codice sorgente del bot**. 

    [ ![Scarica il codice sorgente del bot dell'app Web per il bot di base](../../../includes/media/cognitive-services-luis/bfv4/download-code.png) ](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Quando il codice sorgente è compresso, un messaggio fornirà un collegamento per scaricare il codice. Selezionare il collegamento. 

5. Salvare il file zip nel computer locale ed estrarre i file. Aprire il progetto. 

6. Aprire il file bot.cs e cercare `_services.LuisServices`. Qui è dove viene inviata a LUIS l'espressione utente inserita nel bot.

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    Il bot invia l'espressione utente a LUIS e ottiene i risultati. La finalità superiore determina il flusso della conversazione. 


## <a name="start-the-bot"></a>Avviare il bot
Prima di modificare qualsiasi codice o le impostazioni, verificare il funzionamento del bot. 

1. Aprire il file della soluzione in Visual Studio. 

2. Creare un file `appsettings.json` per contenere le variabili di bot ricercate dal codice bot:

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    Impostare i valori delle variabili per i valori copiati da impostazioni dell'applicazione del servizio Azure Bot nel passaggio 1 della sezione **[Scarica il bot per app Web](#download-the-web-app-bot)**.

3. Avviare il bot in Visual Studio. Verrà visualizzata una finestra del browser con il sito Web dell'app Web del bot all'indirizzo `http://localhost:3978/`.

## <a name="start-the-emulator"></a>Avviare l'emulatore

1. Avviare l'emulatore del bot.

2. Nell'emulatore di bot, selezionare il file *.bot nella radice del progetto. Tale file `.bot` comprende l'endpoint dell'URL del bot per i messaggi:

    [ ![Bot Emulator v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Impostare il segreto di bot copiato da impostazioni dell'applicazione del servizio Azure Bot nel passaggio 1 della sezione **[Scarica il bot per app Web](#download-the-web-app-bot)**. In questo modo l'emulatore può accedere ai campi crittografati nel file `.bot`.

    ![Segreto dell'emulatore BOT v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. Nell'emulatore di Bot, immettere `Hello` e ottenere la risposta appropriata per il bot di base.

    [ ![Risposta del bot di base nell'emulatore](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Modificare il codice di bot 

Nel file `BasicBot.cs`, aggiungere il codice per gestire le nuove finalità. 

1. Nella parte superiore del file, trovare la sezione **Finalità LUIS supportate** e aggiungere le costanti per le finalità HomeAutomation:

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    Si noti che il punto, `.`, tra il dominio e la finalità tramite l'app del portale LUIS viene sostituito con un carattere di sottolineatura, `_`. 

2. Trovare il metodo **OnTurnAsync** che riceve la stima di LUIS dell'espressione. Aggiungere codice nell'istruzione switch per restituire la risposta di LUIS per le due finalità HomeAutomation. 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    ```

    Il bot non ha la stessa risposta esatta di una richiesta all'API REST di LUIS per cui è importante conoscere le differenze esaminando la risposta JSON. Le proprietà di testo e finalità sono le stesse ma sono stati modificati i valori della proprietà dell'entità. 

    ```JSON
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```



## <a name="view-results-in-bot"></a>Visualizzare i risultati nel bot

1. Nell'emulatore di bot, immettere l'espressione: `Turn on the livingroom lights to 50%`

2. Il bot risponde con:

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    

## <a name="learn-more-about-bot-framework"></a>Ulteriori informazioni su Bot Framework
Il servizio Azure Bot utilizza l’SDK Bot Framework. Ulteriori informazioni su SDK e bot framework:

* Documentazione del [servizio Azure Bot](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4
* [Esempi di Bot Builder](https://github.com/Microsoft/botbuilder-samples)
* [SDK Bot Builder](https://docs.microsoft.com/en-us/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Strumenti di Bot Builder](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>Passaggi successivi

È stato creato un servizio Azure Bot, copiato il segreto del bot e il percorso del file `.bot`, quindi è stato scaricato il file ZIP del codice. È stato aggiunto il dominio HomeAutomation predefinito all'app LUIS creato come parte del nuovo servizio bot Azure, quindi sottoposto a training ed è stata di nuovo pubblicata l'app. È stato estratto il progetto di codice, creato un file di ambiente (`.env`) e impostato il segreto del bot e il percorso del file `.bot`. Nel file bot.js è stato aggiunto il codice per gestire le due nuove finalità. Quindi è stato testato il bot nell'emulatore di bot per vedere la risposta di LUIS per un’espressione di una delle nuove finalità. 


> [!div class="nextstepaction"]
> [Creare un dominio personalizzato in LUIS](luis-quickstart-intents-only.md)
