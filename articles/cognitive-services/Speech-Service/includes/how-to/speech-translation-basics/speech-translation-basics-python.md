---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 987aad74401a11407e33f0b0bd51548f354bdd6d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470819"
---
Una delle funzionalità principali del servizio di riconoscimento vocale è la possibilità di riconoscere la voce umana e tradurla in altre lingue. Questa Guida introduttiva illustra come usare l'SDK di riconoscimento vocale nelle app e nei prodotti per eseguire la traduzione vocale di alta qualità. Questa Guida introduttiva illustra gli argomenti tra cui:

* Conversione vocale in testo
* Conversione di riconoscimento vocale in più lingue di destinazione
* Esecuzione della traduzione vocale diretta

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, seguire le istruzioni <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">riportate nella sezione Get the <span class="docon docon-navigate-external x-hidden-focus"></span> Speech SDK</a> dell'articolo _About The Speech SDK_ .

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi in questo articolo, includere le `import` istruzioni seguenti all'inizio del file di codice Python.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Variabili di ambiente e dati sensibili

Il codice sorgente di esempio in questo articolo dipende dalle variabili di ambiente per l'archiviazione di dati sensibili, ad esempio la chiave di sottoscrizione della risorsa vocale e l'area. Il file di codice Python contiene due valori assegnati dalle variabili di ambiente dei computer host, ovvero `SPEECH__SUBSCRIPTION__KEY` e `SPEECH__SERVICE__REGION` . Entrambe queste variabili si trovano nell'ambito globale, rendendole accessibili entro la definizione di funzione del file di codice. Per altre informazioni sulle variabili di ambiente, vedere [variabili di ambiente e configurazione dell'applicazione](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
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

Vediamo come viene creata una classe [`SpeechTranslationConfig`][config] usando una chiave e un'area. Per ottenere queste credenziali, seguire questa procedura in [provare il servizio di riconoscimento vocale gratuitamente](../../../overview.md#try-the-speech-service-for-free).

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Cambiare la lingua di origine

Una delle attività comuni della traduzione vocale è la specifica della lingua di input (o di origine). Vediamo come cambiare la lingua di input in italiano. Nel codice interagire con l' [`SpeechTranslationConfig`][config] istanza, assegnando alla `speech_recognition_language` Proprietà.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

La proprietà [`speech_recognition_language`][recognitionlang] si aspetta una stringa in formato è prevista una stringa di formato lingua-impostazioni locali. È possibile specificare qualsiasi valore nella colonna **Locale** nell'elenco di [impostazioni locali/lingue supportate](../../../language-support.md).

## <a name="add-translation-language"></a>Aggiungi lingua di traduzione

Un'altra attività comune di traduzione vocale è quella di specificare i linguaggi di traduzione di destinazione, almeno uno è obbligatorio ma sono supportati più multipli. Il frammento di codice seguente imposta sia il francese che il tedesco come destinazioni del linguaggio di traduzione.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Con ogni chiamata a [`add_target_language`][addlang] , viene specificato un nuovo linguaggio di conversione di destinazione. In altre parole, quando la voce vocale viene riconosciuta dalla lingua di origine, ogni conversione di destinazione è disponibile come parte dell'operazione di conversione risultante.

## <a name="initialize-a-translation-recognizer"></a>Inizializzare un riconoscimento di traduzione

Dopo aver creato una classe [`SpeechTranslationConfig`][config], il passaggio successivo consiste nell'inizializzare [`TranslationRecognizer`][recognizer]. Quando si Inizializza [`TranslationRecognizer`][recognizer], sarà necessario passare `translation_config`. L'oggetto di configurazione fornisce le credenziali richieste dal servizio di riconoscimento vocale per convalidare la richiesta.

Se si esegue il riconoscimento vocale usando il microfono predefinito del dispositivo, [`TranslationRecognizer`][recognizer] avrà questo aspetto:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Se si vuole specificare il dispositivo di input audio, è necessario creare [`AudioConfig`][audioconfig] e specificare il parametro `audio_config` quando si inizializza [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Informazioni su come ottenere l'ID del dispositivo di input audio](../../../how-to-select-audio-input-devices.md).

In primo luogo, si farà riferimento all' `AudioConfig` oggetto come segue:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Se si vuole specificare un file audio invece di usare un microfono, è comunque necessario fornire `audioConfig`. Tuttavia, quando si crea un oggetto [`AudioConfig`][audioconfig] , invece di chiamare con `use_default_microphone=True` , si chiamerà con `filename="path-to-file.wav"` e si fornirà il `filename` parametro.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Traduzione vocale

Per tradurre la voce, l'SDK di riconoscimento vocale si basa su un microfono o un input di file audio. Il riconoscimento vocale si verifica prima della traduzione vocale. Dopo l'inizializzazione di tutti gli oggetti, chiamare la funzione Recognize-once e ottenere il risultato.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base del riconoscimento vocale](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Sintetizza le traduzioni

Dopo un riconoscimento vocale e una traduzione con esito positivo, il risultato contiene tutte le traduzioni in un dizionario. La [`translations`][translations] chiave del dizionario è il linguaggio di conversione di destinazione e il valore è il testo tradotto. Il riconoscimento vocale riconosciuto può essere convertito, quindi sintetizzato in un linguaggio diverso (sintesi vocale).

### <a name="event-based-synthesis"></a>Sintesi basata su eventi

L' `TranslationRecognizer` oggetto espone un `Synthesizing` evento. L'evento viene generato più volte e fornisce un meccanismo per recuperare l'audio sintetizzato dal risultato del riconoscimento della traduzione. Se si esegue la conversione in più lingue, vedere [sintesi manuale](#manual-synthesis). Specificare la voce di sintesi assegnando un oggetto [`voice_name`][voicename] e fornire un gestore eventi per l' `Synthesizing` evento, ottenere l'audio. Nell'esempio seguente viene salvato l'audio tradotto come file *WAV* .

> [!IMPORTANT]
> La sintesi basata su eventi funziona solo con una singola traduzione. **non** aggiungere più lingue di traduzione di destinazione. Inoltre, [`voice_name`][voicename] deve essere la stessa lingua del linguaggio di traduzione di destinazione, ad esempio `"de"` . può eseguire il mapping a `"de-DE-Hedda"` .

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Sintesi manuale

Il [`translations`][translations] dizionario può essere usato per sintetizzare l'audio dal testo di traduzione. Scorrere ogni traduzione e sintetizzare la traduzione. Quando si crea un' `SpeechSynthesizer` istanza, l' `SpeechConfig` oggetto deve avere la [`speech_synthesis_voice_name`][speechsynthesisvoicename] proprietà impostata sulla voce desiderata. Nell'esempio seguente viene convertito in cinque lingue e ogni conversione viene quindi sintetizzata in un file audio nel linguaggio neurale corrispondente.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base sulla sintesi vocale](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python
[audioconfig]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python
[recognizer]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer?view=azure-python
[recognitionlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
[addlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#add-target-language-language--str-
[translations]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult?view=azure-python#translations
[voicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#voice-name
[speechsynthesisvoicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-synthesis-voice-name
