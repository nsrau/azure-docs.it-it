---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: c63b55905ea16c0597e1f8f1179153229018ed32
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470780"
---
Una delle funzionalità principali del servizio di riconoscimento vocale è la possibilità di riconoscere la voce umana e tradurla in altre lingue. Questa Guida introduttiva illustra come usare l'SDK di riconoscimento vocale nelle app e nei prodotti per eseguire la traduzione vocale di alta qualità. Questa Guida introduttiva illustra gli argomenti tra cui:

* Conversione vocale in testo
* Conversione di riconoscimento vocale in più lingue di destinazione
* Esecuzione della traduzione vocale diretta

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK per JavaScript<span class="docon docon-navigate-external x-hidden-focus"></span></a>. A seconda della piattaforma, usare le istruzioni seguenti:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web browser <span class="docon docon-navigate-external x-hidden-focus"></span></a>

A seconda dell'ambiente di destinazione, inoltre, usare una delle alternative seguenti:

# <a name="script"></a>[script](#tab/script)

Scaricare ed estrarre il file *microsoft.cognitiveservices.speech.sdk.bundle.js* di <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK per JavaScript<span class="docon docon-navigate-external x-hidden-focus"></span></a> e inserirlo in una cartella accessibile per il file HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se la destinazione è un Web browser e si usa il tag `<script>`, il prefisso `sdk` non è necessario. Il prefisso `sdk` è un alias usato per la denominazione del modulo di `require`.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Per altre informazioni su `import`, vedere <a href="https://javascript.info/import-export" target="_blank">Export e import<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Per altre informazioni su `require`, vedere <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Informazioni su require<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="create-a-translation-configuration"></a>Creare una configurazione di traduzione

Per chiamare il servizio di traduzione usando l'SDK vocale, è necessario creare un [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) . Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.
Esistono diversi modi per inizializzare [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true):

* Con una sottoscrizione: passare una chiave e l'area associata.
* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

Vediamo come viene creata una classe [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) usando una chiave e un'area. Per ottenere queste credenziali, seguire questa procedura in [provare il servizio di riconoscimento vocale gratuitamente](../../../overview.md#try-the-speech-service-for-free).

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Inizializzare un convertitore

Dopo aver creato una classe [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true), il passaggio successivo consiste nell'inizializzare [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true). Quando si Inizializza [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true), sarà necessario passare `speechTranslationConfig`. In questo modo vengono fornite le credenziali richieste dal servizio di traduzione per convalidare la richiesta.

Se si sta traducendo il riconoscimento vocale fornito tramite il microfono predefinito del dispositivo, ecco l' [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) aspetto che dovrebbe essere simile al seguente:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Se si vuole specificare il dispositivo di input audio, è necessario creare [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) e specificare il parametro `audioConfig` quando si inizializza [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true).

