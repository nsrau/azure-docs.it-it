---
title: 'Guida introduttiva: Eseguire Speech Devices SDK su Android - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Prerequisiti e istruzioni per iniziare a usare Speech Devices SDK per Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: ccc6c2554576d91d2236fb84f81c8aeccdc07be7
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815348"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Guida introduttiva: Eseguire l'app di esempio Speech Devices SDK su Android

In questa guida di avvio rapido si apprenderà come usare Speech Devices SDK per Android per compilare un prodotto abilitato al riconoscimento vocale o usarlo come dispositivo di [trascrizione conversazione](conversation-transcription-service.md).

Per riprodurre le procedure di questa guida, è necessario un account di [Servizi cognitivi di Azure](get-started.md) con una risorsa del servizio Voce. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/) per ottenere una chiave di sottoscrizione.

Il codice sorgente dell'applicazione di esempio è incluso in Speech Devices SDK ed è anche [disponibile in GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a usare Speech Devices SDK, è necessario:

- Seguire le istruzioni fornite nel [kit di sviluppo](get-speech-devices-sdk.md) per accendere il dispositivo.

- Scaricare la versione più recente di [Speech Devices SDK](https://aka.ms/sdsdk-download) ed estrarre il file ZIP nella directory di lavoro.

  > [!NOTE]
  > Il file Android-Sample-Release.zip include l'app di esempio per Android e questa guida di avvio rapido presuppone che l'app venga estratta in C:\SDSDK\Android-Sample-Release

- Per ottenere una [chiave di sottoscrizione di Azure per il servizio Voce](get-started.md)

- Se si prevede di usare la funzionalità di trascrizione conversazione, è necessario usare un [dispositivo con microfono circolare](get-speech-devices-sdk.md) e questa funzionalità è attualmente disponibile solo per i codici di lingua "en-US" e "zh-CN" nella aree "centralus" e "eastasia". È necessario avere una chiave di riconoscimento vocale in una di queste aree per usare la trascrizione conversazione.

- Se si prevede di usare il servizio Voce per identificare finalità (o azioni) dalle espressioni degli utenti, è necessaria una sottoscrizione di [LUIS (Language Understanding Service)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription). Per altre informazioni su LUIS e sul riconoscimento delle finalità, vedere [Riconoscere le finalità voce con LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  È possibile [creare un modello LUIS semplice](https://docs.microsoft.com/azure/cognitive-services/luis/) oppure usare il modello LUIS di esempio, ovvero LUIS-example.json. Il modello LUIS di esempio è disponibile nel [sito di download di Speech Devices SDK](https://aka.ms/sdsdk-luis). Per caricare il file JSON del modello nel [portale LUIS](https://www.luis.ai/home), selezionare **Import new app** (Importa nuova app) e quindi selezionare il file JSON.

- Installare [Android Studio](https://developer.android.com/studio/) e [Vysor](https://vysor.io/download/) nel PC.

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
   > ![Cartella di file Vysor](media/speech-devices-sdk/qsg-10.png)
   >
   > Selezionare **Impostazioni**. Cercare "indirizzo mac" e quindi selezionare **Mac address** > **Advanced WLAN** (Indirizzo MAC > WLAN avanzata). Prendere nota dell'indirizzo MAC che viene visualizzato nella parte inferiore della finestra di dialogo.
   >
   > ![Indirizzo MAC Vysor](media/speech-devices-sdk/qsg-11.png)
   >
   > Alcune società possono inoltre prevedere un limite al tempo in cui un dispositivo può rimanere connesso al sistema Wi-Fi aziendale. Può quindi essere necessario prolungare la registrazione del kit di sviluppo al sistema Wi-Fi dopo un certo numero di giorni.

## <a name="run-the-sample-application"></a>Eseguire l'applicazione di esempio

Per convalidare la configurazione del kit di sviluppo, compilare e installare l'applicazione di esempio:

1. Avviare Android Studio.

1. Selezionare **Open an existing Android Studio project** (Apri un progetto di Android Studio esistente).

   ![Android Studio: apertura di un progetto esistente](media/speech-devices-sdk/qsg-5.png)

1. Passare a C:\SDSDK\Android-Sample-Release\example. Fare clic su **OK** per aprire il progetto di esempio.

1. Aggiungere la chiave di sottoscrizione del Servizio di riconoscimento vocale al codice sorgente. Se si vuole provare la funzionalità Riconoscimento finalità, aggiungere anche la chiave di sottoscrizione e l'ID applicazione del [servizio Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

   Per il riconoscimento vocale e LUIS, le informazioni vanno in MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Se si usa la trascrizione conversazione, le informazioni sulla chiave di riconoscimento vocale e sull'area sono necessarie anche in conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. La parola chiave predefinita è "Computer". È anche possibile provare una delle altre parole chiave disponibili, come "Machine" o "Assistant". I file di risorse per queste parole chiave alternative sono disponibili nella cartella keyword di Speech Devices SDK. La cartella C:\SDSDK\Android-Sample-Release\keyword\Computer contiene ad esempio i file usati per la parola chiave "Computer".

   > [!TIP]
   > È anche possibile [creare una parola chiave personalizzata](speech-devices-sdk-create-kws.md).

   Per usare una nuova parola chiave, aggiornare le due righe seguenti in `MainActivity.java` e copiare il pacchetto di parole chiave nell'app. Ad esempio, per usare la parola chiave 'Machine' del pacchetto di parole chiave kws-machine.zip:

   - Copiare il pacchetto di parole chiave nella cartella "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   - Aggiornare `MainActivity.java` con la parola chiave e il nome del pacchetto:

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

   | Variabile | Significato | Valori disponibili |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Configurazione microfoni fisica | Per un kit di sviluppo circolare: `Circular6+1` |
   |          |         | Per un kit di sviluppo lineare: `Linear4` |
   | `SelectedGeometry` | Configurazione microfoni software | Per un kit di sviluppo circolare che usa tutti i microfoni: `Circular6+1` |
   |          |         | Per un kit di sviluppo circolare che usa quattro microfoni: `Circular3+1` |
   |          |         | Per un kit di sviluppo lineare che usa tutti i microfoni: `Linear4` |
   |          |         | Per un kit di sviluppo lineare che usa due microfoni: `Linear2` |

1. Per compilare l'applicazione, nel menu **Run** (Esegui) selezionare **Run 'app'** (Esegui 'app'). Viene visualizzata la finestra di dialogo **Select Deployment Target** (Seleziona destinazione di distribuzione).

1. Selezionare il dispositivo e quindi fare clic su **OK** per distribuire l'applicazione nel dispositivo.

   ![Finestra di dialogo Select Deployment Target (Seleziona destinazione di distribuzione)](media/speech-devices-sdk/qsg-7.png)

1. L'applicazione di esempio di Speech Devices SDK viene avviata e visualizza le opzioni seguenti:

   ![Applicazione di esempio di Speech Devices SDK e opzioni](media/speech-devices-sdk/qsg-8.png)

1. Provare la nuova demo di Trascrizione conversazione. Avviare la trascrizione con "Avvia sessione". Per impostazione predefinita, ogni utente è un guest. Se però si hanno le firme vocali dei partecipanti, è possibile inserirle in un file `/video/participants.properties` nel dispositivo. Per generare la firma vocale, vedere [Trascrivere le conversazioni (SDK)](how-to-use-conversation-transcription-service.md).

   ![Applicazione demo di Trascrizione conversazione](media/speech-devices-sdk/qsg-15.png)

1. A questo punto, non resta che sperimentare.

## <a name="troubleshooting"></a>risoluzione dei problemi

Se non è possibile connettersi al dispositivo di riconoscimento vocale, digitare il comando seguente in una finestra del prompt dei comandi. Verrà restituito un elenco di dispositivi.

```powershell
 adb devices
```

> [!NOTE]
> Questo comando usa Android Debug Bridge, `adb.exe`, che fa parte dell'installazione di Android Studio. Questo strumento si trova in C:\Utenti\[nome utente]\AppData\Local\Android\Sdk\platform-tools. Per richiamare più facilmente `adb`, è possibile aggiungere questa directory al percorso. In caso contrario, è necessario specificare il percorso completo dell'installazione di adb.exe in ogni comando che richiama `adb`.
>
> Se viene visualizzato un errore `no devices/emulators found`, verificare che il cavo USB sia collegato e che sia un cavo di alta qualità.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare le note sulla versione](devices-sdk-release-notes.md)
