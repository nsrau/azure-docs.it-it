---
title: 'Esercitazione: Le voci abilitano il bot usando Speech SDK - Servizio di riconoscimento vocaleTutorial: Voices enable your bot using Speech SDK - Speech service'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione verrà creato un bot Echo usando Microsoft Bot-Framework, lo si distribuirà in Azure e lo si registrerà con il canale Bot-Framework Direct Line Speech. Quindi configurerai un'app client di esempio per Windows che ti consente di parlare con il bot e sentirlo rispondere.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 96d2c2e5e3772575e681d2db079ab0122b7014e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348544"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Esercitazione: Abilitare il bot con la voce usando l'SDK di riconoscimento vocaleTutorial: Voice-enable your bot using the Speech SDK

È ora possibile usare la potenza del servizio di riconoscimento vocale per abilitare facilmente un bot di chat.

In questa esercitazione verrà creato un bot Echo usando Microsoft Bot-Framework, lo si distribuirà in Azure e lo si registrerà con il canale Bot-Framework Direct Line Speech. Quindi configurerai un'app client di esempio per Windows che ti consente di parlare con il bot e sentirlo rispondere.

Questa esercitazione è progettata per gli sviluppatori che stanno appena iniziando il percorso con Azure, bot Bot-Framework, Direct Line Speech o Speech SDK e desiderano creare rapidamente un sistema funzionante con codifica limitata. Non è necessaria alcuna esperienza o familiarità con questi servizi.

Al termine di questo esercizio, sarà stato impostato un sistema che funzionerà come segue:

1. L'applicazione client di esempio è configurata per connettersi al canale Direct Line Speech e al bot Echo
1. L'audio viene registrato dal microfono predefinito sulla pressione del pulsante (o registrato continuamente se è attivata la parola chiave personalizzata)
1. Facoltativamente, si verifica il rilevamento personalizzato delle parole chiave, che genera lo streaming audio nel cloud
1. Utilizzando Speech SDK, l'app si connette al canale Direct Line Speech e trasmette l'audio
1. Facoltativamente, la verifica della parola chiave con maggiore precisione avviene nel servizio
1. L'audio viene passato al servizio di riconoscimento vocale e trascritto in testo
1. Il testo riconosciuto viene passato all'Echo-Bot come attività Bot Framework 
1. Il testo della risposta viene trasformato in audio dal servizio di sintesi vocale (TTS) e trasmesso all'applicazione client per la riproduzione

