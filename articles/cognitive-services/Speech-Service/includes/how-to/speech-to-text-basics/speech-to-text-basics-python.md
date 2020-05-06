---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 3ee78560818d3651d5822c551ba2e0ccee3376e6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204627"
---
## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che:

* Si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../get-started.md).

## <a name="install-and-import-the-speech-sdk"></a>Installare e importare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK.

```Python
pip install azure-cognitiveservices-speech
```

Se si usa macOS e si verificano problemi di installazione, può essere necessario eseguire prima questo comando.

```Python
python3 -m pip install --upgrade pip
```

Dopo aver installato Speech SDK, importarlo nel progetto Python con questa istruzione.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python). Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

Esistono diversi modi per inizializzare [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

* Con una sottoscrizione: passare una chiave e l'area associata.
* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

Vediamo come viene creata una classe [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) usando una chiave e un'area. Per trovare l'identificatore di area, vedere la pagina del [supporto a livello di area](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk).

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>Inizializzare lo strumento di riconoscimento

Dopo aver creato una classe [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python), il passaggio successivo consiste nell'inizializzare [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python). Quando si Inizializza [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python), sarà necessario passare `speech_config`. In questo modo vengono fornite le credenziali richieste dal servizio Voce per convalidare la richiesta.

Se si esegue il riconoscimento vocale usando il microfono predefinito del dispositivo, [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) avrà questo aspetto:

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

Se si vuole specificare il dispositivo di input audio, è necessario creare [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) e specificare il parametro `audio_config` quando si inizializza [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python).

> [!TIP]
> [Informazioni su come ottenere l'ID del dispositivo di input audio](../../../how-to-select-audio-input-devices.md).

```Python
audio_config = AudioConfig(device_name="<device id>");
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Se si vuole specificare un file audio invece di usare un microfono, è comunque necessario fornire `audio_config`. Tuttavia, quando si crea [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python), invece di fornire `device_name`, si userà il parametro `filename`.

```Python
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>Riconoscimento vocale

La [classe Recognizer](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) per Speech SDK per Python espone alcuni metodi che è possibile usare per il riconoscimento vocale.

* Riconoscimento singolo (sincrono): esegue il riconoscimento in modalità bloccante (sincrona). Restituisce il risultato dopo il riconoscimento di una singola espressione. La fine di una singola espressione viene determinata restando in ascolto del silenzio al termine o finché non vengono elaborati al massimo 15 secondi di audio. L'attività restituisce il testo di riconoscimento come risultato.
* Riconoscimento singolo (asincrono): esegue il riconoscimento in modalità non bloccante (asincrona). Verrà riconosciuta una singola espressione. La fine di una singola espressione viene determinata restando in ascolto del silenzio al termine o finché non vengono elaborati al massimo 15 secondi di audio.
* Riconoscimento continuo (sincrono): avvia in modo sincrono il riconoscimento continuo. Il client deve connettersi a `EventSignal` per ricevere i risultati del riconoscimento. Per arrestare il riconoscimento, chiamare [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--).
* Riconoscimento continuo (asincrono): avvia in modo asincrono l'operazione di riconoscimento continuo. Per ricevere i risultati del riconoscimento, l'utente deve connettersi a EventSignal. Per arrestare il riconoscimento continuo asincrono, chiamare [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--).

> [!NOTE]
> Per altre informazioni, vedere come [scegliere una modalità di riconoscimento vocale](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Riconoscimento singolo

Ecco un esempio di riconoscimento singolo sincrono usando [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once):

```Python
result = speech_recognizer.recognize_once()
```

Ecco un esempio di riconoscimento singolo asincrono usando [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture):

```Python
result = speech_recognizer.recognize_once_async()
```

Sia che si usi il metodo sincrono o asincrono, sarà necessario scrivere del codice per eseguire l'iterazione nel risultato. Questo esempio valuta [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python):

* Stampa il risultato del riconoscimento: `speechsdk.ResultReason.RecognizedSpeech`
* Se non esiste una corrispondenza di riconoscimento, informa l'utente: `speechsdk.ResultReason.NoMatch `
* Se viene rilevato un errore, stampa il messaggio di errore: `speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>Riconoscimento continuo

Il riconoscimento continuo è un po' più impegnativo rispetto al riconoscimento singolo. Richiede di connettersi a `EventSignal` per ottenere i risultati del riconoscimento e per arrestare il riconoscimento è necessario chiamare [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) o [stop_continuous_recognition_async()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--). Ecco un esempio di riconoscimento continuo eseguito su un file di input audio.

Per iniziare, definire l'input e inizializzare [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python):

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Successivamente, creare una variabile per gestire lo stato del riconoscimento vocale. Per iniziare, questa variabile verrà impostata su `False`, dal momento che all'inizio si può ovviamente presupporre che il riconoscimento non sia terminato.

```Python
done = False
```

A questo punto, verrà creato un callback per arrestare il riconoscimento continuo quando si riceve `evt`. Occorre tenere presenti alcuni aspetti.

* Quando si riceve `evt`, viene stampato il messaggio `evt`.
* Dopo la ricezione di `evt`, viene effettuata una chiamata a [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) per arrestare il riconoscimento.
* Lo stato del riconoscimento cambia in `True`.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Questo esempio di codice mostra come connettere i callback agli eventi inviati da [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--).

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): segnale per gli eventi contenenti i risultati del riconoscimento intermedio.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): segnale per gli eventi contenenti i risultati del riconoscimento finale, che indicano un tentativo di riconoscimento riuscito.
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): segnale per gli eventi che indicano l'inizio di una sessione di riconoscimento (operazione).
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): segnale per gli eventi che indicano la fine di una sessione di riconoscimento (operazione).
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): segnale per gli eventi contenenti risultati di riconoscimento annullati (a indicare un tentativo di riconoscimento annullato in seguito a una richiesta di annullamento diretta o, in alternativa, a un errore di trasporto o di protocollo).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Con tutte le impostazioni configurate, è possibile chiamare [start_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Modalità dettatura

Quando si usa il riconoscimento continuo, è possibile abilitare l'elaborazione della dettatura usando la corrispondente funzione. In questa modalità l'istanza di SpeechConfig interpreta le descrizioni testuali delle strutture di frasi, ad esempio la punteggiatura. Ad esempio, l'espressione "Vivi in città punto interrogativo" verrebbe interpretata come "Vivi in città?".

Per abilitare la modalità di dettatura, usare il metodo [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) in [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python).

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Cambiare la lingua di origine

Un'attività comune per il riconoscimento vocale è la specifica della lingua di input (o di origine). Vediamo come cambiare la lingua di input in tedesco. Nel codice trovare SpeechConfig, quindi aggiungere questa riga al di sotto.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) è un parametro che accetta una stringa come argomento. È possibile specificare qualsiasi valore nell'elenco di [impostazioni locali/lingue supportate](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Migliorare l'accuratezza del riconoscimento

È possibile migliorare l'accuratezza del riconoscimento in vari modi con Speech SDK. Esaminiamo gli elenchi di frasi. Gli elenchi di frasi vengono usati per identificare frasi note nei dati audio, ad esempio il nome di una persona o una specifica località. All'elenco di frasi è possibile aggiungere singole parole o frasi complete. Durante il riconoscimento, se nell'audio è inclusa una corrispondenza esatta per l'intera frase, viene aggiunta una voce nell'elenco di frasi. Se non si trova una corrispondenza esatta, il riconoscimento non è assistito.

> [!IMPORTANT]
> La funzionalità dell'elenco di frasi è disponibile solo in inglese.

Per usare un elenco di frasi, creare prima di tutto un oggetto [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python), quindi aggiungere parole o frasi specifiche con [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-).

Tutte le modifiche apportate a [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) diventano effettive al successivo riconoscimento o dopo una riconnessione al servizio Voce.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Se è necessario cancellare l'elenco di frasi: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Altre opzioni per migliorare l'accuratezza del riconoscimento

Gli elenchi di frasi sono solo una delle opzioni disponibili per migliorare l'accuratezza del riconoscimento. È anche possibile: 

* [Migliorare l'accuratezza con Riconoscimento vocale personalizzato](../../../how-to-custom-speech.md)
* [Migliorare l'accuratezza con i modelli di tenant](../../../tutorial-tenant-model.md)
