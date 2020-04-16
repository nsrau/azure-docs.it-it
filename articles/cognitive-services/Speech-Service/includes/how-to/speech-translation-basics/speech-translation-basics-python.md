---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 17d8c0157fcd478d01452167d240fb67daeeda5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399653"
---
## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si disponga di un account azure e di una sottoscrizione al servizio di riconoscimento vocale. Se non si dispone di un account e di un abbonamento, [provare il servizio di riconoscimento vocale gratuitamente](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, seguire le istruzioni nella sezione <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Ottenere l'SDK <span class="docon docon-navigate-external x-hidden-focus"></span> di riconoscimento vocale</a> dell'articolo Speech SDK.

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi in questo `import` articolo, includere le istruzioni seguenti all'inizio del file di codice python.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Dati sensibili e variabili di ambienteSensitive data and environment variables

Il codice sorgente di esempio in questo articolo dipende dalle variabili di ambiente per l'archiviazione di dati sensibili, ad esempio la chiave e l'area di sottoscrizione della risorsa di riconoscimento vocale. Il file di codice python contiene due valori assegnati `SPEECH__SUBSCRIPTION__KEY` dalle `SPEECH__SERVICE__REGION`variabili di ambiente dei computer host, ovvero e . Entrambe queste variabili si trovano nell'ambito globale, rendendole accessibili all'interno della definizione della funzione del file di codice. Per ulteriori informazioni sulle variabili di ambiente, vedere [Variabili di ambiente e configurazione dell'applicazione](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
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

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Modificare la lingua di origine

Un'attività comune di traduzione vocale è specificare la lingua di input (o origine). Diamo un'occhiata a come si sarebbe cambiare la lingua di input in italiano. Nel codice, interagire [`SpeechTranslationConfig`][config] con l'istanza, `speech_recognition_language` assegnando alla proprietà.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

La [`speech_recognition_language`][recognitionlang] proprietà prevede una stringa di formato delle impostazioni locali della lingua. È possibile fornire qualsiasi valore nella colonna **Impostazioni locali** nell'elenco delle [impostazioni locali/lingue](../../../language-support.md)supportate.

## <a name="add-translation-language"></a>Aggiungere la lingua di traduzione

Un altro compito comune di traduzione vocale è quello di specificare le lingue di traduzione di destinazione, almeno uno è obbligatorio, ma sono supportati i multipli. Nel frammento di codice seguente, sia il francese che il tedesco come destinazioni della lingua di traduzione.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Con ogni [`add_target_language`][addlang]chiamata a , viene specificata una nuova lingua di traduzione di destinazione. In altre parole, quando il riconoscimento vocale viene riconosciuto dalla lingua di origine, ogni traduzione di destinazione è disponibile come parte dell'operazione di traduzione risultante.

## <a name="initialize-a-translation-recognizer"></a>Inizializzare un sistema di riconoscimento delle traduzioni

Dopo aver creato [`SpeechTranslationConfig`][config]un oggetto , il [`TranslationRecognizer`][recognizer]passaggio successivo consiste nell'inizializzare un file . Quando si [`TranslationRecognizer`][recognizer]inizializza un , è necessario `translation_config`passarlo il file . L'oggetto di configurazione fornisce le credenziali richieste dal servizio di riconoscimento vocale per convalidare la richiesta.

Se riconosci il riconoscimento vocale utilizzando il microfono predefinito [`TranslationRecognizer`][recognizer] del dispositivo, ecco come dovrebbe apparire:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Se si desidera specificare il dispositivo di input audio, è necessario creare un [`AudioConfig`][audioconfig] e fornire il `audio_config` parametro durante l'inizializzazione del [`TranslationRecognizer`][recognizer]file .

> [!TIP]
> [Scopri come ottenere l'ID del dispositivo di input audio.](../../../how-to-select-audio-input-devices.md)

In primo luogo, `AudioConfig` si farà riferimento all'oggetto come segue:

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

Se si desidera fornire un file audio invece di utilizzare un `audioConfig`microfono, è comunque necessario fornire un file . Tuttavia, quando [`AudioConfig`][audioconfig]si crea un `use_default_microphone=True`oggetto , anziché chiamare con , verrà chiamato con `filename="path-to-file.wav"` e fornire il `filename` parametro .

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

Per tradurre il riconoscimento vocale, Speech SDK si basa su un microfono o un input di file audio. Il riconoscimento vocale si verifica prima della traduzione vocale. Dopo che tutti gli oggetti sono stati inizializzati, chiamare la funzione recognize-once e ottenere il risultato.

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

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base del riconoscimento vocale.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Sintetizzare le traduzioni

Dopo un riconoscimento vocale e una traduzione di successo, il risultato contiene tutte le traduzioni in un dizionario. La [`translations`][translations] chiave del dizionario è la lingua di traduzione di destinazione e il valore è il testo tradotto. Il discorso riconosciuto può essere tradotto, quindi sintetizzato in una lingua diversa (speech-to-speech).

### <a name="event-based-synthesis"></a>Sintesi basata su eventi

L'oggetto `TranslationRecognizer` espone `Synthesizing` un evento. L'evento viene generato più volte e fornisce un meccanismo per recuperare l'audio sintetizzato dal risultato del riconoscimento della traduzione. Se si sta traducendo in più lingue, vedere [sintesi manuale](#manual-synthesis). Specificare la voce di [`voice_name`][voicename] sintesi assegnando `Synthesizing` un e fornire un gestore eventi per l'evento, ottenere l'audio. L'esempio seguente salva l'audio tradotto come file *wav.*

> [!IMPORTANT]
> La sintesi basata su eventi funziona solo con una singola traduzione, **non** aggiungere più lingue di traduzione di destinazione. Inoltre, il [`voice_name`][voicename] dovrebbe essere la stessa lingua della lingua di traduzione di destinazione, ad esempio; `"de"` potrebbe eseguire `"de-DE-Hedda"`il mapping a .

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

Il [`translations`][translations] dizionario può essere utilizzato per sintetizzare l'audio dal testo di traduzione. Scorrere ogni traduzione e sintetizzare la traduzione. Quando si `SpeechSynthesizer` crea `SpeechConfig` un'istanza, [`speech_synthesis_voice_name`][speechsynthesisvoicename] la proprietà dell'oggetto deve essere impostata sulla voce desiderata. L'esempio seguente traduce in cinque lingue e ogni traduzione viene quindi sintetizzata in un file audio nella lingua neurale corrispondente.

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

Per ulteriori informazioni sulla sintesi vocale, vedere [le nozioni di base della sintesi vocale](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python
[audioconfig]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python
[recognizer]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer?view=azure-python
[recognitionlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
[addlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#add-target-language-language--str-
[translations]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult?view=azure-python#translations
[voicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#voice-name
[speechsynthesisvoicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-synthesis-voice-name
