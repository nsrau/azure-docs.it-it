---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: badc3cd372ca992a83678ef8d32ac76966ba9b94
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499253"
---
Una delle principali funzionalità del servizio Voce è la possibilità di riconoscere e trascrivere la voce umana, ovvero di convertire la voce in testo scritto. Questa guida di avvio rapido illustra come usare Speech SDK in app e prodotti per eseguire la conversione della voce in testo scritto di alta qualità.

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, usare le istruzioni seguenti:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable). Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione. Creare una classe [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) usando la chiave e l'area. Per trovare l'identificatore di area, vedere la pagina del [supporto a livello di area](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk).

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;

SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Esistono alcuni altri modi per inizializzare una classe [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable):

* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

## <a name="recognize-from-microphone"></a>Riconoscimento da microfono

Per eseguire il riconoscimento vocale usando il microfono del dispositivo, creare un oggetto `AudioConfig` usando `fromDefaultMicrophoneInput()`. Quindi, inizializzare una classe [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable), passando la classe `audioConfig` e `config`.

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);

System.out.println("Speak into your microphone.");
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
System.out.println("RECOGNIZED: Text=" + result.getText());
```

Se si vuole usare un dispositivo di input audio *specifico* , è necessario specificare l'ID del dispositivo nella classe `AudioConfig`. Informazioni su [come ottenere l'ID del dispositivo](../../../how-to-select-audio-input-devices.md) di input audio.

## <a name="recognize-from-file"></a>Riconoscimento da file

Se si vuole specificare un file audio invece di usare un microfono per il riconoscimento vocale, è comunque necessario creare un oggetto `AudioConfig`. Tuttavia, quando si crea [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable), invece di chiamare `fromDefaultMicrophoneInput()`, si chiamerà `fromWavFileInput()` e si passerà il percorso file.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);

Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
System.out.println("RECOGNIZED: Text=" + result.getText());
```

## <a name="recognize-speech"></a>Riconoscimento vocale

La [classe Recognizer](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable&preserve-view=true) per Speech SDK per Java espone alcuni metodi che è possibile usare per il riconoscimento vocale.

### <a name="single-shot-recognition"></a>Riconoscimento singolo

Il riconoscimento singolo riconosce in modo asincrono una singola espressione. La fine di una singola espressione viene determinata restando in ascolto del silenzio al termine o finché non vengono elaborati al massimo 15 secondi di audio. Ecco un esempio di riconoscimento singolo asincrono usando [`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable):

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

Per gestire il risultato è necessario scrivere del codice. Questo esempio valuta [`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable&preserve-view=true):

* Stampa il risultato del riconoscimento: `ResultReason.RecognizedSpeech`
* Se non esiste una corrispondenza di riconoscimento, informa l'utente: `ResultReason.NoMatch`
* Se viene rilevato un errore, stampa il messaggio di errore: `ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
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
```

### <a name="continuous-recognition"></a>Riconoscimento continuo

Il riconoscimento continuo è un po' più impegnativo rispetto al riconoscimento singolo. Per ottenere i risultati del riconoscimento, è necessario sottoscrivere gli eventi `recognizing`, `recognized` e `canceled`. Per arrestare il riconoscimento, è necessario chiamare [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync). Ecco un esempio di riconoscimento continuo eseguito su un file di input audio.

Per iniziare, definire l'input e inizializzare [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable&preserve-view=true):

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Successivamente, creare una variabile per gestire lo stato del riconoscimento vocale. Per iniziare, verrà dichiarato un oggetto `Semaphore` nell'ambito della classe.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Verranno sottoscritti gli eventi inviati da [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable&preserve-view=true).

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable&preserve-view=true): segnale per gli eventi contenenti i risultati del riconoscimento intermedio.
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable&preserve-view=true): segnale per gli eventi contenenti i risultati del riconoscimento finale, che indicano un tentativo di riconoscimento riuscito.
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable&preserve-view=true): segnale per gli eventi che indicano la fine di una sessione di riconoscimento (operazione).
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable&preserve-view=true): segnale per gli eventi contenenti risultati di riconoscimento annullati (a indicare un tentativo di riconoscimento annullato in seguito a una richiesta di annullamento diretta o, in alternativa, a un errore di trasporto o di protocollo).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Con tutti gli elementi configurati, è possibile chiamare [`startContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync).

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Modalità dettatura

Quando si usa il riconoscimento continuo, è possibile abilitare l'elaborazione della dettatura usando la corrispondente funzione. In questa modalità l'istanza di SpeechConfig interpreta le descrizioni testuali delle strutture di frasi, ad esempio la punteggiatura. Ad esempio, l'espressione "Vivi in città punto interrogativo" verrebbe interpretata come "Vivi in città?".

Per abilitare la modalità di dettatura, usare il metodo [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable&preserve-view=true) in [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable&preserve-view=true).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Cambiare la lingua di origine

Un'attività comune per il riconoscimento vocale è la specifica della lingua di input (o di origine). Vediamo come cambiare la lingua di input in francese. Nel codice trovare [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable&preserve-view=true), quindi aggiungere questa riga al di sotto.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable&preserve-view=true) è un parametro che accetta una stringa come argomento. È possibile specificare qualsiasi valore nell'elenco di [impostazioni locali/lingue supportate](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Migliorare l'accuratezza del riconoscimento

È possibile migliorare l'accuratezza del riconoscimento in vari modi con Speech SDK. Esaminiamo gli elenchi di frasi. Gli elenchi di frasi vengono usati per identificare frasi note nei dati audio, ad esempio il nome di una persona o una specifica località. All'elenco di frasi è possibile aggiungere singole parole o frasi complete. Durante il riconoscimento, se nell'audio è inclusa una corrispondenza esatta per l'intera frase, viene aggiunta una voce nell'elenco di frasi. Se non si trova una corrispondenza esatta, il riconoscimento non è assistito.

> [!IMPORTANT]
> La funzionalità dell'elenco di frasi è disponibile solo in inglese.

Per usare un elenco di frasi, creare prima di tutto un oggetto [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable&preserve-view=true), quindi aggiungere parole o frasi specifiche con [`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_).

Tutte le modifiche apportate a [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable&preserve-view=true) diventano effettive al successivo riconoscimento o dopo una riconnessione al servizio Voce.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Se è necessario cancellare l'elenco di frasi: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Altre opzioni per migliorare l'accuratezza del riconoscimento

Gli elenchi di frasi sono solo una delle opzioni disponibili per migliorare l'accuratezza del riconoscimento. È anche possibile: 

* [Migliorare l'accuratezza con Riconoscimento vocale personalizzato](../../../how-to-custom-speech.md)
* [Migliorare l'accuratezza con i modelli di tenant](../../../tutorial-tenant-model.md)
