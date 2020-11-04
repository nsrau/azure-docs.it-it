---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 26b5a4c920833669ae0ea44ae5f69c3a72b5a522
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135946"
---
Una delle principali funzionalità del servizio Voce è la possibilità di riconoscere e trascrivere la voce umana, ovvero di convertire la voce in testo scritto. Questa guida di avvio rapido illustra come usare Speech SDK in app e prodotti per eseguire la conversione della voce in testo scritto di alta qualità.

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Se si vuole solo il nome del pacchetto per iniziare, eseguire `Install-Package Microsoft.CognitiveServices.Speech` nella console NuGet.

Per le istruzioni di installazione specifiche per la piattaforma, vedere i collegamenti seguenti:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet). Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione. Creare una classe [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) usando la chiave e l'area. Per trovare l'identificatore di area, vedere la pagina del [supporto a livello di area](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk).

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Esistono alcuni altri modi per inizializzare una classe [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet):

* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

## <a name="recognize-from-microphone"></a>Riconoscimento da microfono

Per eseguire il riconoscimento vocale usando il microfono del dispositivo, creare un oggetto `AudioConfig` usando `FromDefaultMicrophoneInput()`. Quindi, inizializzare una classe [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet), passando la classe `audioConfig` e `speechConfig`.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromMic(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        Console.WriteLine("Speak into your microphone.");
        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromMic(speechConfig);
    }
}
```

Se si vuole usare un dispositivo di input audio *specifico* , è necessario specificare l'ID del dispositivo nella classe `AudioConfig`. Informazioni su [come ottenere l'ID del dispositivo](../../../how-to-select-audio-input-devices.md) di input audio.

## <a name="recognize-from-file"></a>Riconoscimento da file

Se si vuole specificare un file audio invece di un microfono per il riconoscimento vocale, è comunque necessario creare un oggetto `AudioConfig`. Tuttavia, quando si crea [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet), invece di chiamare `FromDefaultMicrophoneInput()`, si chiamerà `FromWavFileInput()` e si passerà il percorso file.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromFile(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromWavFileInput("PathToFile.wav");
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromFile(speechConfig);
    }
}
```

## <a name="recognize-from-in-memory-stream"></a>Riconoscimento dal flusso in memoria

Per molti casi d'uso, è probabile che i dati audio provengano dall'archiviazione BLOB o che siano già in memoria come `byte[]` o una struttura di dati non elaborati simile. L'esempio seguente usa una classe [`PushAudioInputStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudioinputstream?view=azure-dotnet) per riconoscere la voce, cioè fondamentalmente un flusso di memoria astratto. Nel codice di esempio vengono eseguite le operazioni seguenti:

* Scrive i dati audio non elaborati (PCM) nella classe `PushAudioInputStream` usando la funzione `Write()`, che accetta un `byte[]`.
* Legge un file `.wav` usando un `FileReader` a scopo dimostrativo, ma se si hanno già dati audio in un `byte[]`, è possibile passare direttamente alla scrittura del contenuto nel flusso di input.
* Il formato predefinito è 16 bit, 16 KHz e PCM mono. Per personalizzare il formato, è possibile passare un oggetto [`AudioStreamFormat`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audiostreamformat?view=azure-dotnet) alla classe `CreatePushStream()` usando la funzione statica `AudioStreamFormat.GetWaveFormatPCM(sampleRate, (byte)bitRate, (byte)channels)`.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromStream(SpeechConfig speechConfig)
    {
        var reader = new BinaryReader(File.OpenRead("PathToFile.wav"));
        using var audioInputStream = AudioInputStream.CreatePushStream();
        using var audioConfig = AudioConfig.FromStreamInput(audioInputStream);
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        byte[] readBytes;
        do
        {
            readBytes = reader.ReadBytes(1024);
            audioInputStream.Write(readBytes, readBytes.Length);
        } while (readBytes.Length > 0);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromStream(speechConfig);
    }
}
```

L'uso di un flusso push come input presuppone che i dati audio siano un PCM non elaborato, ad esempio ignorano le intestazioni.
L'API continuerà a funzionare in determinati casi se l'intestazione non è stata ignorata, ma per ottenere risultati ottimali è consigliabile implementare la logica per leggere le intestazioni, in modo che il `byte[]` cominci all' *inizio dei dati audio*.

## <a name="error-handling"></a>Gestione degli errori

Gli esempi precedenti ottengono semplicemente il testo riconosciuto da `result.text`, ma per gestire gli errori e altre risposte è necessario scrivere del codice per elaborare il risultato. Il seguente codice valuta la proprietà [`result.Reason`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?view=azure-dotnet&preserve-view=true) e:

* Stampa il risultato del riconoscimento: `ResultReason.RecognizedSpeech`
* Se non esiste una corrispondenza di riconoscimento, informa l'utente: `ResultReason.NoMatch`
* Se viene rilevato un errore, stampa il messaggio di errore: `ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

