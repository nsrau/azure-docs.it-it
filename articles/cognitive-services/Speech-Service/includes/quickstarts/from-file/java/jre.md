---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: f40ccbd87d9e889e88b870cc4aed805e0dc2ea66
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376810"
---
## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../overview.md#try-the-speech-service-for-free)
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Per aggiungere una nuova classe vuota al progetto Java, selezionare **File** > **Nuovo** > **Classe**.

1. Nella finestra **Nuova classe Java** immettere **speechsdk.quickstart** nel campo **Pacchetto** e **Main** nel campo **Nome** .

   ![Screenshot della procedura guidata Nuova classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Sostituire tutto il codice in `Main.java` con il frammento di codice seguente:

   ```java
   package speechsdk.quickstart;

   import java.util.concurrent.Future;
   import com.microsoft.cognitiveservices.speech.*;

   /**
    * Quickstart: recognize speech using the Speech SDK for Java.
    */
   public class Main {

       /**
        * @param args Arguments are ignored in this sample.
        */
       public static void main(String[] args) {
           try {
               // Replace below with your own subscription key
               String speechSubscriptionKey = "YourSubscriptionKey";

               // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
               String serviceRegion = "YourServiceRegion";

               // Replace below with your own filename.
               String audioFileName = "whatstheweatherlike.wav";

               int exitCode = 1;
               SpeechConfig config = SpeechConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
               assert(config != null);

               AudioConfig audioInput = AudioConfig.fromWavFileInput(audioFileName);
               assert(audioInput != null);

               SpeechRecognizer reco = new SpeechRecognizer(config, audioInput);
               assert(reco != null);

               System.out.println("Recognizing first result...");

               Future<SpeechRecognitionResult> task = reco.recognizeOnceAsync();
               assert(task != null);

               SpeechRecognitionResult result = task.get();
               assert(result != null);

               switch (result.getReason()) {
                   case ResultReason.RecognizedSpeech: {
                           System.out.println("We recognized: " + result.getText());
                           exitCode = 0;
                       }
                       break;
                   case ResultReason.NoMatch:
                       System.out.println("NOMATCH: Speech could not be recognized.");
                       break;
                   case ResultReason.Canceled: {
                           CancellationDetails cancellation = CancellationDetails.fromResult(result);
                           System.out.println("CANCELED: Reason=" + cancellation.getReason());
        
                           if (cancellation.getReason() == CancellationReason.Error) {
                               System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                               System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                               System.out.println("CANCELED: Did you update the subscription info?");
                           }
                       }
                       break;
               }

               reco.close();

               System.exit(exitCode);
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());

               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione.

1. Sostituire la stringa `whatstheweatherlike.wav` con il proprio nome file.

1. Salvare le modifiche apportate al progetto.

> [!NOTE]
> Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.
I primi 15 secondi di input vocale dal file audio verranno riconosciuti e registrati nella finestra della console.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]