![diagram-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Il flusso del canale di riconoscimento vocale")

> [!NOTE]
> I passaggi di questa esercitazione non richiedono un servizio a pagamento. Come nuovo utente di Azure, potrai usare i crediti della sottoscrizione di valutazione gratuita di Azure e il livello gratuito del servizio di riconoscimento vocale per completare questa esercitazione.

Contenuto dell'esercitazione:
> [!div class="checklist"]
> * Creare nuove risorse di Azure
> * Compilare, testare e distribuire l'esempio Echo Bot in un servizio app di AzureBuild, test, and deploy the Echo Bot sample to an Azure App Service
> * Registrare il bot con il canale Direct Line Speech
> * Creare ed eseguire il client Windows Voice Assistant per interagire con il bot Echo
> * Aggiungere l'attivazione di parole chiave personalizzate
> * Imparare a cambiare la lingua del discorso riconosciuto e parlato

## <a name="prerequisites"></a>Prerequisiti

Ecco cosa ti serve per completare questa esercitazione:

- Un PC Windows 10 con microfono e altoparlanti funzionanti (o cuffie)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) o versione successiva
- [.NET Core SDK](https://dotnet.microsoft.com/download) versione 2.1 o successiva
- Un account Azure. [Iscriviti gratuitamente](https://azure.microsoft.com/free/ai/).
- Un account [GitHub](https://github.com/)
- [Git per Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

L'app client che verrà creata in questa esercitazione usa alcuni servizi di Azure.The client app that you'll create in this tutorial uses a handful of Azure services. Per ridurre il tempo di andata e ritorno per le risposte dal bot, è consigliabile assicurarsi che questi servizi si trovino nella stessa area di Azure.To reduce the round-trip time for responses from your bot, you'll want to make sure that these services are located in the same Azure region. In questa sezione verrà creato un gruppo di risorse nell'area **Stati Uniti occidentali.** Questo gruppo di risorse verrà usato durante la creazione di singole risorse per Bot-Framework, il canale di riconoscimento vocale diretto e il servizio di riconoscimento vocale.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Creare un gruppo di risorseCreate a resource group<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. Verrà richiesto di fornire alcune informazioni:
   * Impostare **Abbonamento** su **Versione di prova gratuita** (è anche possibile usare un abbonamento esistente).
   * Immettere un nome per il **gruppo di risorse**. È consigliabile **SpeechEchoBotTutorial-ResourceGroup**.
   * Nell'elenco a discesa **Regione** selezionare **Stati uniti occidentali**.
1. Fare clic su **Rivedi e crea**. Verrà visualizzato un banner che legge **Convalida superata**.
1. Fare clic su **Crea**. La creazione del gruppo di risorse potrebbe richiedere alcuni minuti.
1. Come con le risorse che verranno create più avanti in questa esercitazione, è consigliabile aggiungere questo gruppo di risorse al dashboard per un facile accesso. Se si vuole aggiungere questo gruppo di risorse, fare clic sull'icona a forma di puntina nell'angolo superiore destro del dashboard.

### <a name="choosing-an-azure-region"></a>Scelta di un'area di AzureChoosing an Azure region

Se si desidera utilizzare un'area diversa per questo tutorial, questi fattori possono limitare le scelte:

* Assicurarsi di usare un'area di [Azure supportata.](regions.md#voice-assistants)
* Il canale Direct Line Speech usa il servizio di sintesi vocale, che ha voci standard e neurali. Le voci neurali sono [limitate a aree di Azure specifiche.](regions.md#standard-and-neural-voices)
* Le chiavi di prova gratuite possono essere limitate a un'area specifica.

Per altre informazioni sulle aree, vedere Percorsi di Azure.For more information about [regions,](https://azure.microsoft.com/global-infrastructure/locations/)see Azure locations .

## <a name="create-resources"></a>Creare le risorse

Ora che si dispone di un gruppo di risorse in un'area supportata, il passaggio successivo consiste nel creare singole risorse per ogni servizio che verrà usato in questa esercitazione.

### <a name="create-a-speech-service-resource"></a>Creare una risorsa del servizio di riconoscimento vocaleCreate a Speech service resource

Seguire queste istruzioni per creare una risorsa di riconoscimento vocale:Follow these instructions to create a Speech resource:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Creare una risorsa del servizio di riconoscimento vocaleCreate a Speech service resource<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Verrà richiesto di fornire alcune informazioni:
   * Assegnare un **nome**alla risorsa . Si consiglia **SpeechEchoBotTutorial-Speech**
   * Per **Abbonamento**, assicurarsi che **versione di prova gratuita** sia selezionata.
   * Per **Posizione**, selezionare **Stati Uniti occidentali**.
   * Per **Il piano tariffario**, selezionare **F0**. Questo è il livello gratuito.
   * Per **Gruppo di risorse**selezionare **SpeechEchoBotTutorial-ResourceGroup**.
5. Dopo aver immesso tutte le informazioni necessarie, fare clic su **Crea**. La creazione della risorsa potrebbe richiedere alcuni minuti.
6. Più avanti in questa esercitazione sono necessarie chiavi di sottoscrizione per questo servizio. È possibile accedere a queste chiavi in qualsiasi momento da **Panoramica** della risorsa (Gestisci chiavi) o **Chiavi**.

A questo punto, verificare che il gruppo di risorse ( SpeechEchoBotTutorial-ResourceGroup ) disponga di una risorsa di riconoscimento vocale:At this point, check that your resource group (**SpeechEchoBotTutorial-ResourceGroup**) has a Speech resource:

| Nome | Type  | Location |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Servizi cognitivi | Stati Uniti occidentali |

### <a name="create-an-azure-app-service-plan"></a>Creare un piano di servizio app di Azure

Il passaggio successivo consiste nel creare un piano di servizio app. Un piano di servizio app definisce un set di risorse di calcolo per l'esecuzione di un'app Web.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Creare un piano di servizio app di AzureCreate an Azure App Service plan<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Verrà richiesto di fornire alcune informazioni:
   * Impostare **Abbonamento** su **Versione di prova gratuita** (è anche possibile usare un abbonamento esistente).
   * Per **Gruppo di risorse**selezionare **SpeechEchoBotTutorial-ResourceGroup**.
   * Assegnare un **nome**alla risorsa . Consiglia **SpeechEchoBotTutorial-AppServicePlan**
   * Per **Sistema operativo**, selezionare **Windows**.
   * Per **Regione**, selezionare **Stati Uniti occidentali**.
   * Per **Livello di prezzo**, assicurarsi che Standard **S1** sia selezionato. Questo dovrebbe essere il valore predefinito. In caso contrario, assicurati di impostare il **sistema operativo su** **Windows** come descritto in precedenza.
5. Fare clic su **Rivedi e crea**. Verrà visualizzato un banner che legge **Convalida superata**.
6. Fare clic su **Crea**. La creazione del gruppo di risorse potrebbe richiedere alcuni minuti.

A questo punto, verificare che il gruppo di risorse ( SpeechEchoBotTutorial-ResourceGroup ) disponga di due risorse:At this point, check that your resource group (**SpeechEchoBotTutorial-ResourceGroup**) has two resources:

| Nome | Type  | Location |
|------|-------|----------|
| DiscorsoEchoBotTutorial-AppServicePlan | Piano di servizio app | Stati Uniti occidentali |
| SpeechEchoBotTutorial-Speech | Servizi cognitivi | Stati Uniti occidentali |

## <a name="build-an-echo-bot"></a>Costruisci un bot Echo

Dopo aver creato alcune risorse, è possibile creare un bot. Inizieremo con l'esempio Echo Bot, che, come suggerisce il nome, riecheggia il testo immesso come risposta. Non preoccuparti, il codice di esempio è pronto per l'uso senza alcuna modifica. È configurato per funzionare con il canale Direct Line Speech, che ci si connetterà dopo aver distribuito il bot in Azure.It's configured to work with the Direct Line Speech channel, which we'll connect after we've deployed the bot to Azure.

> [!NOTE]
> Le istruzioni che seguono, nonché informazioni aggiuntive sul bot Echo sono disponibili nel [README dell'esempio su GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Eseguire l'esempio di bot nel computerRun the bot sample on your machine

1. Clonare il repository degli esempi:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Avviare Visual Studio.
3. Dalla barra degli strumenti, selezionare Apri**Open** > **progetto/soluzione** **File** > e aprire la soluzione del progetto Echo Bot:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Dopo aver caricato il progetto, premere <kbd>F5</kbd> per compilare ed eseguire il progetto.
5. Un browser dovrebbe essere avviato e vedrai una schermata simile a questa.
    > [!div class="mx-imgBorder"]
    > [![echobot-running-on-localhost](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot in esecuzione su localhost")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Testare l'esempio di bot con l'emulatore bot FrameworkTest the bot sample with the Bot Framework Emulator

[L'emulatore di Framework](https://github.com/microsoft/botframework-emulator) bot è un'app desktop che consente agli sviluppatori di bot di testare ed eseguire il debug dei bot in locale o in remoto tramite un tunnel. L'emulatore supporta il testo digitato come input (non voce). Il bot risponderà con il testo. Seguire questi passaggi per usare l'emulatore di framework bot per testare il bot Echo in esecuzione in locale, con input di testo e output di testo. Dopo aver distribuito il bot in Azure, lo testeremo con l'input vocale e l'output vocale.

1. Installare [l'emulatore di Bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) versione 4.3.0 o successiva
2. Avviare l'emulatore di Bot Framework e aprire il bot:
   * **File** -> **Apri bot**.
3. Immettere l'URL per il bot. Ad esempio:

   ```
   http://localhost:3978/api/messages
   ```
   e premere "Connetti".
4. Il bot dovrebbe salutarti immediatamente con "Ciao e benvenuto!" criteri.). Digitare un messaggio di testo e confermare di ricevere una risposta dal bot.
5. Questo è ciò che uno scambio di comunicazione con un'istanza Echo Bot potrebbe essere simile: [ ![bot-framework-emulatore](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Emulatore di Bot Framework")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Distribuire il bot in un servizio app di AzureDeploy your bot to an Azure App Service

The next step is to deploy the Echo Bot to Azure. Esistono alcuni modi per distribuire un bot, ma in questa esercitazione ci concentreremo sulla pubblicazione direttamente da Visual Studio.There are a few ways to deploy a bot, but in this tutorial we'll focus on publishing directly from Visual Studio.

> [!NOTE]
> In alternativa, è possibile distribuire un bot usando [l'interfaccia della riga di comando](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) di Azure e i modelli di [distribuzione.](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)

1. Da Visual Studio, aprire il bot Echo configurato per l'utilizzo con il canale Direct Line Speech:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto **EchoBot** e **scegliere Pubblica...**
1. Si aprirà una nuova finestra denominata Scegliere una destinazione di **pubblicazione.**
1. Selezionare **Servizio app** nella struttura di spostamento dei servizi di **Azure,** selezionare **Crea nuovo**, quindi fare clic su Crea **profilo**.
1. Quando viene visualizzata la finestra **Crea servizio app:When** the Create App Service window appears:
   * Fare clic su **Aggiungi un account**e accedere con le credenziali dell'account Azure.Click Add an account , and sign in with your Azure account credentials. Se è già stato effettuato l'accesso, scegliere l'account da usare nell'elenco a discesa.
   * Per il **nome dell'app**, è necessario immettere un nome univoco globale per il bot. Questo nome viene usato per creare un URL univoco del bot. Verrà popolato un valore predefinito, inclusa la data e l'ora (ad esempio: "EchoBot20190805125647"). È possibile usare il nome predefinito per questa esercitazione.
   * Per **abbonamento**, impostarlo su **Versione di prova gratuita**
   * Per **Gruppo di risorse**selezionare **SpeechEchoBotTutorial-ResourceGroupFor** Resource Group , select SpeechEchoBotTutorial-ResourceGroup
   * Per **Piano di hosting**, selezionare **SpeechEchoBotTutorial-AppServicePlan**
   * Per **Application Insights**, lasciare come **Nessuno**
1. Fare clic su **Crea**
1. Verrà visualizzato un messaggio di operazione riuscita in Visual Studio simile al seguente:You should see a success message in Visual Studio that looks like this:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Il browser predefinito dovrebbe aprire e visualizzare una pagina con: "Il bot è pronto!".
1. A questo punto, controllare il gruppo di risorse SpeechEchoBotTutorial-ResourceGroup nel portale di Azure e verificare che siano presenti tre risorse:At this point, check your Resource Group **SpeechEchoBotTutorial-ResourceGroup** in the Azure portal, and confirm there are three resources:

| Nome | Type  | Location |
|------|-------|----------|
| EchoBot20190805125647 | Servizio app | Stati Uniti occidentali |
| DiscorsoEchoBotTutorial-AppServicePlan | Piano di servizio app | Stati Uniti occidentali |
| SpeechEchoBotTutorial-Speech | Servizi cognitivi | Stati Uniti occidentali |

## <a name="enable-web-sockets"></a>Abilitare i WebSocket

È necessario apportare una piccola modifica alla configurazione in modo che il bot possa comunicare con il canale Direct Line Speech usando i socket Web. Attenersi alla seguente procedura per abilitare i socket Web:

1. Passare al [portale di Azure](https://portal.azure.com)e individuare il servizio app. La risorsa deve essere denominata in modo simile a **EchoBot20190805125647** (nome univoco dell'app).
2. Nell'area di spostamento dei servizi di **Azure,** in **Impostazioni**, fare clic su **Configurazione**.
3. Selezionare la scheda **Impostazioni generali.**
4. Individuare l'interruttore per **i socket Web** e impostarlo su **On**.
5. Fare clic su **Salva**.

> [!TIP]
> È possibile usare i controlli nella parte superiore della pagina del servizio app di Azure per arrestare o riavviare il servizio. Questo può risultare utile durante la risoluzione dei problemi.

## <a name="create-a-channel-registration"></a>Creare una registrazione del canale

Dopo aver creato un servizio app di Azure per ospitare il bot, il passaggio successivo consiste nel creare una registrazione dei **canali bot.** La creazione di una registrazione del canale è un prerequisito per la registrazione del bot con i canali Bot-Framework, incluso il canale Direct Line Speech.

> [!NOTE]
> Per altre informazioni su come i bot sfruttano i canali, vedere [Connettere un bot ai canali](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Creare una registrazione dei canali di bot di AzureCreate an Azure Bot Channels Registration<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. Verrà richiesto di fornire alcune informazioni:
   * Per **l'handle Bot**, immettere **SpeechEchoBotTutorial-BotRegistration**.
   * Per **Abbonamento**, selezionare **Versione di prova gratuita**.
   * Per **Gruppo di risorse**selezionare **SpeechEchoBotTutorial-ResourceGroup**.
   * Per **Posizione**, selezionare **Stati Uniti occidentali**.
     * Per **Il piano tariffario**, selezionare **F0**.
     * Per **Endpoint di messaggistica**immettere l'URL dell'app Web con il `/api/messages` percorso aggiunto alla fine. Ad esempio: se il nome dell'app univoco a livello globale è **EchoBot20190805125647**, l'endpoint di messaggistica sarà: `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * Per **Application Insights**, è possibile impostare questa opzione su **Off**. Per ulteriori informazioni, vedere [Bot analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignora **ID app e password per la creazione automatica**.
5. Nella parte inferiore del pannello **Registrazione canali bot** fare clic su **Crea**.

A questo punto, controllare il gruppo di risorse SpeechEchoBotTutorial-ResourceGroup nel portale di Azure.At this point, check your Resource Group **SpeechEchoBotTutorial-ResourceGroup** in the Azure portal. Dovrebbe ora mostrare quattro risorse:

| Nome | Type  | Location |
|------|-------|----------|
| EchoBot20190805125647 | Servizio app | Stati Uniti occidentali |
| DiscorsoEchoBotTutorial-AppServicePlan | Piano di servizio app | Stati Uniti occidentali |
| SpeechEchoBotTutorial-BotRegistration | Registrazione dei canali bot | global |
| SpeechEchoBotTutorial-Speech | Servizi cognitivi | Stati Uniti occidentali |

> [!IMPORTANT]
> La risorsa Bot Channels Registration mostrerà l'area globale anche se è stata selezionata l'opzione Stati Uniti occidentali. Si tratta di un comportamento previsto.

## <a name="register-the-direct-line-speech-channel"></a>Registrare il canale Direct Line Speech

Ora è il momento di registrare il bot con il canale Direct Line Speech. Questo canale è quello che viene usato per creare una connessione tra il bot echo e un'app client compilata con Speech SDK.

1. Individuare e aprire la risorsa **SpeechEchoBotTutorial-BotRegistration** nel portale di [Azure.](https://portal.azure.com)
1. Nell'area di spostamento dei servizi di **Azure** selezionare **Canali**.
   * Cercare **Altri canali**, individuare e fare clic su **Direct Line Speech**.
   * Esaminare il testo nella pagina intitolato **Configure Direct line Speech**, quindi espandere il menu a discesa denominato "Account del servizio cognitivo".
   * Selezionare la risorsa di riconoscimento vocale creata in precedenza (ad **esempio, SpeechEchoBotTutorial-Speech**) dal menu per associare il bot al tasto di sottoscrizione vocale.
   * Fare clic su **Salva**.

1. Nella barra di spostamento **Gestione bot** fare clic su **Impostazioni**.
   * Selezionare la casella **Abilita endpoint di streaming**. Ciò è necessario per abilitare un protocollo di comunicazione basato su socket web tra il bot e il canale Direct Line Speech.
   * Fare clic su **Salva**.

> [!TIP]
> Per altre informazioni, vedere [Connettere un bot a Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Questa pagina include informazioni aggiuntive e problemi noti.

## <a name="build-the-windows-voice-assistant-client"></a>Creare il client Di Assistente vocale di Windows

In questo passaggio si intende creare il client Di Assistente vocale di Windows. Il client è un'app di Windows Presentation Foundation (WPF) in C, che usa l'SDK di [riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) per gestire la comunicazione con il bot usando il canale di riconoscimento vocale Direct Line. Usalo per interagire con il bot e testarlo prima di scrivere un'app client personalizzata.

Il client Di Windows Voice Assistant dispone di una semplice interfaccia utente che consente di configurare la connessione al bot, visualizzare la conversazione di testo, visualizzare le attività bot-Framework in formato JSON e visualizzare schede adattive. Supporta anche l'uso di parole chiave personalizzate. Questo client verrà usato per parlare con il bot e ricevere una risposta vocale.

Prima di andare avanti, assicurati che il microfono e gli altoparlanti siano abilitati e funzionanti.

1. Passare al repository GitHub per il client [Assistente vocale Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md).
2. Seguire le istruzioni fornite per clonare il repository, compilare il progetto, configurare il client e avviare il client.
3. Fare clic su **Riconnetti** e assicurarsi di visualizzare il messaggio **Premere il pulsante del microfono o digitare per iniziare a parlare con il bot**.
4. Mettiamolo alla prova. Fare clic sul pulsante del microfono e pronunciare alcune parole in inglese. Il testo riconosciuto verrà visualizzato mentre si parla. Quando hai finito di parlare, il bot risponderà con la propria voce, dicendo "eco" seguito dalle parole riconosciute.
5. È anche possibile usare il testo per comunicare con il bot. Basta digitare il testo nella barra inferiore. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Risoluzione degli errori nel client Di Windows Voice Assistant

Se viene visualizzato un messaggio di errore nella finestra principale dell'app, usa questa tabella per identificare e risolvere l'errore:

| Errore | Come si deve procedere? |
|-------|----------------------|
|Error AuthenticationFailure: Aggiornamento WebSocket non riuscito con un errore di autenticazione (401). Verificare la corretta chiave di sottoscrizione (o token di autorizzazione) e il nome dell'area di archiviazioneCheck for correct subscription key (or authorization token) and region name| Nella pagina Impostazioni dell'app, assicurati di aver inserito correttamente la chiave di sottoscrizione vocale e la relativa area geografica.<br>Assicurarsi che la chiave vocale e l'area chiave siano state immesse correttamente. |
|Errore ConnectionFailure: connessione chiusa dall'host remoto. Codice di errore: 1011. Dettagli errore: Impossibile connettersi al bot prima di inviare un messaggio | Assicurati di aver selezionato la casella ["Abilita endpoint di streaming"](#register-the-direct-line-speech-channel) e/o i [socket **Web** abilitati](#enable-web-sockets) su Attivato.<br>Assicurarsi che il servizio app di Azure sia in esecuzione. In caso affermativo, provare a riavviare il servizio app.|
|Errore ConnectionFailure: connessione chiusa dall'host remoto. Codice di errore: 1011. Dettagli errore: Il codice di stato della risposta non indica l'esito positivo: 500 (InternalServerError)| Il bot ha specificato una voce neurale nel campo [Di scorso](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) attività di output, ma l'area di Azure associata alla chiave di sottoscrizione Riconoscimento vocale non supporta le voci neurali. Consultate [Voci standard e neurali.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)|
|Errore ConnectionFailure: connessione chiusa dall'host remoto. Codice di errore: 1000. Dettagli errore: superato la durata massima di inattività della connessione socket Web (> 300000 ms)| Si tratta di un errore previsto quando una connessione al canale viene lasciata aperta e inattiva per più di cinque minuti. |

Se il problema non viene risolto nella tabella, vedere [Assistenti vocali: domande frequenti](faq-voice-assistants.md).

### <a name="view-bot-activities"></a>Visualizzare le attività del bot

Ogni bot invia e riceve messaggi **di attività.** Nella finestra **Registro attività** del client Di Assistente vocale di Windows verranno visualizzati i log con timestamp con ogni attività ricevuta dal client dal bot. È inoltre possibile visualizzare le attività che il [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) client ha inviato al bot usando il metodo. Quando si seleziona un elemento di log, verranno visualizzati i dettagli dell'attività associata come JSON.

Ecco un json di esempio di un'attività ricevuta dal client:Here's a sample json of an Activity the client received:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Per altre informazioni sugli elementi restituiti nell'output JSON, vedere [campi nella finestra di dialogo Attività](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Ai fini di questa esercitazione, è possibile concentrarsi sui campi [Testo](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) e [Pronuncia.](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Visualizzare il codice sorgente del client per le chiamate a Speech SDKView client source code for calls to the Speech SDK

Il client Windows Voice Assistant utilizza il pacchetto [NuGet Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), che contiene Speech SDK. Un buon punto di partenza per iniziare a esaminare il [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)codice di esempio è il metodo InitSpeechConnector() nel file , che crea questi due oggetti Speech SDK:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)- Per le impostazioni di configurazione (ad esempio, chiave di sottoscrizione vocale, area chiave)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)- Per gestire la connessione del canale e gli eventi di sottoscrizione client per la gestione delle risposte di riconoscimento vocale e bot.

## <a name="add-custom-keyword-activation"></a>Aggiungere l'attivazione di parole chiave personalizzate

Speech SDK supporta l'attivazione di parole chiave personalizzate. Simile a "Ehi Cortana" per l'Assistente di Microsoft, puoi scrivere un'app che ascolterà continuamente una parola chiave di tua scelta. Tieni presente che una parola chiave può essere una singola parola o una frase di più parole.

> [!NOTE]
> Il termine *parola chiave* viene spesso utilizzato in modo intercambiabile con il termine *wake word*e sono possibile visualizzare entrambi utilizzati nella documentazione Microsoft.

Il rilevamento delle parole chiave viene eseguito nell'app client. Se si utilizza una parola chiave, l'audio viene trasmesso al canale Direct Line Speech solo se la parola chiave viene rilevata. Il canale Direct Line Speech include un componente denominato *verifica delle parole chiave (KWV),* che esegue un'elaborazione più complessa nel cloud per verificare che la parola chiave scelta si trovi all'inizio del flusso audio. Se la verifica delle parole chiave ha esito positivo, il canale comunicherà con il bot.

Seguire questi passaggi per creare un modello di parole chiave, configurare il client Windows Voice Assistant per usare questo modello e infine testarlo con il bot.

1. Seguire queste istruzioni per [creare una parola chiave personalizzata utilizzando il servizio di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Decomprimere il file di modello scaricato nel passaggio precedente. Deve essere denominato per la parola chiave. Stai cercando un file `kws.table`di nome .
3. Nel client Di Windows Voice Assistant, individua il menu **Impostazioni** (cerca l'icona a forma di ingranaggio in alto a destra). Individuare **Percorso file modello** e immettere `kws.table` il nome del percorso completo del file dal passaggio 2.
4. Assicurarsi di selezionare la casella **Abilitato**. Questo messaggio dovrebbe essere visualizzato accanto alla casella di controllo: "Ascolterà la parola chiave alla connessione successiva". Se è stato fornito il file errato o un percorso non valido, verrà visualizzato un messaggio di errore.
5. Immettere la **chiave**di sottoscrizione vocale , l'area della **chiave**di sottoscrizione e quindi fare clic **su OK** per chiudere il menu **Impostazioni.**
6. Fare clic su **Riconnetti**. Dovresti vedere un messaggio con la legge: "Nuova conversazione avviata - digita, premi il pulsante del microfono o pronuncia la parola chiave". L'app è ora in ascolto continuo.
7. Pronunciare qualsiasi frase che inizia con la parola chiave. Ad esempio: "**"la tua parola chiave"**, che ora è?". Non è necessario mettere in pausa dopo aver pronunciato la parola chiave. Al termine, accadono due cose:
   * Vedrai una trascrizione di ciò che hai parlato
   * Poco dopo, sentirai la risposta del bot
8. Continuare a sperimentare con i tre tipi di input supportati dal bot:
   * Testo digitato nella barra inferiore
   * Premendo l'icona del microfono e parlando
   * Pronunciare una frase che inizia con la parola chiave

### <a name="view-the-source-code-that-enables-keyword"></a>Visualizzare il codice sorgente che abilita la parola chiaveView the source code that enables keyword

Nel codice sorgente del client Windows Voice Assistant, esaminare questi file per esaminare il codice utilizzato per abilitare il rilevamento di parole chiave:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)include una chiamata al [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)metodo Speech SDK , che viene utilizzato per creare un'istanza del modello da un file locale su disco.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)include una chiamata al [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)metodo Speech SDK , che attiva il rilevamento continuo delle parole chiave.

## <a name="optional-change-the-language-and-bot-voice"></a>(Facoltativo) Modificare la lingua e la voce del bot

Il bot creato ascolterà e risponderà in inglese, con una voce di sintesi vocale in inglese americano predefinita. Tuttavia, non sei limitato all'uso dell'inglese o di una voce predefinita. In questa sezione verrà illustrato come modificare la lingua in cui il bot ascolterà e risponderà. Imparerai anche come selezionare una voce diversa per quella lingua.

### <a name="change-the-language"></a>Cambiare la lingua

È possibile scegliere una qualsiasi delle lingue menzionate nella tabella [di sintesi vocale.](language-support.md#speech-to-text) Nell'esempio seguente, cambieremo la lingua in tedesco.

1. Aprire l'app Windows Voice Assistant Client, fare clic sul pulsante `de-de` delle impostazioni (icona a forma di ingranaggio in alto a destra) e immettere nel campo Lingua (questo è il valore delle impostazioni locali indicato nella tabella di [sintesi vocale).](language-support.md#speech-to-text) In questo modo la lingua parlata `en-us`viene impostata per essere riconosciuta, sovrascrivendo il valore predefinito. Questo indica anche il canale Direct Line Speech per utilizzare una voce tedesca predefinita per la risposta Bot.
2. Chiudere la pagina delle impostazioni e fare clic sul pulsante Riconnetti per stabilire una nuova connessione al bot eco.
3. Fare clic sul pulsante del microfono e pronunciare una frase in tedesco. Vedrai il testo riconosciuto e il bot echo rispondere con la voce tedesca predefinita.

### <a name="change-the-default-bot-voice"></a>Modificare la voce predefinita del bot

La selezione della voce di sintesi vocale e il controllo della pronuncia possono essere effettuati se il bot specifica la risposta sotto forma di un linguaggio SSML [(Speech Synthesis Markup Language)](speech-synthesis-markup.md) anziché di un testo semplice. Il bot echo non usa SSML, ma possiamo facilmente modificare il codice per farlo. Nell'esempio seguente aggiungiamo SSML alla risposta bot eco, in modo che la voce tedesca Stefan Apollo (una voce maschile) verrà utilizzato al posto della voce femminile predefinita. Vedere l'elenco delle [voci standard](language-support.md#standard-voices) e [neurali](language-support.md#neural-voices) supportate per la propria lingua.

1. Iniziamo aprendo `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`.
2. Individuare queste due righe:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Sostituirli con:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Compilare la soluzione in Visual Studio e correggere eventuali errori di compilazione.

Il secondo argomento nel metodo 'MessageFactory.Text' imposta il [campo di pronuncia attività](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) nella risposta bot. Con la modifica di cui sopra, è stato sostituito da testo semplice a SSML al fine di specificare una voce tedesca non predefinita.

### <a name="redeploy-your-bot"></a>Ridistribuire il bot

Dopo aver apportato la modifica necessaria al bot, il passaggio successivo consiste nel ripubblicarlo nel servizio app di Azure e provarlo:Now that you've made the necessary change to the bot, the next step is to republish it to your Azure App Service and try it out:

1. Nella finestra Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **EchoBot** e scegliere **Pubblica**.
2. La configurazione di distribuzione precedente è già stata caricata come predefinita. È sufficiente fare clic su **Pubblica** accanto a **EchoBot20190805125647 - Distribuzione Web**.
3. Il messaggio **Pubblica completata** verrà visualizzato nella finestra di output di Visual Studio e verrà avviata una pagina Web con il messaggio "Il bot è pronto!".
4. Apri l'app Windows Voice Assistant Client, fai clic sul pulsante delle impostazioni `de-de` (icona a forma di ingranaggio in alto a destra) e assicurati di avere ancora nel campo Lingua.
5. Seguire le istruzioni in [Creare il client di Assistente vocale di Windows](#build-the-windows-voice-assistant-client) per riconnettersi al bot appena distribuito, parlare nella nuova lingua e sentire la risposta del bot in tale lingua con la nuova voce.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'echo-bot distribuito in questa esercitazione, è possibile rimuoverlo e tutte le risorse di Azure associate semplicemente eliminando il gruppo di risorse di Azure **SpeechEchoBotTutorial-ResourceGroup**.

1. Nel portale di Azure fare clic su Gruppi di risorse nella struttura di spostamento dei servizi di Azure.From the [Azure portal,](https://portal.azure.com)click on **Resource Groups** from the Azure **services** navigation.
2. Trovare il gruppo di risorse denominato: **SpeechEchoBotTutorial-ResourceGroup**. Fare clic sui tre puntini (...).
3. Selezionare **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Crea la tua app client con Speech SDK](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Vedere anche

* Distribuzione in [un'area](https://azure.microsoft.com/global-infrastructure/locations/) di Azure vicino all'utente per visualizzare il miglioramento dei tempi di risposta dei botDeploying to an Azure region near you to see bot response time improvement
* Distribuzione in un'area di Azure che supporta voci di toTS neurale di alta qualitàDeploying to an [Azure region that supports high quality Neural TTS voices](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Prezzi associati al canale Direct Line Speech:
  * [Prezzi del servizio bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Servizio di riconoscimento vocale](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Creazione e distribuzione di bot abilitati alla voce:
  * Compilare un [bot Bot-Framework](https://dev.botframework.com/). Registralo con [il canale Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) e personalizza il bot per la [voce](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Esplora [le soluzioni Bot-Framework](https://microsoft.github.io/botframework-solutions/index)esistenti: crea un [assistente virtuale](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) ed [estendilo a Direct Line Speech](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
