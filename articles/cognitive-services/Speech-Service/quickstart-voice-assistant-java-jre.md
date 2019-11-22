---
title: 'Guida introduttiva: Assistente vocale personalizzato per Java (Windows, Linux) - Servizio di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Questa guida di avvio rapido spiega come usare Speech SDK di Servizi cognitivi in un'applicazione console Java. Viene spiegato come connettere l'applicazione client a un bot di Bot Framework creato in precedenza e configurato per l'uso del canale Direct Line Speech e abilitare un'esperienza di assistente vocale.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: bidishac
ms.openlocfilehash: 54a5be769ace97ffa9a4f5f38a9227d9565abfd1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111703"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-java-preview"></a>Guida introduttiva: Creare un assistente vocale con Speech SDK, Java (anteprima)

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre), la [sintesi vocale](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre) e la [traduzione vocale](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre).

In questo articolo viene creata un'applicazione console Java usando [Speech SDK di Servizi cognitivi di Azure](speech-sdk.md). L'applicazione si connetterà a un bot creato in precedenza e configurato per l'uso del canale Direct Line Speech, invierà una richiesta vocale e restituirà un'attività di risposta vocale (se configurata). L'applicazione viene creata con il pacchetto Maven di Speech SDK e l'IDE Eclipse Java in Windows, Ubuntu Linux o in macOS. Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

