---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 44980977f366bc40ceff9c7b5751d5657c79ccc2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94482773"
---
Una delle principali funzionalità del servizio Voce è la possibilità di riconoscere e trascrivere la voce umana, ovvero di convertire la voce in testo scritto. Questa guida di avvio rapido illustra come usare Speech SDK in app e prodotti per eseguire la conversione della voce in testo scritto di alta qualità.

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che:

* Si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-and-import-the-speech-sdk"></a>Installare e importare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK.

```Python
pip install azure-cognitiveservices-speech
```

Se si usa macOS e si verificano problemi di installazione, può essere necessario eseguire prima questo comando.

```Python
python3 -m pip install --upgrade pip
```

Dopo aver installato Speech SDK, importarlo nel progetto Python.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python). Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione. Creare una classe [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) usando la chiave e l'area. Per trovare la coppia chiave-area, vedere [Trovare chiavi e area](../../../overview.md#find-keys-and-region).

```Python
speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
```

Esistono alcuni altri modi per inizializzare una classe [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

## <a name="recognize-from-microphone"></a>Riconoscimento da microfono

Per eseguire il riconoscimento vocale usando il microfono del dispositivo, è sufficiente creare un oggetto `SpeechRecognizer` senza passare una classe `AudioConfig` e passando la classe `speech_config`.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_mic():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
    
    print("Speak into your microphone.")
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_mic()
```

Se si vuole usare un dispositivo di input audio *specifico*, è necessario specificare l'ID del dispositivo in una classe `AudioConfig` e passarlo al parametro `audio_config` del costruttore di `SpeechRecognizer`. Informazioni su [come ottenere l'ID del dispositivo](../../../how-to-select-audio-input-devices.md) di input audio.

## <a name="recognize-from-file"></a>Riconoscimento da file

Se si vuole specificare un file audio invece di usare un microfono per il riconoscimento vocale, è comunque necessario creare un oggetto [`AudioConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?preserve-view=true&view=azure-python) e usare il parametro `filename`.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_file():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
    
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_file()
```

## <a name="error-handling"></a>Gestione degli errori

Gli esempi precedenti ottengono semplicemente il testo riconosciuto da `result.text`, ma per gestire gli errori e altre risposte è necessario scrivere del codice per elaborare il risultato. Il seguente codice valuta la proprietà [`result.reason`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python) e:

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

## <a name="continuous-recognition"></a>Riconoscimento continuo

Gli esempi precedenti usano il riconoscimento singolo, che riconosce una singola espressione. La fine di una singola espressione viene determinata restando in ascolto del silenzio al termine o finché non vengono elaborati al massimo 15 secondi di audio.

Al contrario, si usa il riconoscimento continuo quando si vuole **controllare** il momento in cui interrompere il riconoscimento. Richiede di connettersi a `EventSignal` per ottenere i risultati del riconoscimento e per arrestare il riconoscimento è necessario chiamare [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) o [stop_continuous_recognition_async()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--). Ecco un esempio di riconoscimento continuo eseguito su un file di input audio.

Per iniziare, definire l'input e inizializzare [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-python):

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
* Dopo la ricezione di `evt`, viene effettuata una chiamata a [stop_continuous_recognition ()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) per arrestare il riconoscimento.
* Lo stato del riconoscimento cambia in `True`.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Questo esempio di codice mostra come connettere i callback agli eventi inviati da [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--).

* [`recognizing`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): segnale per gli eventi contenenti i risultati del riconoscimento intermedio.
* [`recognized`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): segnale per gli eventi contenenti i risultati del riconoscimento finale, che indicano un tentativo di riconoscimento riuscito.
* [`session_started`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): segnale per gli eventi che indicano l'inizio di una sessione di riconoscimento (operazione).
* [`session_stopped`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): segnale per gli eventi che indicano la fine di una sessione di riconoscimento (operazione).
* [`canceled`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): segnale per gli eventi contenenti risultati di riconoscimento annullati (a indicare un tentativo di riconoscimento annullato in seguito a una richiesta di annullamento diretta o, in alternativa, a un errore di trasporto o di protocollo).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Con tutte le impostazioni configurate, è possibile chiamare [start_continuous_recognition ()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Modalità dettatura

Quando si usa il riconoscimento continuo, è possibile abilitare l'elaborazione della dettatura usando la corrispondente funzione. In questa modalità l'istanza di SpeechConfig interpreta le descrizioni testuali delle strutture di frasi, ad esempio la punteggiatura. Ad esempio, l'espressione "Vivi in città punto interrogativo" verrebbe interpretata come "Vivi in città?".

Per abilitare la modalità di dettatura, usare il metodo [`enable_dictation()`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) in [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python).

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Cambiare la lingua di origine

Un'attività comune per il riconoscimento vocale è la specifica della lingua di input (o di origine). Vediamo come cambiare la lingua di input in tedesco. Nel codice trovare SpeechConfig, quindi aggiungere questa riga al di sotto.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) è un parametro che accetta una stringa come argomento. È possibile specificare qualsiasi valore nell'elenco di [impostazioni locali/lingue supportate](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Migliorare l'accuratezza del riconoscimento

È possibile migliorare l'accuratezza del riconoscimento in vari modi con Speech SDK. Esaminiamo gli elenchi di frasi. Gli elenchi di frasi vengono usati per identificare frasi note nei dati audio, ad esempio il nome di una persona o una specifica località. All'elenco di frasi è possibile aggiungere singole parole o frasi complete. Durante il riconoscimento, se nell'audio è inclusa una corrispondenza esatta per l'intera frase, viene aggiunta una voce nell'elenco di frasi. Se non si trova una corrispondenza esatta, il riconoscimento non è assistito.

> [!IMPORTANT]
> La funzionalità dell'elenco di frasi è disponibile solo in inglese.

Per usare un elenco di frasi, creare prima di tutto un oggetto [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-python), quindi aggiungere parole o frasi specifiche con [`addPhrase`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-).

Tutte le modifiche apportate a [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-python) diventano effettive al successivo riconoscimento o dopo una riconnessione al servizio Voce.

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