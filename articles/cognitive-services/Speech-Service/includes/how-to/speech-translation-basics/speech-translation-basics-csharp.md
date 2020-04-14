---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 0f5570f1c80fdecb4db6c8baf22ad05f1e4a32a6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266658"
---
## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si disponga di un account azure e di una sottoscrizione al servizio di riconoscimento vocale. Se non si dispone di un account e di un abbonamento, [provare il servizio di riconoscimento vocale gratuitamente](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, seguire le istruzioni nella sezione <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Ottenere l'SDK <span class="docon docon-navigate-external x-hidden-focus"></span> di riconoscimento vocale</a> dell'articolo Speech SDK.

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi in questo `using` articolo, includere le istruzioni seguenti all'inizio del file *di Program.cs.*

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

## <a name="sensitive-data-and-environment-variables"></a>Dati sensibili e variabili di ambienteSensitive data and environment variables

Il codice sorgente di esempio in questo articolo dipende dalle variabili di ambiente per l'archiviazione di dati sensibili, ad esempio la chiave e l'area di sottoscrizione della risorsa di riconoscimento vocale. La `Program` classe `static readonly string` contiene due valori assegnati dalle variabili `SPEECH__SUBSCRIPTION__KEY` di `SPEECH__SERVICE__REGION`ambiente dei computer host, ovvero e . Entrambi questi campi si trovano nell'ambito della classe, rendendoli accessibili all'interno dei corpi del metodo della classe. Per ulteriori informazioni sulle variabili di ambiente, vedere [Variabili di ambiente e configurazione dell'applicazione](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

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

Per chiamare il servizio di riconoscimento vocale [`SpeechTranslationConfig`][config]utilizzando l'SDK di riconoscimento vocale, è necessario creare un file . Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area geografica associata, l'endpoint, l'host o il token di autorizzazione.

> [!TIP]
> Indipendentemente dal fatto che si stia eseguendo il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento delle finalità, si creerà sempre una configurazione.

Esistono alcuni modi per inizializzare [`SpeechTranslationConfig`][config]un file :

* Con una sottoscrizione: passare una chiave e l'area associata.
* Con un endpoint: passare un endpoint del servizio di riconoscimento vocale. Una chiave o un token di autorizzazione è facoltativo.
* Con un host: passare un indirizzo host. Una chiave o un token di autorizzazione è facoltativo.
* Con un token di autorizzazione: passa un token di autorizzazione e l'area associata.

Diamo un'occhiata a come [`SpeechTranslationConfig`][config] viene creato un oggetto usando una chiave e un'area. Vedere la pagina di [supporto dell'area](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) geografica per trovare l'identificatore dell'area geografica.

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

## <a name="change-source-language"></a>Modificare la lingua di origine

Un'attività comune di traduzione vocale è specificare la lingua di input (o origine). Diamo un'occhiata a come si sarebbe cambiare la lingua di input in italiano. Nel codice, interagire [`SpeechTranslationConfig`][config] con l'istanza, `SpeechRecognitionLanguage` assegnando alla proprietà.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

La [`SpeechRecognitionLanguage`][recognitionlang] proprietà prevede una stringa di formato delle impostazioni locali della lingua. È possibile fornire qualsiasi valore nella colonna **Impostazioni locali** nell'elenco delle [impostazioni locali/lingue](../../../language-support.md)supportate.

## <a name="add-translation-language"></a>Aggiungere la lingua di traduzione

Un altro compito comune di traduzione vocale è quello di specificare le lingue di traduzione di destinazione, almeno uno è obbligatorio, ma sono supportati i multipli. Nel frammento di codice seguente, sia il francese che il tedesco come destinazioni della lingua di traduzione.

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

Con ogni [`AddTargetLanguage`][addlang]chiamata a , viene specificata una nuova lingua di traduzione di destinazione. In altre parole, quando il riconoscimento vocale viene riconosciuto dalla lingua di origine, ogni traduzione di destinazione è disponibile come parte dell'operazione di traduzione risultante.

## <a name="initialize-a-translation-recognizer"></a>Inizializzare un sistema di riconoscimento delle traduzioni

Dopo aver creato [`SpeechTranslationConfig`][config]un oggetto , il [`TranslationRecognizer`][recognizer]passaggio successivo consiste nell'inizializzare un file . Quando si [`TranslationRecognizer`][recognizer]inizializza un , è necessario `translationConfig`passarlo il file . L'oggetto di configurazione fornisce le credenziali richieste dal servizio di riconoscimento vocale per convalidare la richiesta.

Se riconosci il riconoscimento vocale utilizzando il microfono predefinito [`TranslationRecognizer`][recognizer] del dispositivo, ecco come dovrebbe apparire:

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

Se si desidera specificare il dispositivo di input audio, è necessario creare un [`AudioConfig`][audioconfig] e fornire il `audioConfig` parametro durante l'inizializzazione del [`TranslationRecognizer`][recognizer]file .

> [!TIP]
> [Scopri come ottenere l'ID del dispositivo di input audio.](../../../how-to-select-audio-input-devices.md)

In primo luogo, `AudioConfig` si farà riferimento all'oggetto come segue:

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

Se si desidera fornire un file audio invece di utilizzare un `audioConfig`microfono, è comunque necessario fornire un file . Tuttavia, quando [`AudioConfig`][audioconfig]si crea `FromDefaultMicrophoneInput`un oggetto , `FromWavFileInput` anziché `filename` chiamare , verrà chiamato e passato il parametro .

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

Per tradurre il riconoscimento vocale, Speech SDK si basa su un microfono o un input di file audio. Il riconoscimento vocale si verifica prima della traduzione vocale. Dopo che tutti gli oggetti sono stati inizializzati, chiamare la funzione recognize-once e ottenere il risultato.

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

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base del riconoscimento vocale.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Sintetizzare le traduzioni

Dopo un riconoscimento vocale e una traduzione di successo, il risultato contiene tutte le traduzioni in un dizionario. La [`Translations`][translations] chiave del dizionario è la lingua di traduzione di destinazione e il valore è il testo tradotto. Il discorso riconosciuto può essere tradotto, quindi sintetizzato in una lingua diversa (speech-to-speech).

### <a name="event-based-synthesis"></a>Sintesi basata su eventi

L'oggetto `TranslationRecognizer` espone `Synthesizing` un evento. L'evento viene generato più volte e fornisce un meccanismo per recuperare l'audio sintetizzato dal risultato del riconoscimento della traduzione. Se si sta traducendo in più lingue, vedere [sintesi manuale](#manual-synthesis). Specificare la voce di [`VoiceName`][voicename] sintesi assegnando `Synthesizing` un e fornire un gestore eventi per l'evento, ottenere l'audio. L'esempio seguente salva l'audio tradotto come file *wav.*

> [!IMPORTANT]
> La sintesi basata su eventi funziona solo con una singola traduzione, **non** aggiungere più lingue di traduzione di destinazione. Inoltre, il [`VoiceName`][voicename] dovrebbe essere la stessa lingua della lingua di traduzione di destinazione, ad esempio; `"de"` potrebbe eseguire `"de-DE-Hedda"`il mapping a .

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

Il [`Translations`][translations] dizionario può essere utilizzato per sintetizzare l'audio dal testo di traduzione. Scorrere ogni traduzione e sintetizzare la traduzione. Quando si `SpeechSynthesizer` crea `SpeechConfig` un'istanza, [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] la proprietà dell'oggetto deve essere impostata sulla voce desiderata. L'esempio seguente traduce in cinque lingue e ogni traduzione viene quindi sintetizzata in un file audio nella lingua neurale corrispondente.

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

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base della sintesi vocale](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
