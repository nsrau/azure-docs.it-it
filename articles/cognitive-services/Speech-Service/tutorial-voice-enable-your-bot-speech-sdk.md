---
title: "Esercitazione: Voices Abilita il bot con l'SDK vocale"
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si creerà un bot Echo con Microsoft bot-Framework, lo si distribuirà in Azure e lo si registrerà con il canale di sintesi vocale della linea bot-Framework. Si configurerà quindi un'app client di esempio per Windows che consente di comunicare con il bot e ascoltare la risposta.
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: dcohen
ms.openlocfilehash: 3c57200591f3b7de9a1f9ab4198e55ed844b4d07
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932062"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Esercitazione: Abilitare il bot tramite l'SDK vocale

È ora possibile usare la potenza dei servizi di riconoscimento vocale per abilitare facilmente una chat bot.

In questa esercitazione si creerà un bot Echo con Microsoft bot-Framework, lo si distribuirà in Azure e lo si registrerà con il canale di sintesi vocale della linea bot-Framework. Si configurerà quindi un'app client di esempio per Windows che consente di comunicare con il bot e ascoltare la risposta.

Questa esercitazione è progettata per gli sviluppatori che stanno iniziando a usare Azure, bot-Framework bot, direct line Speech o Speech SDK e vogliono creare rapidamente un sistema funzionante con codice limitato. Non è necessaria alcuna esperienza o familiarità con questi servizi.

Al termine di questo esercizio, sarà possibile configurare un sistema che funzioni come segue:

1. L'applicazione client di esempio è configurata per la connessione al canale vocale diretto e al bot Echo
2. Audio viene registrato dal microfono predefinito sulla pressione del pulsante (o registrato in modo continuo se è attivata la parola di riattivazione personalizzata)
3. Facoltativamente, viene eseguito il rilevamento di una parola di riattivazione personalizzata, che esegue il controllo dello streaming audio
4. Con l'SDK di riconoscimento vocale, l'app si connette al canale vocale diretto e trasmette i flussi audio
5. Facoltativamente, viene eseguita la verifica della parola di riattivazione con maggiore precisione nel servizio
6. L'audio viene passato al servizio riconoscimento vocale e trascritto in testo
7. Il testo riconosciuto viene passato a Echo-bot come attività di bot Framework 
8. Il testo della risposta viene trasformato in audio dal servizio di sintesi vocale (TTS) e trasmesso nuovamente all'applicazione client per la riproduzione

