---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: 7575e174f1f47d55c507fdbf0386fbd578649839
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499202"
---
Una delle principali funzionalità del servizio Voce è la possibilità di riconoscere e trascrivere la voce umana, ovvero di convertire la voce in testo scritto. Questa guida di avvio rapido illustra come usare Speech SDK in app e prodotti per eseguire la conversione della voce in testo scritto di alta qualità.

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, usare le istruzioni seguenti:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig). Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione. Creare una classe [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) usando la chiave e l'area. Per trovare l'identificatore di area, vedere la pagina del [supporto a livello di area](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk).

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;

auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Esistono alcuni altri modi per inizializzare [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig):

* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

## <a name="recognize-from-microphone"></a>Riconoscimento da microfono

Per eseguire il riconoscimento vocale usando il microfono del dispositivo, creare un oggetto `AudioConfig` usando `FromDefaultMicrophoneInput()`. Quindi, inizializzare una classe [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer), passando la classe `audioConfig` e `config`.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);

cout << "Speak into your microphone." << std::endl;
auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

Se si vuole usare un dispositivo di input audio *specifico* , è necessario specificare l'ID del dispositivo nella classe `AudioConfig`. Informazioni su [come ottenere l'ID del dispositivo](../../../how-to-select-audio-input-devices.md) di input audio.

## <a name="recognize-from-file"></a>Riconoscimento da file

Se si vuole specificare un file audio invece di usare un microfono per il riconoscimento vocale, è comunque necessario creare un oggetto `AudioConfig`. Tuttavia, quando si crea [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig), invece di chiamare `FromDefaultMicrophoneInput()`, si chiamerà `FromWavFileInput()` e si passerà il percorso file.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);

auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

## <a name="recognize-speech"></a>Riconoscimento vocale

La [classe Recognizer](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) per Speech SDK per C++ espone alcuni metodi che è possibile usare per il riconoscimento vocale.

### <a name="single-shot-recognition"></a>Riconoscimento singolo

Il riconoscimento singolo riconosce in modo asincrono una singola espressione. La fine di una singola espressione viene determinata restando in ascolto del silenzio al termine o finché non vengono elaborati al massimo 15 secondi di audio. Ecco un esempio di riconoscimento singolo asincrono usando [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync):

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Per gestire il risultato è necessario scrivere del codice. Questo esempio valuta [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason):

* Stampa il risultato del riconoscimento: `ResultReason::RecognizedSpeech`
* Se non esiste una corrispondenza di riconoscimento, informa l'utente: `ResultReason::NoMatch`
* Se viene rilevato un errore, stampa il messaggio di errore: `ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Riconoscimento continuo

Il riconoscimento continuo è un po' più impegnativo rispetto al riconoscimento singolo. Per ottenere i risultati del riconoscimento, è necessario sottoscrivere gli eventi `Recognizing`, `Recognized` e `Canceled`. Per arrestare il riconoscimento, è necessario chiamare [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Ecco un esempio di riconoscimento continuo eseguito su un file di input audio.

Per iniziare, definire l'input e inizializzare [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer):

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Successivamente, creare una variabile per gestire lo stato del riconoscimento vocale. Per iniziare, verrà dichiarato un oggetto `promise<void>`, dal momento che all'inizio si può ovviamente presupporre che il riconoscimento non sia terminato.

```cpp
promise<void> recognitionEnd;
```

Verranno sottoscritti gli eventi inviati da [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer).

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): segnale per gli eventi contenenti i risultati del riconoscimento intermedio.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): segnale per gli eventi contenenti i risultati del riconoscimento finale, che indicano un tentativo di riconoscimento riuscito.
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): segnale per gli eventi che indicano la fine di una sessione di riconoscimento (operazione).
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): segnale per gli eventi contenenti risultati di riconoscimento annullati (a indicare un tentativo di riconoscimento annullato in seguito a una richiesta di annullamento diretta o, in alternativa, a un errore di trasporto o di protocollo).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Con tutti gli elementi configurati, è possibile chiamare [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync).

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Modalità dettatura

Quando si usa il riconoscimento continuo, è possibile abilitare l'elaborazione della dettatura usando la corrispondente funzione. In questa modalità l'istanza di SpeechConfig interpreta le descrizioni testuali delle strutture di frasi, ad esempio la punteggiatura. Ad esempio, l'espressione "Vivi in città punto interrogativo" verrebbe interpretata come "Vivi in città?".

Per abilitare la modalità di dettatura, usare il metodo [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) in [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Cambiare la lingua di origine

Un'attività comune per il riconoscimento vocale è la specifica della lingua di input (o di origine). Vediamo come cambiare la lingua di input in tedesco. Nel codice trovare [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig), quindi aggiungere questa riga al di sotto.

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) è un parametro che accetta una stringa come argomento. È possibile specificare qualsiasi valore nell'elenco di [impostazioni locali/lingue supportate](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Migliorare l'accuratezza del riconoscimento

È possibile migliorare l'accuratezza del riconoscimento in vari modi con Speech SDK. Esaminiamo gli elenchi di frasi. Gli elenchi di frasi vengono usati per identificare frasi note nei dati audio, ad esempio il nome di una persona o una specifica località. All'elenco di frasi è possibile aggiungere singole parole o frasi complete. Durante il riconoscimento, se nell'audio è inclusa una corrispondenza esatta per l'intera frase, viene aggiunta una voce nell'elenco di frasi. Se non si trova una corrispondenza esatta, il riconoscimento non è assistito.

> [!IMPORTANT]
> La funzionalità dell'elenco di frasi è disponibile solo in inglese.

Per usare un elenco di frasi, creare prima di tutto un oggetto [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar), quindi aggiungere parole o frasi specifiche con [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase).

Tutte le modifiche apportate a [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) diventano effettive al successivo riconoscimento o dopo una riconnessione al servizio Voce.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Se è necessario cancellare l'elenco di frasi: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Altre opzioni per migliorare l'accuratezza del riconoscimento

Gli elenchi di frasi sono solo una delle opzioni disponibili per migliorare l'accuratezza del riconoscimento. È anche possibile: 

* [Migliorare l'accuratezza con Riconoscimento vocale personalizzato](../../../how-to-custom-speech.md)
* [Migliorare l'accuratezza con i modelli di tenant](../../../tutorial-tenant-model.md)