## <a name="continuous-recognition"></a>Riconoscimento continuo

Gli esempi precedenti usano il riconoscimento singolo, che riconosce una singola espressione. La fine di una singola espressione viene determinata restando in ascolto del silenzio al termine o finché non vengono elaborati al massimo 15 secondi di audio.

Al contrario, si usa il riconoscimento continuo quando si vuole **controllare** il momento in cui interrompere il riconoscimento. Per ottenere i risultati del riconoscimento, è necessario sottoscrivere gli eventi `Recognizing`, `Recognized` e `Canceled`. Per arrestare il riconoscimento, è necessario chiamare [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet&preserve-view=true). Ecco un esempio di riconoscimento continuo eseguito su un file di input audio.

Per iniziare, definire l'input e inizializzare un [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet&preserve-view=true):

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Quindi, creare una proprietà `TaskCompletionSource<int>` per gestire lo stato del riconoscimento vocale.

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

Infine, sottoscrivere gli eventi inviati dal [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet).

* [`Recognizing`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-dotnet&preserve-view=true): segnale per gli eventi contenenti i risultati del riconoscimento intermedio.
* [`Recognized`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-dotnet&preserve-view=true): segnale per gli eventi contenenti i risultati del riconoscimento finale, che indicano un tentativo di riconoscimento riuscito.
* [`SessionStopped`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-dotnet&preserve-view=true): segnale per gli eventi che indicano la fine di una sessione di riconoscimento (operazione).
* [`Canceled`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-dotnet&preserve-view=true): segnale per gli eventi contenenti risultati di riconoscimento annullati (a indicare un tentativo di riconoscimento annullato in seguito a una richiesta di annullamento diretta o, in alternativa, a un errore di trasporto o di protocollo).

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

Al termine della configurazione, chiamare `StartContinuousRecognitionAsync` per avviare il riconoscimento.

```csharp
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// make the following call at some point to stop recognition.
// await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Modalità dettatura

Quando si usa il riconoscimento continuo, è possibile abilitare l'elaborazione della dettatura usando la corrispondente funzione. In questa modalità l'istanza di SpeechConfig interpreta le descrizioni testuali delle strutture di frasi, ad esempio la punteggiatura. Ad esempio, l'espressione "Vivi in città punto interrogativo" verrebbe interpretata come "Vivi in città?".

Per abilitare la modalità di dettatura, usare il metodo [`EnableDictation`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet&preserve-view=true) in [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true).

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>Cambiare la lingua di origine

Un'attività comune per il riconoscimento vocale è la specifica della lingua di input (o di origine). Vediamo come cambiare la lingua di input in italiano. Nel codice trovare [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true), quindi aggiungere questa riga al di sotto.

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

La proprietà [`SpeechRecognitionLanguage`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet&preserve-view=true) si aspetta una stringa in formato è prevista una stringa di formato lingua-impostazioni locali. È possibile specificare qualsiasi valore nella colonna **Locale** nell'elenco di [impostazioni locali/lingue supportate](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Migliorare l'accuratezza del riconoscimento

È possibile migliorare l'accuratezza del riconoscimento in vari modi con Speech SDK. Esaminiamo gli elenchi di frasi. Gli elenchi di frasi vengono usati per identificare frasi note nei dati audio, ad esempio il nome di una persona o una specifica località. All'elenco di frasi è possibile aggiungere singole parole o frasi complete. Durante il riconoscimento, se nell'audio è inclusa una corrispondenza esatta per l'intera frase, viene aggiunta una voce nell'elenco di frasi. Se non si trova una corrispondenza esatta, il riconoscimento non è assistito.

> [!IMPORTANT]
> La funzionalità dell'elenco di frasi è disponibile solo in inglese.

Per usare un elenco di frasi, creare prima di tutto un oggetto [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet&preserve-view=true), quindi aggiungere parole o frasi specifiche con [`AddPhrase`](https://docs.microsoft.com//dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-dotnet&preserve-view=true).

Tutte le modifiche apportate a [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet&preserve-view=true) diventano effettive al successivo riconoscimento o dopo una riconnessione al servizio Voce.

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

Se è necessario cancellare l'elenco di frasi: 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Altre opzioni per migliorare l'accuratezza del riconoscimento

Gli elenchi di frasi sono solo una delle opzioni disponibili per migliorare l'accuratezza del riconoscimento. È anche possibile: 

* [Migliorare l'accuratezza con Riconoscimento vocale personalizzato](../../../how-to-custom-speech.md)
* [Migliorare l'accuratezza con i modelli di tenant](../../../tutorial-tenant-model.md)