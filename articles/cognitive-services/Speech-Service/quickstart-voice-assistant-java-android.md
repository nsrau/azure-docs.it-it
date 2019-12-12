---
title: 'Guida introduttiva: Assistente vocale personalizzato, Java (Android) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare un'applicazione di assistente vocale in Java in ambiente Android con Speech SDK
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: travisw
ms.openlocfilehash: 70031513d6f120e539e80d6befad4bf9320fb9c4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975959"
---
# <a name="quickstart-create-a-voice-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Guida introduttiva: Creare un assistente vocale in Java in ambiente Android con Speech SDK

È disponibile una guida di avvio rapido anche per il [riconoscimento vocale](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android) e la [sintesi vocale](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android).

In questo articolo si creerà un assistente vocale con Java per Android tramite [Speech SDK](speech-sdk.md). L'applicazione si connetterà a un bot precedentemente creato e configurato con il [canale Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Invierà quindi una richiesta vocale al bot e presenterà un'attività di risposta con riconoscimento vocale.

L'applicazione è basata sul pacchetto Maven per Speech SDK e su Android Studio 3.3. Speech SDK è attualmente compatibile con i dispositivi Android con processori ARM a 32/64 bit e Intel x86/x64 compatibili.

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

- Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md) o crearla nel [portale di Azure](https://portal.azure.com).
- Un bot creato in precedenza e configurato per l'uso del [canale Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).
- [Android Studio](https://developer.android.com/studio/) 3.3 o versioni successive

    > [!NOTE]
    > Consultare [l'elenco delle aree supportate per gli assistenti vocali ](regions.md#voice-assistants) e assicurarsi che le risorse vengano distribuite in una di queste aree.

## <a name="create-and-configure-a-project"></a>Creare e configurare un progetto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Creare l'interfaccia utente

In questa sezione si creerà un'interfaccia utente di base per l'applicazione. Per prima cosa, aprire l'attività principale: `activity_main.xml`. Il modello di base include una barra del titolo con il nome dell'applicazione e un elemento `TextView` con il messaggio "Hello world!".

Sostituire quindi il contenuto di `activity_main.xml` con il codice seguente:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Questo codice XML definisce un'interfaccia utente semplice per l'interazione con il bot.

- L'elemento `button` avvia un'interazione e, quando viene selezionato, richiama il metodo `onBotButtonClicked`.
- Durante l'interazione con il bot, l'elemento `recoText` consentirà di visualizzare i risultati del riconoscimento vocale.
- L'elemento `activityText` consentirà invece di visualizzare il payload JSON relativo all'ultima attività di Bot Framework eseguita dal bot.

Il testo e la versione grafica dell'interfaccia utente dovrebbero essere simili all'immagine seguente:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire `MainActivity.java` e sostituire il contenuto con il codice seguente:

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * Il metodo `onCreate` include codice che richiede autorizzazioni Internet e microfono.

   * Il metodo `onBotButtonClicked` è, come indicato in precedenza, il gestore di clic del pulsante. Premendo un pulsante si attiva una singola interazione ("turno") con il bot.

   * Il metodo `registerEventListeners` consente di visualizzare gli eventi usati dal `DialogServiceConnector` e la gestione di base delle attività in ingresso.

1. Nello stesso file, sostituire le stringhe di configurazione in modo che corrispondano alle risorse:

    * Sostituire `YourChannelSecret` con il segreto del canale Direct Line Speech relativo al bot.

    * Sostituire `YourSpeechSubscriptionKey` con la chiave di sottoscrizione.

    * Sostituire `YourServiceRegion` con l'[area di Azure](regions.md) associata alla propria sottoscrizione. È attualmente supportato solo un sottoinsieme di aree per il servizio Voce con Direct Line Speech. Per altre informazioni, vedere le [aree di Azure](regions.md#voice-assistants).

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Connettere il dispositivo Android al PC di sviluppo. Assicurarsi di avere la [modalità di sviluppo e il debug USB](https://developer.android.com/studio/debug/dev-options) abilitati sul dispositivo.

1. Per compilare l'applicazione, premere Ctrl + F9 o scegliere **Compila** > **Crea progetto** dalla barra del menu.

1. Per avviare l'applicazione, premere MAIUSC + F10 oppure scegliere **Esegui** > **Esegui 'app'** .

1. Nella finestra di destinazione della distribuzione che viene visualizzata, selezionare il dispositivo Android.

   ![Screenshot della finestra di schermata Seleziona destinazione di distribuzione](media/sdk/qs-java-android-12-deploy.png)

Dopo aver avviato l'applicazione e la relativa attività, fare clic sul pulsante per iniziare a interagire con il bot. Il testo trascritto apparirà mentre si parla e l'ultima attività ricevuta dal bot verrà visualizzata nel momento in cui viene ricevuta. Se il bot è configurato per fornire risposte vocali, verrà automaticamente avviato il riconoscimento vocale.

![Screenshot dell'applicazione Android](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare e distribuire un bot di base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Vedere anche
- [Informazioni sugli assistenti vocali](voice-assistants.md)
- [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
- [Parole chiave personalizzate](speech-devices-sdk-create-kws.md)
- [Collegare Direct Line Speech al bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/csspeech/samples)
