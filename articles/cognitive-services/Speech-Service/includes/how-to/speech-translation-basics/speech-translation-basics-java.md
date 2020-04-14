---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 9f1fdca94edc6d3938c137185e037ba0c57b7a0f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266614"
---
## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si disponga di un account azure e di una sottoscrizione al servizio di riconoscimento vocale. Se non si dispone di un account e di un abbonamento, [provare il servizio di riconoscimento vocale gratuitamente](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, seguire le istruzioni nella sezione <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Ottenere l'SDK <span class="docon docon-navigate-external x-hidden-focus"></span> di riconoscimento vocale</a> dell'articolo Speech SDK.

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi riportati `import` in questo articolo, includere le istruzioni riportate di seguito all'inizio del file *. *File di codice Java.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Dati sensibili e variabili di ambienteSensitive data and environment variables

Il codice sorgente di esempio in questo articolo dipende dalle variabili di ambiente per l'archiviazione di dati sensibili, ad esempio la chiave e l'area di sottoscrizione della risorsa di riconoscimento vocale. Il file di `static final String` codice Java contiene due valori assegnati `SPEECH__SUBSCRIPTION__KEY` dalle `SPEECH__SERVICE__REGION`variabili di ambiente dei computer host, ovvero e . Entrambi questi campi si trovano nell'ambito della classe, rendendoli accessibili all'interno dei corpi del metodo della classe. Per ulteriori informazioni sulle variabili di ambiente, vedere [Variabili di ambiente e configurazione dell'applicazione](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
```

## <a name="create-a-speech-translation-configuration"></a>Creare una configurazione di traduzione vocale

Per chiamare il servizio di riconoscimento vocale [`SpeechTranslationConfig`][config]utilizzando l'SDK di riconoscimento vocale, è necessario creare un file . Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area geografica associata, l'endpoint, l'host o il token di autorizzazione.

> [!TIP]
> Indipendentemente dal fatto che si stia eseguendo il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento delle finalità, si creerà sempre una configurazione.

Esistono alcuni modi per inizializzare [`SpeechTranslationConfig`][config]un file :

* Con una sottoscrizione: passare una chiave e l'area associata.
* Con un endpoint: passare un endpoint del servizio di riconoscimento vocale. Una chiave o un token di autorizzazione è facoltativo.
* Con un host: passare un indirizzo host. Una chiave o un token di autorizzazione è facoltativo.
* Con un token di autorizzazione: passa un token di autorizzazione e l'area associata.

Diamo un'occhiata a come [`SpeechTranslationConfig`][config] viene creato un oggetto usando una chiave e un'area. Vedere la pagina di [supporto dell'area](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) geografica per trovare l'identificatore dell'area geografica.

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Modificare la lingua di origine

Un'attività comune di traduzione vocale è specificare la lingua di input (o origine). Diamo un'occhiata a come si sarebbe cambiare la lingua di input in italiano. Nel codice interagire con [`SpeechTranslationConfig`][config] l'istanza, chiamando il `setSpeechRecognitionLanguage` metodo .

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

La [`setSpeechRecognitionLanguage`][recognitionlang] funzione prevede una stringa di formato delle impostazioni locali della lingua. È possibile fornire qualsiasi valore nella colonna **Impostazioni locali** nell'elenco delle [impostazioni locali/lingue](../../../language-support.md)supportate.

## <a name="add-translation-language"></a>Aggiungere la lingua di traduzione

Un altro compito comune di traduzione vocale è quello di specificare le lingue di traduzione di destinazione, almeno uno è obbligatorio, ma sono supportati i multipli. Nel frammento di codice seguente, sia il francese che il tedesco come destinazioni della lingua di traduzione.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

Con ogni [`addTargetLanguage`][addlang]chiamata a , viene specificata una nuova lingua di traduzione di destinazione. In altre parole, quando il riconoscimento vocale viene riconosciuto dalla lingua di origine, ogni traduzione di destinazione è disponibile come parte dell'operazione di traduzione risultante.

## <a name="initialize-a-translation-recognizer"></a>Inizializzare un sistema di riconoscimento delle traduzioni

Dopo aver creato [`SpeechTranslationConfig`][config]un oggetto , il [`TranslationRecognizer`][recognizer]passaggio successivo consiste nell'inizializzare un file . Quando si [`TranslationRecognizer`][recognizer]inizializza un , è necessario `translationConfig`passarlo il file . L'oggetto di configurazione fornisce le credenziali richieste dal servizio di riconoscimento vocale per convalidare la richiesta.

Se riconosci il riconoscimento vocale utilizzando il microfono predefinito [`TranslationRecognizer`][recognizer] del dispositivo, ecco come dovrebbe apparire:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Se si desidera specificare il dispositivo di input audio, è necessario creare un [`AudioConfig`][audioconfig] e fornire il `audioConfig` parametro durante l'inizializzazione del [`TranslationRecognizer`][recognizer]file .

> [!TIP]
> [Scopri come ottenere l'ID del dispositivo di input audio.](../../../how-to-select-audio-input-devices.md)

In primo luogo, `AudioConfig` si farà riferimento all'oggetto come segue:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Se si desidera fornire un file audio invece di utilizzare un `audioConfig`microfono, è comunque necessario fornire un file . Tuttavia, quando [`AudioConfig`][audioconfig]si crea `fromDefaultMicrophoneInput`un oggetto , `fromWavFileInput` anziché `filename` chiamare , verrà chiamato e passato il parametro .

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Traduzione vocale

Per tradurre il riconoscimento vocale, Speech SDK si basa su un microfono o un input di file audio. Il riconoscimento vocale si verifica prima della traduzione vocale. Dopo che tutti gli oggetti sono stati inizializzati, chiamare la funzione recognize-once e ottenere il risultato.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base del riconoscimento vocale.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Sintetizzare le traduzioni

Dopo un riconoscimento vocale e una traduzione di successo, il risultato contiene tutte le traduzioni in un dizionario. La [`getTranslations`][translations] funzione restituisce un dizionario con la chiave come lingua di traduzione di destinazione e il valore è il testo tradotto. Il discorso riconosciuto può essere tradotto, quindi sintetizzato in una lingua diversa (speech-to-speech).

### <a name="event-based-synthesis"></a>Sintesi basata su eventi

L'oggetto `TranslationRecognizer` espone `synthesizing` un evento. L'evento viene generato più volte e fornisce un meccanismo per recuperare l'audio sintetizzato dal risultato del riconoscimento della traduzione. Se si sta traducendo in più lingue, vedere [sintesi manuale](#manual-synthesis). Specificare la voce di [`setVoiceName`][voicename] sintesi assegnando `synthesizing` un e fornire un gestore eventi per l'evento, ottenere l'audio. L'esempio seguente salva l'audio tradotto come file *wav.*

> [!IMPORTANT]
> La sintesi basata su eventi funziona solo con una singola traduzione, **non** aggiungere più lingue di traduzione di destinazione. Inoltre, il [`setVoiceName`][voicename] dovrebbe essere la stessa lingua della lingua di traduzione di destinazione, ad esempio; `"de"` potrebbe eseguire `"de-DE-Hedda"`il mapping a .

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Sintesi manuale

La [`getTranslations`][translations] funzione restituisce un dizionario che può essere utilizzato per sintetizzare l'audio dal testo di traduzione. Scorrere ogni traduzione e sintetizzare la traduzione. Quando si `SpeechSynthesizer` crea `SpeechConfig` un'istanza, [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] la proprietà dell'oggetto deve essere impostata sulla voce desiderata. L'esempio seguente traduce in cinque lingue e ogni traduzione viene quindi sintetizzata in un file audio nella lingua neurale corrispondente.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base della sintesi vocale](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig?view=azure-java-stable
[audioconfig]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig?view=azure-java-stable
[recognizer]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer?view=azure-java-stable
[recognitionlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable
[addlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage?view=azure-java-stable
[translations]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations?view=azure-java-stable
[voicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename?view=azure-java-stable
[speechsynthesisvoicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename?view=azure-java-stable
