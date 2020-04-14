---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 7c57952d0b78271bbcc45bd282385524772f0f9e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266647"
---
## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si disponga di un account azure e di una sottoscrizione al servizio di riconoscimento vocale. Se non si dispone di un account e di un abbonamento, [provare il servizio di riconoscimento vocale gratuitamente](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, seguire le istruzioni nella sezione <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Ottenere l'SDK <span class="docon docon-navigate-external x-hidden-focus"></span> di riconoscimento vocale</a> dell'articolo Speech SDK.

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi riportati `#include` in `using` questo articolo, includere le istruzioni riportate di seguito e all'inizio del file di codice c'è.

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

## <a name="sensitive-data-and-environment-variables"></a>Dati sensibili e variabili di ambienteSensitive data and environment variables

Il codice sorgente di esempio in questo articolo dipende dalle variabili di ambiente per l'archiviazione di dati sensibili, ad esempio la chiave e l'area di sottoscrizione della risorsa di riconoscimento vocale. Il file di codice di C' contiene due valori stringa che `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION`vengono assegnati dalle variabili di ambiente dei computer host, ovvero e . Entrambi questi campi si trovano nell'ambito della classe, rendendoli accessibili all'interno dei corpi del metodo della classe. Per ulteriori informazioni sulle variabili di ambiente, vedere [Variabili di ambiente e configurazione dell'applicazione](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

## <a name="change-source-language"></a>Modificare la lingua di origine

Un'attività comune di traduzione vocale è specificare la lingua di input (o origine). Diamo un'occhiata a come si sarebbe cambiare la lingua di input in italiano. Nel codice interagire con [`SpeechTranslationConfig`][config] l'istanza, chiamando il `SetSpeechRecognitionLanguage` metodo .

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

La [`SpeechRecognitionLanguage`][recognitionlang] proprietà prevede una stringa di formato delle impostazioni locali della lingua. È possibile fornire qualsiasi valore nella colonna **Impostazioni locali** nell'elenco delle [impostazioni locali/lingue](../../../language-support.md)supportate.

## <a name="add-translation-language"></a>Aggiungere la lingua di traduzione

Un altro compito comune di traduzione vocale è quello di specificare le lingue di traduzione di destinazione, almeno uno è obbligatorio, ma sono supportati i multipli. Nel frammento di codice seguente, sia il francese che il tedesco come destinazioni della lingua di traduzione.

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

Con ogni [`AddTargetLanguage`][addlang]chiamata a , viene specificata una nuova lingua di traduzione di destinazione. In altre parole, quando il riconoscimento vocale viene riconosciuto dalla lingua di origine, ogni traduzione di destinazione è disponibile come parte dell'operazione di traduzione risultante.

## <a name="initialize-a-translation-recognizer"></a>Inizializzare un sistema di riconoscimento delle traduzioni

Dopo aver creato [`SpeechTranslationConfig`][config]un oggetto , il [`TranslationRecognizer`][recognizer]passaggio successivo consiste nell'inizializzare un file . Quando si [`TranslationRecognizer`][recognizer]inizializza un , è necessario `translationConfig`passarlo il file . L'oggetto di configurazione fornisce le credenziali richieste dal servizio di riconoscimento vocale per convalidare la richiesta.

Se riconosci il riconoscimento vocale utilizzando il microfono predefinito [`TranslationRecognizer`][recognizer] del dispositivo, ecco come dovrebbe apparire:

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

Se si desidera specificare il dispositivo di input audio, è necessario creare un [`AudioConfig`][audioconfig] e fornire il `audioConfig` parametro durante l'inizializzazione del [`TranslationRecognizer`][recognizer]file .

> [!TIP]
> [Scopri come ottenere l'ID del dispositivo di input audio.](../../../how-to-select-audio-input-devices.md)

In primo luogo, `AudioConfig` si farà riferimento all'oggetto come segue:

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

Se si desidera fornire un file audio invece di utilizzare un `audioConfig`microfono, è comunque necessario fornire un file . Tuttavia, quando [`AudioConfig`][audioconfig]si crea `FromDefaultMicrophoneInput`un oggetto , `FromWavFileInput` anziché `filename` chiamare , verrà chiamato e passato il parametro .

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

Per tradurre il riconoscimento vocale, Speech SDK si basa su un microfono o un input di file audio. Il riconoscimento vocale si verifica prima della traduzione vocale. Dopo che tutti gli oggetti sono stati inizializzati, chiamare la funzione recognize-once e ottenere il risultato.

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

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base del riconoscimento vocale.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Sintetizzare le traduzioni

Dopo un riconoscimento vocale e una traduzione di successo, il risultato contiene tutte le traduzioni in un dizionario. La [`Translations`][translations] chiave del dizionario è la lingua di traduzione di destinazione e il valore è il testo tradotto. Il discorso riconosciuto può essere tradotto, quindi sintetizzato in una lingua diversa (speech-to-speech).

### <a name="event-based-synthesis"></a>Sintesi basata su eventi

L'oggetto `TranslationRecognizer` espone `Synthesizing` un evento. L'evento viene generato più volte e fornisce un meccanismo per recuperare l'audio sintetizzato dal risultato del riconoscimento della traduzione. Se si sta traducendo in più lingue, vedere [sintesi manuale](#manual-synthesis). Specificare la voce di [`SetVoiceName`][voicename] sintesi assegnando `Synthesizing` un e fornire un gestore eventi per l'evento, ottenere l'audio. L'esempio seguente salva l'audio tradotto come file *wav.*

> [!IMPORTANT]
> La sintesi basata su eventi funziona solo con una singola traduzione, **non** aggiungere più lingue di traduzione di destinazione. Inoltre, il [`SetVoiceName`][voicename] dovrebbe essere la stessa lingua della lingua di traduzione di destinazione, ad esempio; `"de"` potrebbe eseguire `"de-DE-Hedda"`il mapping a .

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

Il [`Translations`][translations] dizionario può essere utilizzato per sintetizzare l'audio dal testo di traduzione. Scorrere ogni traduzione e sintetizzare la traduzione. Quando si `SpeechSynthesizer` crea `SpeechConfig` un'istanza, [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] la proprietà dell'oggetto deve essere impostata sulla voce desiderata. L'esempio seguente traduce in cinque lingue e ogni traduzione viene quindi sintetizzata in un file audio nella lingua neurale corrispondente.

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

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base della sintesi vocale](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
