---
title: Bot - Node.js - v4
titleSuffix: Azure Cognitive Services
description: Usando Node.js, creare un chat bot integrato con Language Understanding (LUIS). Questo bot chat utilizza l'app Risorse umane per implementare rapidamente una soluzione di bot. Il bot viene compilato con la versione Bot Framework 4 e il bot per app Web di Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 852cc5e5b3b018d871bd40775dce64d669fb3011
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880615"
---
# <a name="tutorial-luis-bot-in-nodejs-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>Esercitazione: Bot LUIS in Node.js con Bot Framework 4.x e il bot app Web di Azure
Usando Node.js, è possibile creare un chat bot integrato con Language Understanding (LUIS). Questo bot Usa l'app HomeAutomation per implementare una soluzione di bot. Il bot viene compilato con il [bot per app Web](https://docs.microsoft.com/azure/bot-service/) di Azure e la [versione Bot Framework](https://github.com/Microsoft/botbuilder-js) 4.

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
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-web-app-bot"></a>Creare bot app Web

1. Nel [portale di Azure](https://portal.azure.com), selezionare **Crea nuova risorsa**.

2. Nella casella di ricerca cercare e selezionare **Web App Bot** (Bot app Web). Selezionare **Create**.

3. In **Bot Service** (Servizio bot) fornire le informazioni necessarie:

    |Impostazione|Scopo|Impostazione consigliata|
    |--|--|--|
    |Nome bot|Nome risorsa|`luis-nodejs-bot-` + `<your-name>`, ad esempio, `luis-nodejs-bot-johnsmith`|
    |Sottoscrizione|Sottoscrizione in cui creare bot.|Chiave di sottoscrizione primaria.
    |Gruppo di risorse|Gruppo logico di risorse di Azure|Creare un nuovo gruppo per archiviare tutte le risorse usate con questo bot, denominare il gruppo `luis-nodejs-bot-resource-group`.|
    |Località|Area di Azure - non deve essere la stessa della creazione o dell'area di pubblicazione di LUIS.|`westus`|
    |Piano tariffario|Utilizzato per la fatturazione e i limiti delle richieste di servizio.|`F0` è il livello gratuito.
    |Nome app|Il nome viene usato come sottodominio durante la distribuzione del bot nel cloud, (ad esempio humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, ad esempio, `luis-nodejs-bot-johnsmith`|
    |Modello del bot|Impostazioni di Bot framework - vedere la tabella seguente|
    |Percorso app LUIS|Deve essere lo stesso dell'area risorse di LUIS|`westus`|

4. In **impostazioni del modello di Bot**, selezionare le opzioni seguenti, quindi scegliere il pulsante **seleziona** sotto queste impostazioni:

    |Impostazione|Scopo|Selezione|
    |--|--|--|
    |Versione dell'SDK|Versione di Bot Framework|**SDK v4**|
    |Linguaggio SDK|Linguaggio di programmazione di bot|**Node.js**|
    |Bot echo/di base|Tipo di bot|**Bot di base**|
    
5. Selezionare **Create**. Il servizio bot viene creato e distribuito in Azure. Parte di questo processo permette di creare una nuova app denominata LUIS`luis-nodejs-bot-XXXX`. Questo nome è basato sul nome del bot e dell'app nella sezione precedente.

    [![Creare un bot app Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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
2. Sulla pagina **My Apps**, selezionare la colonna **Created date** (Data creazione) per ordinare in base alla data in cui l'app è stata creata. Il servizio Azure Bot ha permesso di creare una nuova app nella sezione precedente. Il suo nome è `luis-nodejs-bot-` + `<your-name>` + 4 caratteri casuali.
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

    [![Scaricare il codice sorgente del bot app Web per il bot di base](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Quando il codice sorgente è compresso, un messaggio fornirà un collegamento per scaricare il codice. Selezionare il collegamento. 

5. Salvare il file zip nel computer locale ed estrarre i file. Aprire il progetto. 

6. Aprire il file bot.js e cercare `const results = await this.luisRecognizer.recognize(context);`. Qui è dove viene inviata a LUIS l'espressione utente inserita nel bot.

   ```javascript
    /**
     * Driver code that does one of the following:
     * 1. Display a welcome card upon startup
     * 2. Use LUIS to recognize intents
     * 3. Start a greeting dialog
     * 4. Optionally handle Cancel or Help interruptions
     *
     * @param {Context} context turn context from the adapter
     */
    async onTurn(context) {
        // Create a dialog context
        const dc = await this.dialogs.createContext(context);

        if(context.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the current activity message.
            const results = await this.luisRecognizer.recognize(context);
            
            const topIntent = LuisRecognizer.topIntent(results);

            // handle conversation interrupts first
            const interrupted = await this.isTurnInterrupted(dc, results);
            if(interrupted) {
                return;
            }

            // Continue the current dialog
            const dialogResult = await dc.continue();

            switch(dialogResult.status) {
                case DialogTurnStatus.empty:
                    switch (topIntent) {
                        case GREETING_INTENT:
                            await dc.begin(GREETING_DIALOG);
                            break;

                        case NONE_INTENT:
                        default:
                            // help or no intent identified, either way, let's provide some help
                            // to the user
                            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
                            break;
                    }

                case DialogTurnStatus.waiting:
                    // The active dialog is waiting for a response from the user, so do nothing
                break;

                case DialogTurnStatus.complete:
                    await dc.end();
                    break;

                default:
                    await dc.cancelAll();
                    break;

            }

        } else if (context.activity.type === 'conversationUpdate' && context.activity.membersAdded[0].name === 'Bot') {
            // When activity type is "conversationUpdate" and the member joining the conversation is the bot
            // we will send our Welcome Adaptive Card.  This will only be sent once, when the Bot joins conversation
            // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
            const welcomeCard = CardFactory.adaptiveCard(WelcomeCard);
            await context.sendActivity({ attachments: [welcomeCard] });
        }
    }
    ```

    Il bot invia l'espressione utente a LUIS e ottiene i risultati. La finalità superiore determina il flusso della conversazione. 


## <a name="start-the-bot"></a>Avviare il bot
Prima di modificare qualsiasi codice o le impostazioni, verificare il funzionamento del bot. 

1. In Visual Studio Code, aprire una finestra del terminale. 

2. Installare le dipendenze di npm per questo bot. 

    ```bash
    npm install
    ```
3. Creare un file per contenere le variabili di ambiente ricercate dal codice bot. Denominare il file `.env`. Aggiungere le variabili di ambiente seguenti:

    <!--there is no code language that represents an .env file correctly-->
    ```env
    botFilePath=
    botFileSecret=
    ```

    Impostare i valori delle variabili di ambiente per i valori copiati da impostazioni dell'applicazione del servizio Azure Bot nel passaggio 1 della sezione **[Scarica il bot per app Web](#download-the-web-app-bot)**.

4. Avviare il bot in modalità espressione di controllo. Le modifiche apportate al codice dopo questo avvio causeranno un riavvio automatico dell'app.

    ```bash
    npm run watch
    ```

5. Quando si avvia il bot, la finestra del terminale mostra la porta locale sulla quale il bot è in esecuzione:

    ```console
    > basic-bot@0.1.0 start C:\Users\pattiowens\repos\BFv4\luis-nodejs-bot-src
    > node ./index.js NODE_ENV=development

    restify listening to http://[::]:3978
    
    Get the Emulator: https://aka.ms/botframework-emulator
    
    To talk to your bot, open the luis-nodejs-bot-pattiowens.bot file in the Emulator
    ```

## <a name="start-the-emulator"></a>Avviare l'emulatore

1. Avviare l'emulatore del bot. 

2. Nell'emulatore di bot, selezionare il file *.bot nella radice del progetto. Tale file `.bot` comprende l'endpoint dell'URL del bot per i messaggi:

    [![Emulatore bot v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png)](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Impostare il segreto di bot copiato da impostazioni dell'applicazione del servizio Azure Bot nel passaggio 1 della sezione **[Scarica il bot per app Web](#download-the-web-app-bot)**. In questo modo l'emulatore può accedere ai campi crittografati nel file .bot.

    ![Segreto dell'emulatore BOT v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)


4. Nell'emulatore di Bot, immettere `Hello` e ottenere la risposta appropriata per il bot di base.

    [![Risposta del bot di base nell'emulatore](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png)](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Modificare il codice di bot 

Nel file `bot.js`, aggiungere il codice per gestire le nuove finalità. 

1. Nella parte superiore del file, trovare la sezione **Finalità LUIS supportate** e aggiungere le costanti per le finalità HomeAutomation:

   ```javascript
    // Supported LUIS Intents
    const GREETING_INTENT = 'Greeting';
    const CANCEL_INTENT = 'Cancel';
    const HELP_INTENT = 'Help';
    const NONE_INTENT = 'None';
    const TURNON_INTENT = 'HomeAutomation_TurnOn'; // new intent
    const TURNOFF_INTENT = 'HomeAutomation_TurnOff'; // new intent
    ```

    Si noti che il punto, `.`, tra il dominio e la finalità tramite l'app del portale LUIS viene sostituito con un carattere di sottolineatura, `_`. 

2. Trovare il **isTurnInterrupted** che riceve la stima LUIS dell'espressione e aggiungere una riga per stampare il risultato sulla console.

   ```javascript
    /**
     * Look at the LUIS results and determine if we need to handle
     * an interruptions due to a Help or Cancel intent
     *
     * @param {DialogContext} dc - dialog context
     * @param {LuisResults} luisResults - LUIS recognizer results
     */
    async isTurnInterrupted(dc, luisResults) {
        console.log(JSON.stringify(luisResults));
    ...
    ```

    Il bot non ha la stessa risposta esatta di una richiesta all'API REST di LUIS per cui è importante conoscere le differenze esaminando la risposta JSON. Le proprietà di testo e finalità sono le stesse ma sono stati modificati i valori della proprietà dell'entità. 

    ```json
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

3. Aggiungere le finalità all'istruzione di commutazione del metodo onTurn per il case `DialogTurnStatus.empty`:

   ```javascript
    switch (topIntent) {
        case GREETING_INTENT:
            await dc.begin(GREETING_DIALOG);
            break;

        // New HomeAutomation.TurnOn intent
        case TURNON_INTENT: 

            await dc.context.sendActivity(`TurnOn intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        // New HomeAutomation.TurnOff intent
        case TURNOFF_INTENT: 

            await dc.context.sendActivity(`TurnOff intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        case NONE_INTENT:
        default:
            // help or no intent identified, either way, let's provide some help
            // to the user
            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
            break;
    }
    ```

## <a name="view-results-in-bot"></a>Visualizzare i risultati nel bot

1. Nell'emulatore di bot, immettere l'espressione: `Turn on the livingroom lights to 50%`

2. Il bot risponde con:

    ```json
    TurnOn intent found, entities included: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```

## <a name="learn-more-about-bot-framework"></a>Ulteriori informazioni su Bot Framework
Il servizio Azure Bot utilizza l’SDK Bot Framework. Ulteriori informazioni su SDK e bot framework:

* Documentazione del [servizio Azure Bot](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4
* [Esempi di Bot Builder](https://github.com/Microsoft/botbuilder-samples)
* [SDK Bot Builder](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Strumenti di Bot Builder](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>Passaggi successivi

È stato creato un servizio bot Azure, copiato il segreto del bot e il percorso del file .bot, scaricato il file zip del codice. È stato aggiunto il dominio HomeAutomation predefinito all'app LUIS creato come parte del nuovo servizio bot Azure, quindi sottoposto a training ed è stata di nuovo pubblicata l'app. È stato estratto il progetto di codice, creato un file di ambiente (`.env`) e impostato il segreto del bot e il percorso del file .bot. Nel file bot.js è stato aggiunto il codice per gestire le due nuove finalità. Quindi è stato testato il bot nell'emulatore di bot per vedere la risposta di LUIS per un’espressione di una delle nuove finalità. 


> [!div class="nextstepaction"]
> [Creare un dominio personalizzato in LUIS](luis-quickstart-intents-only.md)
