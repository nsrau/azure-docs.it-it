---
title: 'Guida introduttiva: Traduzione vocale, Java (Windows, Linux)- Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si creerà una semplice applicazione Java per acquisire i contenuti vocali dell'utente, convertirli in un'altra lingua e restituire il testo nella riga di comando. Questa guida è destinata agli utenti di Windows e Linux.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: e0c5139203b360be1eed2292d3ca07c9948b2ca5
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729564"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Guida introduttiva: Traduzione vocale con Speech SDK per Java

In questa guida introduttiva si creerà una semplice applicazione Java per acquisire i contenuti vocali dell'utente dal microfono del computer, tradurli e trascrivere il testo tradotto nella riga di comando in tempo reale. Quest'applicazione è progettata per l'esecuzione in Windows a 64 bit o Ubuntu Linux a 64 bit 16.04/18.04 ed è integrata con il pacchetto Speech SDK Maven e l'ambiente IDE Java Eclipse.

Per un elenco completo di lingue disponibili per la traduzione vocale, vedere [Supporto per le lingue](language-support.md).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Sistema operativo: Windows a 64 bit o Ubuntu Linux a 64 bit 16.04/18.04
* [Ambiente IDE Java Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

Se si esegue Ubuntu 16.04/18.04, assicurarsi che queste dipendenze siano installate prima di avviare Eclipse.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
```

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Creare e configurare un progetto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Per aggiungere una nuova classe vuota al progetto Java, selezionare **File** > **Nuovo** > **Classe**.

1. Nella finestra **Nuova classe Java** immettere **speechsdk.quickstart** nel campo **Pacchetto** e **Main** nel campo **Nome** .

   ![Screenshot della procedura guidata Nuova classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Sostituire tutto il codice in `Main.java` con il frammento di codice seguente:

   ```java
   package speechsdk.quickstart;

   import java.io.IOException;
   import java.util.Map;
   import java.util.Scanner;
   import java.util.concurrent.ExecutionException;
   import com.microsoft.cognitiveservices.speech.*;
   import com.microsoft.cognitiveservices.speech.translation.*;

   public class Main {

       public static void translationWithMicrophoneAsync() throws InterruptedException, ExecutionException, IOException
       {
           // Creates an instance of a speech translation config with specified
           // subscription key and service region. Replace with your own subscription key
           // and service region (e.g., "westus").
           SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

           // Sets source and target language(s).
           String fromLanguage = "en-US";
           config.setSpeechRecognitionLanguage(fromLanguage);
           config.addTargetLanguage("de");

           // Sets voice name of synthesis output.
           String GermanVoice = "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)";
           config.setVoiceName(GermanVoice);

           // Creates a translation recognizer using microphone as audio input.
           TranslationRecognizer recognizer = new TranslationRecognizer(config);
           {
               // Subscribes to events.
               recognizer.recognizing.addEventListener((s, e) -> {
                   System.out.println("RECOGNIZING in '" + fromLanguage + "': Text=" + e.getResult().getText());

                   Map<String, String> map = e.getResult().getTranslations();
                   for(String element : map.keySet()) {
                       System.out.println("    TRANSLATING into '" + element + "': " + map.get(element));
                   }
               });

               recognizer.recognized.addEventListener((s, e) -> {
                   if (e.getResult().getReason() == ResultReason.TranslatedSpeech) {
                       System.out.println("RECOGNIZED in '" + fromLanguage + "': Text=" + e.getResult().getText());

                       Map<String, String> map = e.getResult().getTranslations();
                       for(String element : map.keySet()) {
                           System.out.println("    TRANSLATED into '" + element + "': " + map.get(element));
                       }
                   }
                   if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
                       System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
                       System.out.println("    Speech not translated.");
                   }
                   else if (e.getResult().getReason() == ResultReason.NoMatch) {
                       System.out.println("NOMATCH: Speech could not be recognized.");
                   }
               });

               recognizer.synthesizing.addEventListener((s, e) -> {
                   System.out.println("Synthesis result received. Size of audio data: " + e.getResult().getAudio().length);
               });

               recognizer.canceled.addEventListener((s, e) -> {
                   System.out.println("CANCELED: Reason=" + e.getReason());

                   if (e.getReason() == CancellationReason.Error) {
                       System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
                       System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
                       System.out.println("CANCELED: Did you update the subscription info?");
                   }
               });

               recognizer.sessionStarted.addEventListener((s, e) -> {
                   System.out.println("\nSession started event.");
               });

               recognizer.sessionStopped.addEventListener((s, e) -> {
                   System.out.println("\nSession stopped event.");
               });

               // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
               System.out.println("Say something...");
               recognizer.startContinuousRecognitionAsync().get();

               System.out.println("Press any key to stop");
               new Scanner(System.in).nextLine();

               recognizer.stopContinuousRecognitionAsync().get();
           }
       }

       public static void main(String[] args) {
           try {
               translationWithMicrophoneAsync();
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());
               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.

L'input vocale dal microfono verrà trascritto in tedesco e registrato nella finestra della console. Per arrestare il riconoscimento vocale, premere "Invio".

![Screenshot della console dopo il riconoscimento corretto](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio come eseguire il riconoscimento vocale da un file audio e restituire il testo tradotto come sintesi vocale, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche 

- [Guida introduttiva: Riconoscimento vocale, Java (Windows, Linux)](quickstart-java-jre.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