- Sistema operativo: Windows (a 64 bit), Ubuntu Linux 16.04/18.04 (a 64 bit) o macOS 10.13 o versione successiva.
- [Ambiente IDE Java Eclipse](https://www.eclipse.org/downloads/).
- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
- Una chiave di sottoscrizione di Azure per i servizi Voce. [È possibile ottenerne una gratuitamente](get-started.md) o crearla nel [portale di Azure](https://portal.azure.com).
- Un bot preconfigurato creato con Bot Framework 4.2 o versione successiva. Per ricevere gli input vocali, il bot deve sottoscrivere il nuovo canale Direct Line Speech.

  > [!NOTE]
  > Consultare [l'elenco delle aree supportate per gli assistenti vocali](regions.md#voice-assistants) e assicurarsi che le risorse vengano distribuite in una di queste aree.

Se si esegue Ubuntu 16.04/18.04, assicurarsi che queste dipendenze siano installate prima di avviare Eclipse:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Se si esegue Windows (64 bit), assicurarsi di avere installato Microsoft Visual C++ Redistributable per la piattaforma in uso:

- [Scaricare Microsoft Visual C++ Redistributable per Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Facoltativo: Introduzione rapida

Questa guida di avvio rapido descrive in dettaglio come creare una semplice applicazione client per connettersi al bot abilitato per i servizi voce. Se si preferisce iniziare subito, il codice sorgente completo e pronto per la compilazione usato in questa guida di avvio rapido è disponibile nella cartella `quickstart` degli [esempi di Speech SDK](https://aka.ms/csspeech/samples).

## <a name="create-and-configure-project"></a>Creare e configurare un progetto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Per abilitare la registrazione, aggiornare inoltre il file _pom.xml_ in modo da includere la dipendenza seguente:

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Per aggiungere una nuova classe vuota al progetto Java, selezionare **File** > **Nuovo** > **Classe**.

1. Nella finestra **Nuova classe Java** immettere _speechsdk.quickstart_ nel campo **Pacchetto** e _Main_ nel campo **Nome**.

   ![Screenshot della procedura guidata Nuova classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Aprire la classe `Main` appena creata e sostituire il contenuto del file `Main.java` con il codice iniziale seguente:

   ```java
   package speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;

   import javax.sound.sampled.AudioFormat;
   import javax.sound.sampled.AudioSystem;
   import javax.sound.sampled.DataLine;
   import javax.sound.sampled.SourceDataLine;
   import java.io.InputStream;

   public class Main {
       final Logger log = LoggerFactory.getLogger(Main.class);

       public static void main(String[] args) {
           // New code will go here
       }

       private void playAudioStream(PullAudioOutputStream audio) {
           ActivityAudioStream stream = new ActivityAudioStream(audio);
           final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
           final AudioFormat format = new AudioFormat(
                   AudioFormat.Encoding.PCM_SIGNED,
                   audioFormat.getSamplesPerSecond(),
                   audioFormat.getBitsPerSample(),
                   audioFormat.getChannels(),
                   audioFormat.getFrameSize(),
                   audioFormat.getSamplesPerSecond(),
                   false);
           try {
               int bufferSize = format.getFrameSize();
               final byte[] data = new byte[bufferSize];

               SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
               SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
               line.open(format);

               if (line != null) {
                   line.start();
                   int nBytesRead = 0;
                   while (nBytesRead != -1) {
                       nBytesRead = stream.read(data);
                       if (nBytesRead != -1) {
                           line.write(data, 0, nBytesRead);
                       }
                   }
                   line.drain();
                   line.stop();
                   line.close();
               }
               stream.close();

           } catch (Exception e) {
               e.printStackTrace();
           }
       }

   }
   ```

1. Nel metodo `main` è prima necessario configurare `DialogServiceConfig` e usarlo per creare un'istanza di `DialogServiceConnector`. Questa istanza si connette al canale Direct Line Speech per interagire con il bot. Viene inoltre usata un'istanza di `AudioConfig` per specificare l'origine dell'input audio. In questo esempio il microfono predefinito viene usato con `AudioConfig.fromDefaultMicrophoneInput()`.

   - Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione disponibile in [questo sito Web](get-started.md).
   - Sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione.

   > [!NOTE]
   > Consultare [l'elenco delle aree supportate per gli assistenti vocali](regions.md#voice-assistants) e assicurarsi che le risorse vengano distribuite in una di queste aree.

   ```java
   final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
   final String region = "YourServiceRegion"; // Your speech subscription service region
   final BotFrameworkConfig botConfig = BotFrameworkConfig.fromSubscription(subscriptionKey, region);

   // Configure audio input from a microphone.
   final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

   // Create a DialogServiceConnector instance.
   final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
   ```

1. Il connettore `DialogServiceConnector` si basa su diversi eventi per comunicare le attività del bot, i risultati del riconoscimento vocale e altre informazioni. Aggiungere più avanti questi listener di eventi.

   ```java
   // Recognizing will provide the intermediate recognized text while an audio stream is being processed.
   connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // Recognized will provide the final recognized text once audio capture is completed.
   connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // SessionStarted will notify when audio begins flowing to the service for a turn.
   connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
       log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
   });

   // SessionStopped will notify when a turn is complete and it's safe to begin listening again.
   connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
       log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
   });

   // Canceled will be signaled when a turn is aborted or experiences an error condition.
   connector.canceled.addEventListener((o, canceledEventArgs) -> {
       log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
       connector.disconnectAsync();
   });

   // ActivityReceived is the main way your bot will communicate with the client and uses Bot Framework activities.
   connector.activityReceived.addEventListener((o, activityEventArgs) -> {
       final String act = activityEventArgs.getActivity().serialize();
           log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
           if (activityEventArgs.hasAudio()) {
               playAudioStream(activityEventArgs.getAudio());
           }
       });
   ```

1. Per connettere `DialogServiceConnector` a Direct Line Speech, richiamare il metodo `connectAsync()`. Per testare il bot, è possibile richiamare il metodo `listenOnceAsync` per inviare l'input audio dal microfono. Inoltre, è anche possibile usare il metodo `sendActivityAsync` per inviare un'attività personalizzata sotto forma di stringa serializzata. Queste attività personalizzate possono fornire dati aggiuntivi che verranno usati dal bot nella conversazione.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. Salvare le modifiche apportate al file `Main`.

1. Per supportare la riproduzione delle risposte, verrà aggiunta un'ulteriore classe che trasformerà l'oggetto PullAudioOutputStream restituito dall'API getAudio() in un oggetto Java InputStream per una gestione semplificata. Questo oggetto `ActivityAudioStream` è una classe specializzata che gestirà la risposta audio restituita dal canale Direct Line Speech. Fornirà le funzioni di accesso per il recupero delle informazioni sul formato audio necessarie per la gestione della riproduzione. Selezionare quindi **File** > **New** (Nuovo)  > **Class** (Classe).

1. Nella finestra **New Java Class** (Nuova classe Java) immettere _speechsdk.quickstart_ nel campo **Package** (Pacchetto) e _ActivityAudioStream_ nel campo **Name** (Nome).

1. Aprire la classe `ActivityAudioStream` appena creata e sostituirne il contenuto con il codice seguente:

   ```java
   package com.speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

   import java.io.IOException;
   import java.io.InputStream;

    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second (16 kHz).
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format (16 bits).
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream,
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample, and the # channels.
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer, this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream.
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

   ```

1. Salvare le modifiche apportate al file `ActivityAudioStream`.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.
La console visualizza il messaggio "Say something" (Iniziare a parlare).
A questo punto, pronunciare una frase in lingua inglese che può essere interpretata dal bot. La frase viene trasmessa al bot tramite il canale Direct Line Speech in cui viene riconosciuta ed elaborata dal bot. La risposta viene restituita come un'entità. Se il bot restituisce una risposta di tipo parlato, l'audio verrà riprodotto usando la classe `AudioPlayer`.

![Screenshot della console dopo il riconoscimento corretto](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio per eseguire il riconoscimento vocale da un file audio, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Creare e distribuire un bot di base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Vedere anche

- [Informazioni sugli assistenti vocali](voice-assistants.md)
- [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
- [Parole chiave personalizzate](speech-devices-sdk-create-kws.md)
- [Collegare Direct Line Speech al bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/csspeech/samples)