![diagramma-Tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Flusso del canale vocale")

> [!NOTE]
> Per la procedura descritta in questa esercitazione non è necessario un servizio a pagamento. Un nuovo utente di Azure potrà usare i crediti della sottoscrizione gratuita di Azure Trail e il livello gratuito di servizi di riconoscimento vocale per completare questa esercitazione.

Contenuto dell'esercitazione:
> [!div class="checklist"]
> * Creare nuove risorse di Azure
> * Compilare, testare e distribuire l'esempio Echo bot in un servizio app Azure
> * Registrare il bot con il canale di sintesi vocale direct line
> * Compila ed Esegui il client di sintesi vocale diretta per interagire con il bot Echo
> * Aggiunta dell'attivazione di una parola di riattivazione personalizzata
> * Informazioni su come modificare la lingua della voce riconosciuta e pronunciata

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario eseguire le operazioni seguenti:

- Un PC Windows 10 con un microfono funzionante e altoparlanti (o cuffie)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) o versione successiva
- [.NET Core SDK](https://dotnet.microsoft.com/download) versione 2,1 o successiva
- Un account Azure. [Iscriviti](https://azure.microsoft.com/free/ai/)gratuitamente.
- Un account [GitHub](https://github.com/)
- [Git per Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

L'app client che verrà creata in questa esercitazione usa alcuni servizi di Azure. Per ridurre il tempo di round trip per le risposte dal bot, è necessario assicurarsi che questi servizi si trovino nella stessa area di Azure. In questa sezione verrà creato un gruppo di risorse nell'area **Stati Uniti occidentali** . Questo gruppo di risorse verrà usato durante la creazione di singole risorse per bot-Framework, il canale di riconoscimento vocale direct line e i servizi di riconoscimento vocale.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel percorso di spostamento a sinistra selezionare **gruppi di risorse**. Fare quindi clic su **Aggiungi** per aggiungere un nuovo gruppo di risorse.
3. Verrà richiesto di fornire alcune informazioni:
   * Impostare la **sottoscrizione** per la **versione di valutazione gratuita** . è anche possibile usare una sottoscrizione esistente.
   * Immettere un nome per il **gruppo di risorse**. Si consiglia **SpeechEchoBotTutorial-ResourceGroup**.
   * Dall'elenco a discesa **Region** selezionare **Stati Uniti occidentali**.
4. Fare clic su **Verifica e crea**. Verrà visualizzato un banner che ha **superato la convalida**di lettura.
5. Fare clic su **Create**(Crea). La creazione del gruppo di risorse potrebbe richiedere alcuni minuti.
6. Come per le risorse che verranno create più avanti in questa esercitazione, è consigliabile aggiungere questo gruppo di risorse al dashboard per semplificare l'accesso. Se si vuole aggiungere questo gruppo di risorse, fare clic sull'icona Aggiungi in alto a destra nel dashboard.

### <a name="choosing-an-azure-region"></a>Scelta di un'area di Azure

Se si vuole usare un'area diversa per questa esercitazione, questi fattori possono limitare le scelte:

* Il canale vocale direct line è un servizio in anteprima. Di conseguenza, può essere limitato a specifiche aree di Azure. Per altre informazioni sulle aree disponibili, vedere la pagina relativa agli assistenti virtuali per la [prima voce](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#voice-first-virtual-assistants).
* Il canale di riconoscimento vocale della linea diretta usa il servizio di sintesi vocale, che include voci standard e neurali. Le voci neurali sono [limitate a specifiche aree di Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
* Le chiavi di valutazione gratuite possono essere limitate a un'area specifica.

Per altre informazioni sulle aree, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Crea risorse

Ora che si dispone di un gruppo di risorse nell'area **Stati Uniti occidentali** , il passaggio successivo consiste nel creare singole risorse per ogni servizio che verrà usato in questa esercitazione.

### <a name="create-a-speech-services-resource"></a>Creare una risorsa di servizi vocali

Seguire queste istruzioni per creare una risorsa vocale:

1. Selezionare **Crea una risorsa** nel percorso di spostamento a sinistra.
2. Nella barra di ricerca digitare **Speech**.
3. Selezionare **sintesi vocale**, quindi fare clic su **Crea**.
4. Verrà richiesto di fornire alcune informazioni:
   * Assegnare un **nome**alla risorsa. Si consiglia **di SpeechEchoBotTutorial-Speech**
   * Per **Subscription (sottoscrizione**), verificare che sia selezionata la **versione di valutazione gratuita** .
   * Per **località**selezionare **Stati Uniti occidentali**.
   * Perpiano tariffario selezionare **F0**. Questo è il livello gratuito.
   * Per **gruppo di risorse**selezionare **SpeechEchoBotTutorial-ResourceGroup**.
5. Dopo aver immesso tutte le informazioni necessarie, fare clic su **Crea**. La creazione della risorsa potrebbe richiedere alcuni minuti.
6. Più avanti in questa esercitazione sono necessarie le chiavi di sottoscrizione per questo servizio. È possibile accedere a queste chiavi in qualsiasi momento dalla **Panoramica** della risorsa (Gestisci chiavi) o dalle **chiavi**.

A questo punto, verificare che il gruppo di risorse (**SpeechEchoBotTutorial-ResourceGroup**) disponga di una risorsa vocale:

| NOME | TIPO  | PERCORSO |
|------|-------|----------|
| SpeechEchoBotTutorial-sintesi vocale | Servizi cognitivi | Stati Uniti occidentali |

### <a name="create-an-azure-app-service-plan"></a>Creare un piano di servizio app di Azure

Il passaggio successivo consiste nel creare un piano di servizio app. Un piano di servizio app definisce un set di risorse di calcolo per l'esecuzione di un'app Web.

1. Selezionare **Crea una risorsa** nel percorso di spostamento a sinistra.
2. Nella barra di ricerca digitare **piano di servizio app**. Individuare e selezionare la scheda del **piano di servizio app** nei risultati della ricerca.
3. Fare clic su **Create**(Crea).
4. Verrà richiesto di fornire alcune informazioni:
   * Impostare la **sottoscrizione** per la **versione di valutazione gratuita** . è anche possibile usare una sottoscrizione esistente.
   * Per **gruppo di risorse**selezionare **SpeechEchoBotTutorial-ResourceGroup**.
   * Assegnare un **nome**alla risorsa. Si consiglia **di SpeechEchoBotTutorial-AppServicePlan**
   * Per **sistema operativo**selezionare **Windows**.
   * Per **area**selezionare **Stati Uniti occidentali**.
   * Perpiano tariffario, assicurarsi che sia selezionata l'opzione **standard S1** . Deve corrispondere al valore predefinito.
5. Fare clic su **Verifica e crea**. Verrà visualizzato un banner che ha **superato la convalida**di lettura.
6. Fare clic su **Create**(Crea). La creazione del gruppo di risorse potrebbe richiedere alcuni minuti.

A questo punto, verificare che il gruppo di risorse (**SpeechEchoBotTutorial-ResourceGroup**) disponga di due risorse:

| NOME | TIPO  | PERCORSO |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Piano di servizio app | Stati Uniti occidentali |
| SpeechEchoBotTutorial-sintesi vocale | Servizi cognitivi | Stati Uniti occidentali |

## <a name="build-an-echo-bot"></a>Creare un bot Echo

Ora che sono state create alcune risorse, è possibile creare un bot. Si inizierà con l'esempio Echo bot, che come suggerisce il nome, viene visualizzato il testo immesso come risposta. Il codice di esempio è pronto per l'uso senza alcuna modifica. È configurato per funzionare con il canale di riconoscimento vocale diretto, che verrà connesso dopo aver distribuito il bot in Azure.

> [!NOTE]
> Le istruzioni seguenti, oltre a informazioni aggiuntive su Echo bot sono disponibili nel [file Leggimi dell'esempio in GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="download-and-run-the-sample"></a>Scaricare ed eseguire l'esempio

1. Clonare il repository degli esempi:
   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```
2. Avviare Visual Studio.
3. Dalla barra degli strumenti selezionare **file** > **Apri** > **progetto/soluzione**e aprire il file di progetto del bot echo che è stato configurato per l'uso con il canale vocale direct line:
   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```
4. Una volta caricato il progetto, premere `F5` per eseguire il progetto.

### <a name="test-with-the-bot-framework-emulator"></a>Eseguire il test con l'emulatore di bot Framework

L' [emulatore di bot Framework](https://github.com/microsoft/botframework-emulator) è un'applicazione desktop che consente agli sviluppatori di bot di testare ed eseguire il debug dei bot in locale o in remoto tramite un tunnel. Seguire questa procedura per usare l'emulatore di bot Framework per testare il bot Echo.

1. Installare la versione 4.3.0 o successiva dell'emulatore di [bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases/latest)
2. Avviare l'emulatore di bot Framework e aprire il bot:
   * File -> **Open bot**.
3. Immettere l'URL per il bot. Ad esempio:
   ```
   http://localhost:3978/api/messages
   ```
4. Usare l'interfaccia utente per comunicare con il bot usando il testo digitato. Confermare di ricevere una risposta.


## <a name="deploy-your-bot-to-an-azure-app-service"></a>Distribuire il bot a un servizio app Azure

Il passaggio successivo consiste nel distribuire Echo bot in Azure. Ci sono alcuni modi per distribuire un bot, ma in questa esercitazione ci concentreremo sulla pubblicazione diretta da Visual Studio.

> [!NOTE]
> In alternativa, è possibile distribuire un bot usando l' [interfaccia](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) della riga di comando di Azure e i [modelli di distribuzione](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates).

1. Da Visual Studio, aprire il bot echo che è stato configurato per l'uso con il canale di sintesi vocale direct line:
   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```
2. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla soluzione **EchoBot** e scegliere **pubblica...**
3. Viene aperta una nuova finestra denominata **Seleziona una destinazione di pubblicazione** .
3. Selezionare **servizio app** dal di spostamento a sinistra, selezionare **Crea nuovo**, quindi fare clic su **pubblica**.
5. Quando viene visualizzata la finestra **Crea servizio app** :
   * Fare clic su **Aggiungi un account**e accedere con le credenziali dell'account Azure. Se è già stato effettuato l'accesso, scegliere l'account da usare nell'elenco a discesa.
   * Per il **nome dell'app**, è necessario immettere un nome univoco globale per il bot. Questo nome viene usato per creare un URL bot univoco. Verrà popolato un valore predefinito, tra cui la data e l'ora, ad esempio: "EchoBot20190805125647"). Per questa esercitazione è possibile usare il nome predefinito.
   * Per la **sottoscrizione**, impostarla su **versione di valutazione gratuita**
   * Per **gruppo di risorse**, selezionare **SpeechEchoBotTutorial-ResourceGroup**
   * Per **piano di hosting**selezionare **SpeechEchoBotTutorial-AppServicePlan**
6. Fare clic su **Crea**
7. Verrà visualizzato un messaggio di operazione completata in Visual Studio simile al seguente:
   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```
8. Il browser predefinito dovrebbe aprirsi e visualizzare una pagina che legge: "Il tuo bot è pronto!".
9. A questo punto, controllare il gruppo di risorse **SpeechEchoBotTutorial-ResourceGroup** nel portale di Azure e verificare che siano disponibili tre risorse:

| NOME | TIPO  | PERCORSO |
|------|-------|----------|
| EchoBot20190805125647 | Servizio app | Stati Uniti occidentali |
| SpeechEchoBotTutorial-AppServicePlan | Piano di servizio app | Stati Uniti occidentali |
| SpeechEchoBotTutorial-sintesi vocale | Servizi cognitivi | Stati Uniti occidentali |

## <a name="enable-web-sockets"></a>Abilitare i WebSocket

È necessario apportare una piccola modifica alla configurazione in modo che il bot sia in grado di comunicare con il canale di sintesi vocale direct line usando i socket Web. Per abilitare i socket Web, attenersi alla procedura seguente:

1. Passare alla [portale di Azure](https://portal.azure.com)e individuare il servizio app. La risorsa deve essere denominata simile a **EchoBot20190805125647** (nome univoco dell'app).
2. Nel percorso di spostamento a sinistra selezionare **Impostazioni**, quindi fare clic su **configurazione**.
3. Selezionare la scheda **Impostazioni generali** .
4. Individuare l'interruttore per i **socket Web** e impostarlo **su on**.
5. Fare clic su **Save**.

> [!TIP]
> È possibile utilizzare i controlli nella parte superiore della pagina del servizio app Azure per arrestare o riavviare il servizio. Questo potrebbe essere utile per la risoluzione dei problemi.

## <a name="create-a-channel-registration"></a>Creare una registrazione del canale

Ora che è stato creato un servizio di app Azure per ospitare il bot, il passaggio successivo consiste nel creare una **registrazione dei canali bot**. La creazione di una registrazione del canale è un prerequisito per la registrazione del bot con i canali bot-Framework, incluso il canale vocale diretto.

> [!NOTE]
> Per altre informazioni sul modo in cui i bot sfruttano i canali, vedere [connettere un bot ai canali](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).

1. Il primo passaggio consiste nel creare una nuova risorsa per la registrazione. Nel [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa**.
2. Nella barra di ricerca digitare **bot**, dopo aver visualizzato i risultati, selezionare **bot Channels Registration**.
3. Fare clic su **Create**(Crea).
4. Verrà richiesto di fornire alcune informazioni:
   * Per **nome bot**immettere **SpeechEchoBotTutorial-BotRegistration**.
   * Per **sottoscrizione**selezionare **versione di valutazione gratuita**.
   * Per **gruppo di risorse**selezionare **SpeechEchoBotTutorial-ResourceGroup**.
   * Per **località**selezionare **Stati Uniti occidentali**.
     * Perpiano tariffario selezionare **F0**.
     * Per **endpoint di messaggistica**, immettere l'URL dell'app Web con il `/api/messages` percorso accodato alla fine. Ad esempio: se il nome dell'app globalmente univoco è **EchoBot20190805125647**, l'endpoint di messaggistica sarà `https://EchoBot20190805125647.azurewebsites.net/api/messages/`:.
     * Per **Application Insights**, è possibile impostare questa opzione su **off**. Per altre informazioni, vedere [bot Analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignora **creazione automatica ID app e password**.
5. Tornare alla registrazione dei **canali bot** e fare clic su **Crea**.

A questo punto, controllare il gruppo di risorse **SpeechEchoBotTutorial-ResourceGroup** nel portale di Azure. A questo punto dovrebbero essere visualizzate quattro risorse:

| NOME | TIPO  | PERCORSO |
|------|-------|----------|
| EchoBot20190805125647 | Servizio app | Stati Uniti occidentali |
| SpeechEchoBotTutorial-AppServicePlan | Piano di servizio app | Stati Uniti occidentali |
| SpeechEchoBotTutorial-BotRegistration | Registrazione canali bot | Global |
| SpeechEchoBotTutorial-sintesi vocale | Servizi cognitivi | Stati Uniti occidentali |

> [!IMPORTANT]
> La risorsa di registrazione dei canali bot visualizzerà l'area globale anche se è stata selezionata l'opzione Stati Uniti occidentali. Si tratta di un comportamento previsto.

## <a name="register-the-direct-line-speech-channel"></a>Registrare il canale vocale direct line

A questo punto è giunto il momento di registrare il bot con il canale di sintesi vocale direct line. Questo canale è quello che viene usato per creare una connessione tra il bot Echo e un'app client compilata con l'SDK di riconoscimento vocale.

1. Individuare e aprire la risorsa **SpeechEchoBotTutorial-BotRegistration** nel [portale di Azure](https://portal.azure.com).
2. Nel percorso di spostamento a sinistra selezionare **canali**.
   * Cercare **altri canali**, trovare e fare clic su **voce diretta linea**.
   * Esaminare il testo nella pagina denominata **Configura linea vocale diretta (anteprima)** , quindi fare clic su **Salva**.
   * Nell'ambito della creazione sono state generate due **chiavi segrete** . Queste chiavi sono univoche per il bot. Quando si scrive un'app client con l' [SDK di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/), è necessario specificare una di queste chiavi per stabilire una connessione tra l'app client, il canale di sintesi vocale diretta e il servizio bot. In questa esercitazione si userà il client Direct Line Speech (WPF C#).
   * Fare clic su **Mostra** e copiare una delle chiavi in un punto in cui sarà possibile accedervi facilmente. Non preoccuparti, è sempre possibile accedere alle chiavi dal portale di Azure.
3. Nella finestra di spostamento a sinistra fare clic su **Impostazioni**.
   * Selezionare la casella **Abilita endpoint di streaming**. Questa operazione è necessaria per abilitare un protocollo di comunicazione basato su Web socket tra il bot e il canale di riconoscimento vocale diretto.
   * Fare clic su **Save**.

> [!TIP]
> Per altre informazioni, vedere [connettere un bot a direct line Speech (anteprima)](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Questa pagina include informazioni aggiuntive e problemi noti.

## <a name="build-the-direct-line-speech-client"></a>Creare il client di sintesi vocale diretta

In questo passaggio verrà compilato il client di sintesi vocale diretta. Il client è un'app Windows Presentation Foundation (WPF) in C# che usa l' [SDK di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) per gestire la comunicazione con il bot usando il canale di sintesi vocale diretta. Usarlo per interagire con il bot e testarlo prima di scrivere un'app client personalizzata.

Il client Direct Line speech ha una semplice interfaccia utente che consente di configurare la connessione al bot, visualizzare la conversazione testuale, visualizzare le attività di bot-Framework in formato JSON e visualizzare le schede adattive. Supporta inoltre l'utilizzo di parole di riattivazione personalizzate. Questo client verrà usato per comunicare con il bot e ricevere una risposta vocale.

Prima di procedere, verificare che il microfono e le relatori siano abilitati e funzionanti.

1. Passare al repository GitHub per il [client Direct Line Speech](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md).
2. Seguire le istruzioni fornite per clonare il repository, compilare il progetto, configurare il client e avviare il client.
3. Fare clic su Riconnetti e verificare che venga visualizzato il messaggio **premere il pulsante MIC oppure digitare per iniziare a comunicare con il bot**.
4. È ora di provarlo. Fare clic sul pulsante del microfono e pronunciare alcune parole in inglese. Il testo riconosciuto verrà visualizzato mentre si parla. Al termine dell'operazione, il bot risponderà con la propria voce, affermando "echo" seguito dalle parole riconosciute.
5. È anche possibile usare il testo per comunicare con il bot. È sufficiente digitare il testo nella barra inferiore. 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Risoluzione degli errori nel client Direct Line Speech

Se viene ricevuto un messaggio di errore nella finestra principale dell'app, usare questa tabella per identificare e risolvere il problema:

| Errore | Quali operazioni è necessario eseguire? |
|-------|----------------------|
|Errore dell'app (vedere il log per i dettagli): Microsoft. CognitiveServices. Speech. CSharp: Il valore non può essere null. Nome parametro: speechConfig | Si tratta di un errore dell'app client. Assicurarsi di avere un valore non vuoto per *bot Secret* nella finestra principale dell'app (vedere la sezione [registrare il bot con il canale di sintesi vocale diretta](#register-the-direct-line-speech-channel)) |
|Errore AuthenticationFailure: L'aggiornamento di WebSocket non è riuscito con un errore di autenticazione (401). Verificare la chiave di sottoscrizione corretta (o il token di autorizzazione) e il nome dell'area| Nella pagina delle impostazioni dell'app verificare di aver immesso correttamente la chiave di sottoscrizione vocale e la relativa area.<br>Verificare che il segreto bot sia stato immesso correttamente. |
|Errore ConnectionFailure: Connessione chiusa dall'host remoto. Codice errore: 1011. Dettagli errore: Non è stato possibile connettersi al bot prima di inviare un messaggio | Assicurarsi di aver [selezionato la casella "Abilita endpoint di streaming"](#register-the-direct-line-speech-channel) e/o i [ **socket Web** attivati in attivato](#enable-web-sockets) .<br>Verificare che il servizio app Azure sia in esecuzione. In caso contrario, provare a riavviare il servizio app.|
|Errore ConnectionFailure: Connessione chiusa dall'host remoto. Codice errore: 1011. Dettagli errore: Il codice di stato della risposta non indica esito positivo: 500 (InternalServerError)| Il bot ha specificato una voce neurale nel campo dell'attività di output [Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) , ma l'area di Azure associata alla chiave di sottoscrizione vocale non supporta le voci neurali. Vedere [le voci standard e neurali](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Errore ConnectionFailure: Connessione chiusa dall'host remoto. Codice errore: 1000. Dettagli errore: Durata di inattività massima connessione Web socket superata (> 300000 MS)| Si tratta di un errore previsto quando una connessione al canale viene lasciata aperta e inattiva per più di cinque minuti. |

Se il problema non viene risolto nella tabella, vedere [la pagina relativa all'anteprima degli assistenti virtuali per la prima voce: domande frequenti](https://docs.microsoft.com/azure/cognitive-services/speech-service/faq-voice-first-virtual-assistants).

### <a name="view-bot-activities"></a>Visualizza attività bot

Ogni bot invia e riceve messaggi di **attività** . Nella finestra **log attività** del client Direct Line Speech verranno visualizzati i log con timestamp con ogni attività ricevuta dal client dal bot. È anche possibile visualizzare le attività inviate dal client al bot usando il [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) metodo. Quando si seleziona un elemento di log, vengono visualizzati i dettagli dell'attività associata come JSON.

Ecco un esempio di codice JSON di un'attività ricevuta dal client:
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

Per altre informazioni sugli elementi restituiti nell'output JSON, vedere [campi nell'attività](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Ai fini di questa esercitazione, è possibile concentrarsi sui campi [testo](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) e [parla](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) .

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Visualizzare il codice sorgente del client per le chiamate all'SDK di riconoscimento vocale

Il client di sintesi vocale diretta usa il pacchetto NuGet [Microsoft. CognitiveServices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), che contiene l'SDK di riconoscimento vocale. Un valido punto di partenza per esaminare il codice di esempio è il metodo InitSpeechConnector () nel file [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs), che consente di creare questi due oggetti dell'SDK vocale:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)-Per le impostazioni di configurazione (chiave di sottoscrizione vocale, area chiave, segreto bot)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)-Per gestire la connessione del canale e gli eventi di sottoscrizione client per la gestione delle risposte di riconoscimento vocale e bot riconosciute.

## <a name="add-custom-wake-word-activation"></a>Aggiunta dell'attivazione di una parola di riattivazione personalizzata

L'SDK di riconoscimento vocale supporta l'attivazione della parola di riattivazione personalizzata. Analogamente a "Hey Cortana" per l'Assistente di Microsoft, è possibile scrivere un'app che resterà in ascolto continua per una parola di riattivazione di propria scelta. Tenere presente che una parola di riattivazione può essere costituita da una parola singola o da una frase a più parole.

> [!NOTE]
> Il termine *lavoro* di riattivazione viene spesso usato in modo interscambiabile con la *parola chiave*e può essere usato nella documentazione Microsoft.

Il rilevamento delle parole di riattivazione viene eseguito nell'app client. Se si utilizza una parola di riattivazione, l'audio viene trasmesso solo al canale di riconoscimento diretto della linea se viene rilevata la parola di riattivazione. Il canale di sintesi vocale diretta include un componente denominato *KWV (Key Word Verification)* , che esegue un'elaborazione più complessa nel cloud per verificare che la parola di riattivazione scelta si trovi all'inizio del flusso audio. Se la verifica della parola chiave ha esito positivo, il canale comunicherà con il bot.

Attenersi alla procedura seguente per creare un modello di Word di riattivazione, configurare il client di sintesi vocale diretta per usare questo modello e infine testarlo con il bot.

1. Seguire queste istruzioni per [creare una parola di riattivazione personalizzata usando il servizio di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Decomprimere il file di modello scaricato nel passaggio precedente. Deve essere denominato per la parola di riattivazione. Si sta cercando un file denominato `kws.table`.
3. Nel client Direct Line Speech individuare il menu **Impostazioni** (cercare l'icona a forma di ingranaggio in alto a destra). Individuare **percorso file modello** e immettere il nome percorso completo del file `kws.table` nel passaggio 2.
4. Assicurarsi di selezionare la casella abilitata. Questo messaggio verrà visualizzato accanto alla casella di controllo: "Resterà in ascolto della parola di riattivazione alla connessione successiva". Se è stato specificato un file errato o un percorso non valido, verrà visualizzato un messaggio di errore.
5. Immettere la **chiave di sottoscrizione**vocale, l' **area chiave della sottoscrizione**e quindi fare clic su **OK** per chiudere il menu **Impostazioni** .
6. Selezionare un **segreto bot**, quindi fareclic su Riconnetti. Verrà visualizzato un messaggio che legge: "Nuova conversazione avviata: digitare, premere il pulsante del microfono o pronunciare la parola di riattivazione". L'app è ora in ascolto continua.
7. Pronunciare qualsiasi frase che inizia con la parola di riattivazione. Ad esempio: " **{Your Wake Word}** , qual è l'ora?". Non è necessario sospendere dopo aver pronunciato la parola di riattivazione. Al termine, vengono eseguite due operazioni:
   * Verrà visualizzata una trascrizione di ciò che si parla
   * Subito dopo, sarà possibile ascoltare la risposta del bot
8. Continuare a provare con i tre tipi di input supportati dal bot:
   * Testo digitato nella barra inferiore
   * Premere l'icona del microfono e parlare
   * Pronunciare una frase che inizia con la parola di riattivazione

### <a name="view-the-source-code-that-enables-wake-word"></a>Visualizzare il codice sorgente che Abilita la parola di riattivazione

Nel codice sorgente del client Direct Line esaminare questi file per esaminare il codice usato per abilitare il rilevamento delle parole di riattivazione:

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs)include una chiamata al metodo [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/en-us/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)dell'SDK vocale, che viene usato per creare un'istanza del modello da un file locale su disco.
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs)include una chiamata al metodo [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)dell'SDK di sintesi vocale, che attiva il rilevamento continuo della parola di riattivazione.

## <a name="optional-change-the-language-and-redeploy-your-bot"></a>Opzionale Modificare la lingua e ridistribuire il bot

Il bot creato sarà in ascolto e risponderà in inglese. Tuttavia, non si è limitati a usare l'inglese. In questa sezione si apprenderà come modificare la lingua che il bot resterà in ascolto e rispondere e ridistribuire il bot.

### <a name="change-the-language"></a>Modificare la lingua

1. Iniziamo aprendo `\experimental\directline-speech\csharp_dotnetcore\02.echo-bot\Bots\echo-bot.cs`.
2. Individuare quindi il SSML. È facile da trovare, in quanto è racchiuso tra `<speak></speak>` tag.
3. Nella stringa SSML individuare il `<voice name>` tag, sostituirlo con `<voice name='de-DE-Stefan-Apollo'>`e salvare. Questa stringa formattata indica al servizio di sintesi vocale di restituire una risposta sintesi vocale con la voce `de-DE-Stefan-Apollo`, ottimizzata per il tedesco.

>[!NOTE]
> Non si è limitati al tedesco ed è possibile scegliere dall'elenco di voci disponibili dal [servizio di riconoscimento vocale](language-support.md#text-to-speech).

### <a name="redeploy-your-bot"></a>Ridistribuire il bot

Ora che è stata apportata la modifica necessaria al bot, il passaggio successivo consiste nel ripubblicarlo nel servizio app Azure e provarlo:

1. Compilare la soluzione in Visual Studio e correggere gli eventuali errori di compilazione.
2. Nella finestra Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **EchoBot** e scegliere **pubblica**.
3. La configurazione della distribuzione precedente è già stata caricata come predefinita. È sufficiente fare clic su **Publish** accanto a **EchoBot20190805125647-distribuzione Web**.
4. Il messaggio **Publish succeeded** verrà visualizzato nella finestra di output di Visual Studio e verrà avviata una pagina Web con il messaggio "il bot è pronto!".
5. Aprire l'app client di sintesi vocale diretta, fare clic sul pulsante Settings (icona a forma di ingranaggio superiore `de-de` destro) e immettere nel campo Language (lingua). In questo modo la lingua parlata viene impostata in modo da essere `en-us`riconosciuta, eseguendo l'override dell'impostazione predefinita.
6. Seguire le istruzioni riportate in [creare il client di sintesi vocale diretta](#build-the-direct-line-speech-client) per riconnettersi al bot appena distribuito, rivolgersi al nuovo linguaggio e ascoltare il bot Reply in quel linguaggio con la nuova voce.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare echo-bot distribuito in questa esercitazione, è possibile rimuoverlo e tutte le risorse di Azure associate semplicemente eliminando il gruppo di risorse di Azure **SpeechEchoBotTutorial-ResourceGroup**.

1. Dal [portale di Azure](https://portal.azure.com)fare clic su **gruppi di risorse** nel percorso di spostamento a sinistra.
2. Trovare il gruppo di risorse denominato: **SpeechEchoBotTutorial-ResourceGroup**. Fare clic sui tre puntini di sospensione (...).
3. Selezionare **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app client personalizzata con l'SDK di riconoscimento vocale](quickstart-virtual-assistant-csharp-uwp.md)

## <a name="see-also"></a>Vedere anche

* Distribuzione in un' [area di Azure nelle vicinanze](https://azure.microsoft.com/global-infrastructure/locations/) per visualizzare il miglioramento del tempo di risposta del bot
* Distribuzione in un' [area di Azure che supporta le voci TTS neurali di qualità elevata](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Prezzi associati al canale vocale direct line:
  * [Prezzi del servizio bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Servizi Voce](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Compilazione e distribuzione di un bot abilitato per la voce:
    * Creare un [bot bot-Framework](https://dev.botframework.com/). Esegui la registrazione con il [canale vocale Direct Line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) e [Personalizza il bot per la voce](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
    * Esplora le [soluzioni bot-Framework](https://github.com/microsoft/botframework-solutions)esistenti: Creare un [assistente virtuale personalizzato per la prima voce](https://docs.microsoft.com/azure/cognitive-services/speech-service/voice-first-virtual-assistants) e [abilitarlo](https://github.com/microsoft/botframework-solutions/blob/master/docs/howto/assistant/csharp/speechenablement.md) per la voce