> [!TIP]
> [Informazioni su come ottenere l'ID del dispositivo di input audio](../../../how-to-select-audio-input-devices.md).
Fare riferimento all'oggetto `AudioConfig` come indicato di seguito:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Se si vuole specificare un file audio invece di usare un microfono, è comunque necessario fornire `audioConfig`. Questo è tuttavia possibile solo quando la destinazione è **Node.js**. Quando si crea un oggetto [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true), invece di chiamare `fromDefaultMicrophoneInput`, si chiamerà `fromWavFileOutput` e si passerà il parametro `filename`.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Traduzione vocale

La [classe TranslationRecognizer](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true) per l'SDK di riconoscimento vocale per JavaScript espone alcuni metodi che è possibile usare per la traduzione vocale.

* Single-Shot Translation (Async): esegue la conversione in modalità non di blocco (asincrono). Questo tradurrà una singola espressione. La fine di una singola espressione viene determinata restando in ascolto del silenzio al termine o finché non vengono elaborati al massimo 15 secondi di audio.
* Continuous Translation (Async): avvia in modo asincrono un'operazione di conversione continua. L'utente esegue la registrazione agli eventi e gestisce diversi Stati dell'applicazione. Per arrestare la conversione continua asincrona, chiamare [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync) .

> [!NOTE]
> Per altre informazioni, vedere come [scegliere una modalità di riconoscimento vocale](../../../how-to-choose-recognition-mode.md).
### <a name="specify-a-target-language"></a>Specificare una lingua di destinazione

Per tradurre, è necessario specificare una lingua di origine e almeno una lingua di destinazione.
È possibile scegliere una lingua di origine usando le impostazioni locali elencate nella [tabella di traduzione vocale](../../../language-support.md#speech-translation). Trova le opzioni per la lingua tradotta con lo stesso collegamento. Le opzioni per le lingue di destinazione sono diverse quando si desidera visualizzare il testo o si desidera ascoltare la sintesi vocale tradotta. Per eseguire la conversione dalla lingua inglese alla lingua tedesca, modificare l'oggetto Translation config:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Riconoscimento singolo

Di seguito è riportato un esempio di traduzione asincrona a singolo tentativo con [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Per gestire il risultato è necessario scrivere del codice. Questo esempio valuta [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult?view=azure-node-latest&preserve-view=true) per una traduzione in tedesco:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

Il codice può anche gestire gli aggiornamenti forniti durante l'elaborazione della traduzione.
È possibile utilizzare questi aggiornamenti per fornire commenti visivi sullo stato di avanzamento della traduzione.
Vedere [questo esempio di Node.js JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) per il codice di esempio che Mostra gli aggiornamenti forniti durante il processo di conversione. Il codice seguente visualizza anche i dettagli prodotti durante il processo di conversione.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Traduzione continua

La traduzione continua è un po' più complessa rispetto al riconoscimento single-shot. Per ottenere i risultati del riconoscimento, è necessario sottoscrivere gli eventi `recognizing`, `recognized` e `canceled`. Per arrestare la traduzione, è necessario chiamare [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync) . Di seguito è riportato un esempio di come viene eseguita la conversione continua su un file di input audio.

Per iniziare, definire l'input e inizializzare [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true):

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Verranno sottoscritti gli eventi inviati da [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true).

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#recognizing): Segnale per gli eventi contenenti i risultati della traduzione intermedia.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#recognized): Segnale per gli eventi contenenti i risultati finali della traduzione (che indicano un tentativo di conversione riuscito).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#sessionstopped): Segnale per gli eventi che indicano la fine di una sessione di traduzione (operazione).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest&preserve-view=true#canceled): Segnale per gli eventi contenenti i risultati della traduzione annullata (indicante un tentativo di conversione annullato come risultato o una richiesta di annullamento diretta o, in alternativa, un errore di trasporto o di protocollo).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
    }
};
recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);
    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }
    recognizer.stopContinuousRecognitionAsync();
};
recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Con tutti gli elementi configurati, è possibile chiamare [`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#startcontinuousrecognitionasync).

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>Scegliere una lingua di origine

Un'attività comune per la traduzione vocale è la specifica della lingua di input (o di origine). Vediamo come cambiare la lingua di input in italiano. Nel codice trovare il [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true) e quindi aggiungere la riga seguente direttamente sotto di essa.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

La proprietà [`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest&preserve-view=true#speechrecognitionlanguage) si aspetta una stringa in formato è prevista una stringa di formato lingua-impostazioni locali. È possibile specificare qualsiasi valore nella colonna **Locale** nell'elenco di [impostazioni locali/lingue supportate](../../../language-support.md).

## <a name="choose-one-or-more-target-languages"></a>Scegliere una o più lingue di destinazione

L'SDK vocale può tradurre in più lingue di destinazione in parallelo. Le lingue di destinazione disponibili sono leggermente diverse dall'elenco della lingua di origine e si specificano le lingue di destinazione usando un codice di lingua, anziché le impostazioni locali.
Per un elenco dei codici di lingua per le destinazioni di testo, vedere [la tabella traduzione vocale nella pagina supporto per la lingua](../../../language-support.md#speech-translation). È anche possibile trovare informazioni dettagliate sulla traduzione in linguaggi sintetizzati.

Il codice seguente aggiunge il tedesco come lingua di destinazione:

```javascript
translationConfig.addTargetLanguage("de");
```

Poiché sono possibili più traduzioni della lingua di destinazione, il codice deve specificare la lingua di destinazione quando si esamina il risultato. Il codice seguente ottiene i risultati della traduzione per il tedesco.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
