---
title: Bot - C# - v3
titleSuffix: Language Understanding - Azure Cognitive Services
description: Usando C#, creare un chat bot integrato con Language Understanding (LUIS). Questo bot chat utilizza il dominio di HomeAutomation precompilato per implementare rapidamente una soluzione di bot.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: c3e142c65cdfe00fb26694063f7733d62882cc0c
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267369"
---
# <a name="luis-bot-in-c-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Bot LUIS in C# con Bot Framework 3.x e il bot app Web di Azure

Usando C#, creare un chat bot integrato con Language Understanding (LUIS). Questo bot chat utilizza il dominio di HomeAutomation precompilato per implementare rapidamente una soluzione di bot. Il bot viene compilato con Bot Framework 3.x e il bot per app Web di Azure.

## <a name="prerequisite"></a>Prerequisito

* [App LUIS HomeAutomation](luis-get-started-create-app.md). Le finalità di questa app LUIS sono mappate ai gestori della finestra di dialogo del bot. 

## <a name="luis-homeautomation-intents"></a>Finalità dell'app HomeAutomation LUIS

| Finalità | Espressione di esempio | Funzionalità bot |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Accendi le luci. | Quando la finalità LUIS `HomeAutomation.TurnOn` viene rilevata, il bot richiama il gestore della finestra di dialogo `OnIntent`. In questa finestra di dialogo è possibile richiamare un servizio IoT per accendere un dispositivo e indicare all'utente che il dispositivo si è acceso. |
| HomeAutomation.TurnOff | Spegni le luci della camera da letto. | Quando la finalità LUIS `HomeAutomation.TurnOff` viene rilevata, il bot richiama il gestore della finestra di dialogo `OffIntent`. In questa finestra di dialogo è possibile richiamare un servizio IoT per spegnere un dispositivo e indicare all'utente che il dispositivo si è spento. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Creare un bot LUIS (Language Understanding) con il servizio Bot

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Create new resource** (Crea nuova risorsa) nel menu in alto a sinistra.

    ![Creare una nuova risorsa nel portale di Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. Nella casella di ricerca cercare **Web App Bot** (Bot app Web). 

    ![Selezionare il bot app Web come tipo di risorsa](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. Nella finestra Web App Bot App fare clic su **Create** (Crea).

4. In **Bot Service** (Servizio bot) fornire le informazioni necessarie, quindi fare clic su **Create** (Crea). Il servizio bot e l'app LUIS vengono creati e distribuiti in Azure. Se si desidera usare il [priming del riconoscimento vocale](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), rivedere i [requisiti della regione](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) prima di creare il bot. 
    * Impostare il **nome dell'app** sul nome del bot. Il nome viene usato come sottodominio durante la distribuzione del bot nel cloud, ad esempio mynotesbot.azurewebsites.net. <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Selezionare la sottoscrizione, il [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), il piano di servizio dell'app e la [posizione](https://azure.microsoft.com/regions/).
    * Come **modello del bot**, selezionare:
        * **SDK v3**
        * **C#**
        * **Comprensione del linguaggio**
    * Selezionare la **posizione dell'app LUIS**. Si tratta della [regione](luis-reference-regions.md) in cui viene creata l'app.
    * Selezionare la casella di controllo di conferma per le note legali. Il contenuto delle note legali si trova sotto la casella di controllo.

    ![Servizio bot](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Confermare che il servizio bot è stato distribuito.
    * Fare clic su Notifications (Notifiche), ovvero l'icona a forma di campanello posta in alto nel portale di Azure. La notifica cambierà da **Deployment started** (Distribuzione iniziata) a **Deployment succeeded** (Distribuzione completata).
    * Dopo che la notifica è cambiata in **Deployment succeeded** (Distribuzione completata), selezionare **Go to resource** (Vai alla risorsa) in quella notifica.

> [!Note]
> Il processo di creazione web app bot crea una nuova app LUIS. Il training e la pubblicazione avvengono automaticamente. 

## <a name="try-the-default-bot"></a>Provare il bot predefinito

Confermare che il bot è stato distribuito selezionando la casella di controllo **Notifications** (Notifiche). Le notifiche cambiano da **Deployment in progress...** (Distribuzione in corso...) in **Deployment succeeded** (Distribuzione completata). Fare clic sul pulsante **Go to resource** (Vai alla risorsa) per aprire le risorse del bot.

Dopo aver distribuito il bot, fare clic su **Test in Web Chat** (Test nella chat Web) per aprire il riquadro Web Chat (Chat Web). Digitare "hello" (Ciao) nella chat Web.

  ![Testare il bot nella chat Web](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

Il bot risponde dicendo "You have reached Greeting. You said: hello" (Hai raggiunto Saluti. Hai detto ciao).  Questo conferma che il bot ha ricevuto il messaggio e che lo ha passato a un'app LUIS predefinita che ha creato. Questa app LUIS predefinita ha rilevato una finalità Greeting (Saluti). Nel passaggio successivo si connetterà il bot all'app LUIS creata in precedenza anziché a quella predefinita.

## <a name="connect-your-luis-app-to-the-bot"></a>Connettere l'app LUIS al bot

Aprire **Application Settings** (Impostazioni applicazione) e modificare il campo **LuisAppId** affinché contenga l'ID applicazione dell'app LUIS. Se l'app LUIS HomeAutomation è stata creata in una regione diversa dagli Stati Uniti occidentali, è necessario modificare anche **LuisAPIHostName**. **LuisAPIKey** è attualmente impostato sulla chiave di creazione. Impostarlo sulla chiave endpoint quando il traffico supera la quota del livello gratuito. 

  ![Aggiornare l'ID dell'app LUIS in Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Se non si dispone dell'ID dell'app [Home Automation](luis-get-started-create-app.md), accedere al sito Web [LUIS](luis-reference-regions.md) usando lo stesso account usato per accedere ad Azure. 
> 1. Fare clic su **My apps** (App personali). 
> 2. Trovare l'app LUIS creata in precedenza che contiene le finalità e le entità del dominio HomeAutomation.
> 3. Nella pagina **Settings** (Impostazioni) per l'app LUIS, trovare e copiare l'ID app. Assicurarsi che il [training](luis-interactive-test.md) e la [pubblicazione](luis-how-to-publish-app.md) siano stati eseguiti. 

    > [!WARNING]
    > If you delete your app ID or LUIS key, the bot will stop working.

## <a name="modify-the-bot-code"></a>Modificare il codice del bot

1. Fare clic su **Build** (Crea), quindi fare clic su **Open online code editor** (Apri editor di codice online).

   ![Aprire l'editor di codice online](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Fare clic con il pulsante destro del mouse su `build.cmd` e scegliere **Run from Console** (Esegui dalla console) per compilare l'app. Il servizio esegue automaticamente diversi passaggi di compilazione. La compilazione è completata quando viene visualizzato il messaggio "Finished successfully" (Completato correttamente).

3. Nell'editor del codice aprire `/Dialogs/BasicLuisDialog.cs`. Contiene il codice seguente:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>Modificare il codice nelle finalità HomeAutomation


1. Rimuovere i tre attributi finalità e i metodi per **Greeting** (Saluti), **Cancel** (Annulla) e **Help** (Guida). Queste finalità non sono usate nel dominio predefinito HomeAutomation. Assicurarsi di mantenere il metodo e l'attributo della finalità **None** (Nessuna). 

2. Aggiungere dipendenze nella parte superiore del file, con le altre dipendenze:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. Aggiungere costanti per gestire stringhe nella parte superiore della classe `BasicLuisDialog `:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Aggiungere il codice per le nuove finalità di `HomeAutomation.TurnOn` e `HomeAutomation.TurnOff` all'interno della classe `BasicLuisDialog `:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Aggiungere il codice per ottenere le entità trovate da LUIS all'interno della classe `BasicLuisDialog `:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Modificare il metodo **ShowLuisResult** nella classe `BasicLuisDialog ` per arrotondare il punteggio, raccogliere le entità e visualizzare il messaggio di risposta nel chatbot:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>Compilare il bot
Nell'editor di codice fare clic con il pulsante destro del mouse su `build.cmd` e selezionare **Run from Console** (Esegui dalla console).

![Compilare il bot Web ](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

La visualizzazione del codice viene sostituita con una finestra del terminale che mostra lo stato di avanzamento e i risultati della compilazione.

![Compilare il bot Web correttamente](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> Un metodo alternativo per compilare il bot consiste nel selezionare il relativo nome nella barra blu superiore e nel scegliere **Open Kudu Console** (Apri console kudu). Il console si apre in **D:\home**. 
> 
> Passare alla directory **D:\home\site\wwwroot** digitando: `cd site\wwwroot`
>
> Eseguire lo script di compilazione digitando: `build.cmd`

## <a name="test-the-bot"></a>Testare il bot

Nel portale di Azure fare clic su **Test in Web Chat** (Testa nella chat Web) per testare il bot. Digitare messaggi quali "Turn on the lights" (Accendi le luci) e "turn off my heater" (spegni il riscaldamento) per richiamare le finalità aggiunte.

   ![Testare il bot HomeAutomation nella chat Web](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> È possibile rieseguire il training dell'app LUIS senza alcuna modifica al codice del bot. Vedere [Aggiungere espressioni di esempio](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) ed [eseguire il training e il testing dell'app LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test). 

## <a name="download-the-bot-to-debug"></a>Scaricare il bot per eseguire il debug
Se il bot non funziona, scaricare il progetto nel computer locale e continuare il [debug](https://docs.microsoft.com/bot-framework/bot-service-debug-bot#debug-a-c-bot). 

## <a name="learn-more-about-bot-framework"></a>Ulteriori informazioni su Bot Framework
Ulteriori informazioni su [Bot Framework](https://dev.botframework.com/) e [3.x](https://github.com/Microsoft/BotBuilder) e [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK.

## <a name="next-steps"></a>Passaggi successivi

Aggiungere le finalità LUIS e le finestre di dialogo del servizio Bot per gestire le finalità **Help** (Guida), **Cancel** (Annulla) e **Greeting** (Saluti). Ricordarsi eseguire il training, di pubblicare e di [compilare](#build-the-bot) il bot dell'app Web. LUIS e il bot devono avere le stesse finalità.

Vedere altri [esempi](https://github.com/Microsoft/AI) con i bot di conversazione. 

> [!div class="nextstepaction"]
> [Aggiungere finalità](./luis-how-to-add-intents.md)
> [Priming del riconoscimento vocale](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)

<!-- tested on Win10 -->
