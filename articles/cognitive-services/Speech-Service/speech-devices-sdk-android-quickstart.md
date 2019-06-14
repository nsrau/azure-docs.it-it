---
title: 'Avvio rapido: Eseguire i dispositivi di Speech SDK in Android - servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Prerequisiti e istruzioni per iniziare con un SDK di dispositivi Android di riconoscimento vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 5bebdd77afb9e9c77624ea4266ca217567dbf143
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072385"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Avvio rapido: Eseguire l'app di esempio Speech SDK di dispositivi in Android

In questa Guida introduttiva verrà illustrato come usare dispositivi Speech SDK per Android per compilare un prodotto abilitate al riconoscimento vocale o usarla come una [trascrizione conversazione](conversation-transcription-service.md) dispositivo.

Questa guida richiede un [servizi cognitivi di Azure](get-started.md) account con una risorsa di servizi di riconoscimento vocale. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/) per ottenere una chiave di sottoscrizione.

Il codice sorgente dell'applicazione di esempio è incluso in Speech Devices SDK ed è anche [disponibile in GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a usare il SDK di dispositivi di riconoscimento vocale, è necessario:

* Seguire le istruzioni fornite con il [kit di sviluppo](get-speech-devices-sdk.md) per accendere il dispositivo.

* Scaricare la versione più recente del [vocale Devices SDK](https://aka.ms/sdsdk-download)ed estrarre il file zip nella directory di lavoro.
   > [!NOTE]
   > Il file di esempio-Android-Release.zip include l'app di esempio di Android e questa Guida introduttiva presuppone che l'app venga estratto in C:\SDSDK\Android-Sample-Release

* Per ottenere un [chiave di sottoscrizione di Azure per servizi di riconoscimento vocale](get-started.md)

* Se si prevede di usare la trascrizione di conversazione è necessario utilizzare un [microfono circolare](get-speech-devices-sdk.md) e questa funzionalità è attualmente disponibile solo per "en-US" e "zh-CN" in aree geografiche, "centralus" e "Asia orientale". È necessario disporre una chiave di riconoscimento vocale in una di queste aree per usare la trascrizione di conversazione.

* Se si prevede di usare i servizi di riconoscimento vocale per identificare gli Intent (o azioni) da espressioni di utente, è necessario un [servizio LUIS (Language Understanding)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) sottoscrizione. Per altre informazioni su LUIS e riconoscimento delle intenzioni tramite, vedere [riconosce gli Intent di riconoscimento vocale con LUIS, C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    È possibile [creare un modello LUIS semplice](https://docs.microsoft.com/azure/cognitive-services/luis/) oppure usare il modello LUIS di esempio, ovvero LUIS-example.json. Il modello LUIS di esempio è disponibile nel [sito di download di Speech Devices SDK](https://aka.ms/sdsdk-luis). Per caricare il file JSON del modello nel [portale LUIS](https://www.luis.ai/home), selezionare **Import new app** (Importa nuova app) e quindi selezionare il file JSON.

* Installare [Android Studio](https://developer.android.com/studio/) e [Vysor](https://vysor.io/download/) nel PC.

## <a name="set-up-the-device"></a>Configurare il dispositivo

1. Avviare Vysor nel computer.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Il dispositivo dovrebbe essere incluso nell'elenco **Choose a device** (Scegli un dispositivo). Selezionare clic sul pulsante **View** (Visualizza) accanto al dispositivo.

1. Connettersi alla rete wireless facendo clic sull'icona della cartella e quindi su **Impostazioni** > **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Se la società prevede criteri per la connessione dei dispositivi al sistema Wi-Fi, è necessario ottenere l'indirizzo MAC e contattare il reparto IT per sapere come connettersi al sistema Wi-Fi aziendale.
    >
    > Per trovare l'indirizzo MAC del kit di sviluppo, selezionare l'icona della cartella file sul desktop del kit di sviluppo.
    >
    >  ![Cartella di file Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Selezionare **Impostazioni**. Cercare "indirizzo mac" e quindi selezionare **Mac address** > **Advanced WLAN** (Indirizzo MAC > WLAN avanzata). Prendere nota dell'indirizzo MAC che viene visualizzato nella parte inferiore della finestra di dialogo.
    >
    > ![Indirizzo MAC Vysor](media/speech-devices-sdk/qsg-11.png)
    >
    > Alcune società possono inoltre prevedere un limite al tempo in cui un dispositivo può rimanere connesso al sistema Wi-Fi aziendale. Può quindi essere necessario prolungare la registrazione del kit di sviluppo al sistema Wi-Fi dopo un certo numero di giorni.

## <a name="run-the-sample-application"></a>Eseguire l'applicazione di esempio

Per convalidare l'installazione del kit di sviluppo, compilare e installare l'applicazione di esempio:

1. Avviare Android Studio.

1. Selezionare **Open an existing Android Studio project** (Apri un progetto di Android Studio esistente).

   ![Android Studio: apertura di un progetto esistente](media/speech-devices-sdk/qsg-5.png)

1. Passare a C:\SDSDK\Android-Sample-Release\example. Fare clic su **OK** per aprire il progetto di esempio.

1. Aggiungere la chiave di sottoscrizione del riconoscimento vocale per il codice sorgente. Se si vuole provare la funzionalità Riconoscimento finalità, aggiungere anche la chiave di sottoscrizione e l'ID applicazione del [servizio Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

   Per il riconoscimento vocale e LUIS, le informazioni entra in mainactivity. Java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Se si usa la trascrizione di conversazione, sono necessarie anche le informazioni chiave e l'area di riconoscimento vocale in conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. La parola di attivazione (parola chiave) predefinita è "Computer". È anche possibile provare una delle altre parole di attivazione disponibili, come "Machine" o "Assistant". I file di risorse per queste parole di attivazione alternative sono disponibili nella cartella keyword di Speech Devices SDK. La cartella C:\SDSDK\Android-Sample-Release\keyword\Computer contiene ad esempio i file usati per la parola di attivazione "Computer".

   > [!TIP]
   > È anche possibile [creare una parola di attivazione personalizzata](speech-devices-sdk-create-kws.md).

    Per usare un nuovo termine di riattivazione, aggiornare le due righe seguenti in `MainActivity.java`e copiare il pacchetto di word riattivazione all'app. Ad esempio, per usare la parola di riattivazione 'Machine' from il kws pacchetti di riattivazione word-machine.zip:

   * Copiare il pacchetto di word di riattivazione nella cartella "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   * Aggiornamento di `MainActivity.java` con la parola chiave e il nome del pacchetto:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Aggiornare le righe seguenti, che contengono le impostazioni per la geometria dell'array di microfoni:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Questa tabella elenca i valori supportati:

   |Variabile|Significato|Valori disponibili|
   |--------|-------|----------------|
   |`DeviceGeometry`|Configurazione microfoni fisica|Per un kit di sviluppo circolare: `Circular6+1` |
   |||Per un kit di sviluppo lineare: `Linear4`|
   |`SelectedGeometry`|Configurazione microfoni software|Per un kit di sviluppo circolare che usa tutti i microfoni: `Circular6+1`|
   |||Per un kit di sviluppo circolare che usa quattro microfoni: `Circular3+1`|
   |||Per un kit di sviluppo lineare che usa tutti i microfoni: `Linear4`|
   |||Per un kit di sviluppo lineare che usa due microfoni: `Linear2`|

1. Per compilare l'applicazione, nel menu **Run** (Esegui) selezionare **Run 'app'** (Esegui 'app'). Viene visualizzata la finestra di dialogo **Select Deployment Target** (Seleziona destinazione di distribuzione).

1. Selezionare il dispositivo e quindi fare clic su **OK** per distribuire l'applicazione nel dispositivo.

    ![Finestra di dialogo Select Deployment Target (Seleziona destinazione di distribuzione)](media/speech-devices-sdk/qsg-7.png)

1. L'applicazione di esempio di Speech Devices SDK viene avviata e visualizza le opzioni seguenti:

   ![Applicazione di esempio di Speech Devices SDK e opzioni](media/speech-devices-sdk/qsg-8.png)

1. Prova la demo di trascrizione di conversazione nuova. Avviare la trascrizione con 'Avvia sessione'. Per impostazione predefinita tutti gli utenti è un ospite. Tuttavia, se si dispone delle firme di voce del partecipante è possibile inserire in un file `/video/participants.properties` nel dispositivo. Per generare la firma vocale, esaminare [trascrivere conversazioni (SDK)](how-to-use-conversation-transcription-service.md).

   ![Applicazione demo conversazione trascrizione](media/speech-devices-sdk/qsg-15.png)

1. A questo punto, non resta che sperimentare.

## <a name="troubleshooting"></a>risoluzione dei problemi

   Se non è possibile connettersi al dispositivo di riconoscimento vocale. Digitare il comando seguente in una finestra del prompt dei comandi. Restituirà un elenco dei dispositivi:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Questo comando Usa Android Debug Bridge, `adb.exe`, che fa parte dell'installazione di Android Studio. Questo strumento si trova in C:\Utenti\[nome utente]\AppData\Local\Android\Sdk\platform-tools. Per richiamare più facilmente `adb`, è possibile aggiungere questa directory al percorso. In caso contrario, è necessario specificare il percorso completo dell'installazione di adb.exe in ogni comando che richiama `adb`.
   >
   > Se viene visualizzato un errore `no devices/emulators found` quindi verificare il cavo USB è connesso e assicurarsi che viene usato un cavo di qualità elevata.
   >

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare le note sulla versione](devices-sdk-release-notes.md)
