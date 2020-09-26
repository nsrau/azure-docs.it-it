---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 1b54d0059e68c7e06904155fdd1a70782e07f493
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377608"
---
Una delle funzionalità principali del servizio di riconoscimento vocale è la possibilità di riconoscere la voce umana e tradurla in altre lingue. Questa Guida introduttiva illustra come usare l'SDK di riconoscimento vocale nelle app e nei prodotti per eseguire la traduzione vocale di alta qualità. Questa Guida introduttiva illustra gli argomenti tra cui:

* Conversione vocale in testo
* Conversione di riconoscimento vocale in più lingue di destinazione
* Esecuzione della traduzione vocale diretta

## <a name="skip-to-samples-on-github"></a>Passare agli esempi in GitHub

Per passare direttamente al codice di esempio, vedere gli esempi di [avvio rapido di C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/translate-speech-to-text) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, seguire le istruzioni <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">riportate nella sezione Get the <span class="docon docon-navigate-external x-hidden-focus"></span> Speech SDK</a> dell'articolo _About The Speech SDK_ .

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi in questo articolo, includere le `#include` istruzioni e seguenti `using` all'inizio del file di codice C++.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Variabili di ambiente e dati sensibili

Il codice sorgente di esempio in questo articolo dipende dalle variabili di ambiente per l'archiviazione di dati sensibili, ad esempio la chiave di sottoscrizione della risorsa vocale e l'area. Il file di codice C++ contiene due valori stringa assegnati dalle variabili di ambiente computer host, in particolare `SPEECH__SUBSCRIPTION__KEY` e `SPEECH__SERVICE__REGION` . Entrambi questi campi si trovano nell'ambito della classe, rendendoli accessibili nei corpi dei metodi della classe. Per altre informazioni sulle variabili di ambiente, vedere [variabili di ambiente e configurazione dell'applicazione](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Cambiare la lingua di origine

Una delle attività comuni della traduzione vocale è la specifica della lingua di input (o di origine). Vediamo come cambiare la lingua di input in italiano. Nel codice interagire con l' [`SpeechTranslationConfig`][config] istanza, chiamando il `SetSpeechRecognitionLanguage` metodo.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

La proprietà [`SpeechRecognitionLanguage`][recognitionlang] si aspetta una stringa in formato è prevista una stringa di formato lingua-impostazioni locali. È possibile specificare qualsiasi valore nella colonna **Locale** nell'elenco di [impostazioni locali/lingue supportate](../../../language-support.md).

## <a name="add-translation-language"></a>Aggiungi lingua di traduzione

Un'altra attività comune di traduzione vocale è quella di specificare i linguaggi di traduzione di destinazione, almeno uno è obbligatorio ma sono supportati più multipli. Il frammento di codice seguente imposta sia il francese che il tedesco come destinazioni del linguaggio di traduzione.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

Con ogni chiamata a [`AddTargetLanguage`][addlang] , viene specificato un nuovo linguaggio di conversione di destinazione. In altre parole, quando la voce vocale viene riconosciuta dalla lingua di origine, ogni conversione di destinazione è disponibile come parte dell'operazione di conversione risultante.

## <a name="initialize-a-translation-recognizer"></a>Inizializzare un riconoscimento di traduzione

Dopo aver creato una classe [`SpeechTranslationConfig`][config], il passaggio successivo consiste nell'inizializzare [`TranslationRecognizer`][recognizer]. Quando si Inizializza [`TranslationRecognizer`][recognizer], sarà necessario passare `translationConfig`. L'oggetto di configurazione fornisce le credenziali richieste dal servizio di riconoscimento vocale per convalidare la richiesta.

Se si esegue il riconoscimento vocale usando il microfono predefinito del dispositivo, [`TranslationRecognizer`][recognizer] avrà questo aspetto:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Se si vuole specificare il dispositivo di input audio, è necessario creare [`AudioConfig`][audioconfig] e specificare il parametro `audioConfig` quando si inizializza [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Informazioni su come ottenere l'ID del dispositivo di input audio](../../../how-to-select-audio-input-devices.md).

In primo luogo, si farà riferimento all' `AudioConfig` oggetto come segue:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Se si vuole specificare un file audio invece di usare un microfono, è comunque necessario fornire `audioConfig`. Tuttavia, quando si crea [`AudioConfig`][audioconfig], invece di chiamare `FromDefaultMicrophoneInput`, si chiamerà `FromWavFileInput` e si passerà il parametro `filename`.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Traduzione vocale

Per tradurre la voce, l'SDK di riconoscimento vocale si basa su un microfono o un input di file audio. Il riconoscimento vocale si verifica prima della traduzione vocale. Dopo l'inizializzazione di tutti gli oggetti, chiamare la funzione Recognize-once e ottenere il risultato.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base del riconoscimento vocale](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Sintetizza le traduzioni

Dopo un riconoscimento vocale e una traduzione con esito positivo, il risultato contiene tutte le traduzioni in un dizionario. La [`Translations`][translations] chiave del dizionario è il linguaggio di conversione di destinazione e il valore è il testo tradotto. Il riconoscimento vocale riconosciuto può essere convertito, quindi sintetizzato in un linguaggio diverso (sintesi vocale).

### <a name="event-based-synthesis"></a>Sintesi basata su eventi

L' `TranslationRecognizer` oggetto espone un `Synthesizing` evento. L'evento viene generato più volte e fornisce un meccanismo per recuperare l'audio sintetizzato dal risultato del riconoscimento della traduzione. Se si esegue la conversione in più lingue, vedere [sintesi manuale](#manual-synthesis). Specificare la voce di sintesi assegnando un oggetto [`SetVoiceName`][voicename] e fornire un gestore eventi per l' `Synthesizing` evento, ottenere l'audio. Nell'esempio seguente viene salvato l'audio tradotto come file *WAV* .

> [!IMPORTANT]
> La sintesi basata su eventi funziona solo con una singola traduzione. **non** aggiungere più lingue di traduzione di destinazione. Inoltre, [`SetVoiceName`][voicename] deve essere la stessa lingua del linguaggio di traduzione di destinazione, ad esempio `"de"` . può eseguire il mapping a `"de-DE-Hedda"` .

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Sintesi manuale

Il [`Translations`][translations] dizionario può essere usato per sintetizzare l'audio dal testo di traduzione. Scorrere ogni traduzione e sintetizzare la traduzione. Quando si crea un' `SpeechSynthesizer` istanza, l' `SpeechConfig` oggetto deve avere la [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] proprietà impostata sulla voce desiderata. Nell'esempio seguente viene convertito in cinque lingue e ogni conversione viene quindi sintetizzata in un file audio nel linguaggio neurale corrispondente.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base sulla sintesi vocale](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
