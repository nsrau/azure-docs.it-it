---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 1042383e285780f5ee9297b1c87615bbdd2fe1b6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88925259"
---
## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, seguire le istruzioni <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">riportate nella sezione Get the <span class="docon docon-navigate-external x-hidden-focus"></span> Speech SDK</a> dell'articolo _About The Speech SDK_ .

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi in questo articolo, includere le istruzioni seguenti nella `using` parte superiore del file *Program.cs* .

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Variabili di ambiente e dati sensibili

Il codice sorgente di esempio in questo articolo dipende dalle variabili di ambiente per l'archiviazione di dati sensibili, ad esempio la chiave di sottoscrizione della risorsa vocale e l'area. La `Program` classe contiene due `static readonly string` valori assegnati dalle variabili di ambiente dei computer host, ovvero `SPEECH__SUBSCRIPTION__KEY` e `SPEECH__SERVICE__REGION` . Entrambi questi campi si trovano nell'ambito della classe, rendendoli accessibili nei corpi dei metodi della classe. Per altre informazioni sulle variabili di ambiente, vedere [variabili di ambiente e configurazione dell'applicazione](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
```

## <a name="create-a-speech-translation-configuration"></a>Creare una configurazione di traduzione vocale

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechTranslationConfig`][config]. Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione.

> [!TIP]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

Esistono diversi modi per inizializzare [`SpeechTranslationConfig`][config]:

* Con una sottoscrizione: passare una chiave e l'area associata.
* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

Vediamo come viene creata una classe [`SpeechTranslationConfig`][config] usando una chiave e un'area. Per trovare l'identificatore di area, vedere la pagina del [supporto a livello di area](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Cambiare la lingua di origine

Una delle attività comuni della traduzione vocale è la specifica della lingua di input (o di origine). Vediamo come cambiare la lingua di input in italiano. Nel codice interagire con l' [`SpeechTranslationConfig`][config] istanza, assegnando alla `SpeechRecognitionLanguage` Proprietà.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

La proprietà [`SpeechRecognitionLanguage`][recognitionlang] si aspetta una stringa in formato è prevista una stringa di formato lingua-impostazioni locali. È possibile specificare qualsiasi valore nella colonna **Locale** nell'elenco di [impostazioni locali/lingue supportate](../../../language-support.md).

## <a name="add-translation-language"></a>Aggiungi lingua di traduzione

Un'altra attività comune di traduzione vocale è quella di specificare i linguaggi di traduzione di destinazione, almeno uno è obbligatorio ma sono supportati più multipli. Il frammento di codice seguente imposta sia il francese che il tedesco come destinazioni del linguaggio di traduzione.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Con ogni chiamata a [`AddTargetLanguage`][addlang] , viene specificato un nuovo linguaggio di conversione di destinazione. In altre parole, quando la voce vocale viene riconosciuta dalla lingua di origine, ogni conversione di destinazione è disponibile come parte dell'operazione di conversione risultante.

## <a name="initialize-a-translation-recognizer"></a>Inizializzare un riconoscimento di traduzione

Dopo aver creato una classe [`SpeechTranslationConfig`][config], il passaggio successivo consiste nell'inizializzare [`TranslationRecognizer`][recognizer]. Quando si Inizializza [`TranslationRecognizer`][recognizer], sarà necessario passare `translationConfig`. L'oggetto di configurazione fornisce le credenziali richieste dal servizio di riconoscimento vocale per convalidare la richiesta.

Se si esegue il riconoscimento vocale usando il microfono predefinito del dispositivo, [`TranslationRecognizer`][recognizer] avrà questo aspetto:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Se si vuole specificare il dispositivo di input audio, è necessario creare [`AudioConfig`][audioconfig] e specificare il parametro `audioConfig` quando si inizializza [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Informazioni su come ottenere l'ID del dispositivo di input audio](../../../how-to-select-audio-input-devices.md).

In primo luogo, si farà riferimento all' `AudioConfig` oggetto come segue:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Se si vuole specificare un file audio invece di usare un microfono, è comunque necessario fornire `audioConfig`. Tuttavia, quando si crea [`AudioConfig`][audioconfig], invece di chiamare `FromDefaultMicrophoneInput`, si chiamerà `FromWavFileInput` e si passerà il parametro `filename`.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Traduzione vocale

Per tradurre la voce, l'SDK di riconoscimento vocale si basa su un microfono o un input di file audio. Il riconoscimento vocale si verifica prima della traduzione vocale. Dopo l'inizializzazione di tutti gli oggetti, chiamare la funzione Recognize-once e ottenere il risultato.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base del riconoscimento vocale](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Sintetizza le traduzioni

Dopo un riconoscimento vocale e una traduzione con esito positivo, il risultato contiene tutte le traduzioni in un dizionario. La [`Translations`][translations] chiave del dizionario è il linguaggio di conversione di destinazione e il valore è il testo tradotto. Il riconoscimento vocale riconosciuto può essere convertito, quindi sintetizzato in un linguaggio diverso (sintesi vocale).

### <a name="event-based-synthesis"></a>Sintesi basata su eventi

L' `TranslationRecognizer` oggetto espone un `Synthesizing` evento. L'evento viene generato più volte e fornisce un meccanismo per recuperare l'audio sintetizzato dal risultato del riconoscimento della traduzione. Se si esegue la conversione in più lingue, vedere [sintesi manuale](#manual-synthesis). Specificare la voce di sintesi assegnando un oggetto [`VoiceName`][voicename] e fornire un gestore eventi per l' `Synthesizing` evento, ottenere l'audio. Nell'esempio seguente viene salvato l'audio tradotto come file *WAV* .

> [!IMPORTANT]
> La sintesi basata su eventi funziona solo con una singola traduzione. **non** aggiungere più lingue di traduzione di destinazione. Inoltre, [`VoiceName`][voicename] deve essere la stessa lingua del linguaggio di traduzione di destinazione, ad esempio `"de"` . può eseguire il mapping a `"de-DE-Hedda"` .

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Sintesi manuale

Il [`Translations`][translations] dizionario può essere usato per sintetizzare l'audio dal testo di traduzione. Scorrere ogni traduzione e sintetizzare la traduzione. Quando si crea un' `SpeechSynthesizer` istanza, l' `SpeechConfig` oggetto deve avere la [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] proprietà impostata sulla voce desiderata. Nell'esempio seguente viene convertito in cinque lingue e ogni conversione viene quindi sintetizzata in un file audio nel linguaggio neurale corrispondente.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base sulla sintesi vocale](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
