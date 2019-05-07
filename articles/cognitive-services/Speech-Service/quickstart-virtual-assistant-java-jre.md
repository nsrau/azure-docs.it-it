---
title: 'Guida introduttiva: Assistente virtuale voice-first personalizzato (anteprima), Java (Windows, Linux) - Servizi Voce'
titleSuffix: Azure Cognitive Services
description: Questa guida di avvio rapido spiega come usare Speech SDK di Servizi cognitivi in un'applicazione console Java. Viene spiegato come connettere l'applicazione client a un bot di Bot Framework creato in precedenza e configurato per l'uso del canale Direct Line Speech e abilitare un'esperienza di assistente virtuale voice-first.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025366"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Guida introduttiva: Creare un assistente virtuale voice-first con Speech SDK, Java

In questo articolo viene creata un'applicazione console Java usando [Speech SDK di Servizi cognitivi](speech-sdk.md). L'applicazione si connetterà a un bot creato in precedenza e configurato per l'uso del canale Direct Line Speech, invierà una richiesta vocale e restituirà un'attività di risposta vocale (se configurata). L'applicazione viene creata con il pacchetto Maven di Speech SDK e l'IDE Eclipse Java in Windows, Ubuntu Linux o in macOS. Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Sistema operativo: Windows (a 64 bit), Ubuntu Linux 16.04/18.04 (a 64 bit) o macOS 10.13 o versione successiva
* [Ambiente IDE Java Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* Un bot preconfigurato creato con Bot Framework 4.2 o versione successiva. Per ricevere gli input vocali, il bot deve sottoscrivere il nuovo canale "Direct Line Speech".

    > [!NOTE]
    > In fase di anteprima, il canale Direct Line Speech supporta attualmente solo l'area **westus2**.

    > [!NOTE]
    > La versione di valutazione di 30 giorni per il piano tariffario standard descritta in [Provare gratuitamente i servizi di riconoscimento vocale](get-started.md) è limitata a **westus** (non **westus2**) e di conseguenza non è compatibile con Direct Line Speech. Le sottoscrizioni del livello Gratuito e Standard di **westus2** sono compatibili.

Se si esegue Ubuntu 16.04/18.04, assicurarsi che queste dipendenze siano installate prima di avviare Eclipse:

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Se si esegue Windows (64 bit) assicurarsi di avere installato Microsoft Visual C++ Redistributable per la piattaforma in uso:
* [Scaricare Microsoft Visual C++ Redistributable per Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Facoltativo: Introduzione rapida

Questa guida di avvio rapido descrive in dettaglio come creare una semplice applicazione client per connettersi al bot abilitato al riconoscimento vocale. Se si preferisce iniziare subito, il codice sorgente completo e pronto per la compilazione usato in questa guida di avvio rapido è disponibile nella cartella `quickstart` negli [esempi di SDK Speech](https://aka.ms/csspeech/samples).

## <a name="create-and-configure-project"></a>Creare e configurare un progetto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Per abilitare la registrazione, aggiornare inoltre il file **pom.xml** in modo da includere la dipendenza seguente.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Per aggiungere una nuova classe vuota al progetto Java, selezionare **File** > **Nuovo** > **Classe**.

1. Nella finestra **Nuova classe Java** immettere **speechsdk.quickstart** nel campo **Pacchetto** e **Main** nel campo **Nome** .

   ![Screenshot della procedura guidata Nuova classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Aprire la classe **Main** appena creata e sostituire il contenuto del file `Main.java` con il codice iniziale seguente.

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. Nel metodo **main** configurare innanzitutto `BotConnectorConfig` e usarlo per creare un'istanza di `SpeechBotConnector`. In questo modo sarà possibile connettersi al canale Direct Line Speech per interagire con il bot. Viene inoltre usata un'istanza di `AudioConfig` per specificare l'origine dell'input audio. In questo esempio il microfono predefinito viene usato con `AudioConfig.fromDefaultMicrophoneInput()`.

    * Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione, che è possibile ottenere [qui](get-started.md).
    * Sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione.
    * Sostituire la stringa `YourChannelSecret` con il segreto del canale Direct Line Speech.

    > [!NOTE]
    > In fase di anteprima, il canale Direct Line Speech supporta attualmente solo l'area **westus2**.

    > [!NOTE]
    > La versione di valutazione di 30 giorni per il piano tariffario standard descritta in [Provare gratuitamente i servizi di riconoscimento vocale](get-started.md) è limitata a **westus** (non **westus2**) e di conseguenza non è compatibile con Direct Line Speech. Le sottoscrizioni del livello Gratuito e Standard di **westus2** sono compatibili.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` si basa su diversi eventi per comunicare le attività del bot, i risultati del riconoscimento vocale e altre informazioni. Aggiungere più avanti questi listener di eventi.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. Per connettere `SpeechBotConnector` a Direct Line Speech, richiamare il metodo `connectAsync()`. Per testare il bot, è possibile richiamare il metodo `listenOnceAsync` per inviare l'input audio dal microfono. Inoltre, è anche possibile usare il metodo `sendActivityAsync` per inviare un'attività personalizzata sotto forma di stringa serializzata. Queste attività personalizzate possono fornire dati aggiuntivi che verranno usati dal bot nella conversazione.

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. Salvare le modifiche apportate al file `Main`.

1. Per supportare la riproduzione delle risposte, verrà aggiunta una classe aggiuntiva che include i metodi di utilità per supportare l'audio. Per abilitare l'audio, aggiungere un'altra classe nuova vuota al progetto Java. Selezionare **File** > **New** (Nuovo)  > **Class** (Classe).

1. Nella finestra **New Java Class** (Nuova classe Java) immettere **speechsdk.quickstart** nel campo **Package** (Pacchetto) e **AudioPlayer** nel campo **Name**  (Nome).

   ![Screenshot della procedura guidata Nuova classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Aprire la classe **AudioPlayer** appena creata e sostituire il contenuto seguente.

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. Salvare le modifiche apportate al file `AudioPlayer`.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.
La console visualizzerà un messaggio "Say something" (Pronunciare una frase). A questo punto, è possibile pronunciare una frase o un'espressione in inglese che verrà riconosciuta dal bot. Il parlato verrà trasmesso al bot tramite il canale Direct Line Speech, in cui verrà riconosciuto ed elaborato dal bot. La risposta verrà restituita sotto forma di attività. Se il bot restituisce parlato come risposta, l'audio verrà riprodotto usando la classe `AudioPlayer`.

![Screenshot della console dopo il riconoscimento corretto](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio per eseguire il riconoscimento vocale da un file audio, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche 

- [Guida introduttiva: Traduzione vocale, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
