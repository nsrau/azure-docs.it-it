---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 3ac9c0f473279866d580795b76c77516fc3ef679
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92471034"
---
Questo argomento di avvio rapido illustra i modelli di progettazione comuni per eseguire la sintesi vocale con Speech SDK. Si inizia con la configurazione di base e la sintesi e si passa ad esempi più avanzati per lo sviluppo di applicazioni personalizzate, tra cui:

* Recupero di risposte come flussi in memoria
* Personalizzazione della frequenza di campionamento e della velocità in bit dell'output
* Invio di richieste di sintesi tramite SSML (Speech Synthesis Markup Language)
* Uso di voci neurali

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK per JavaScript<span class="docon docon-navigate-external x-hidden-focus"></span></a>. A seconda della piattaforma, usare le istruzioni seguenti:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web browser <span class="docon docon-navigate-external x-hidden-focus"></span></a>

A seconda dell'ambiente di destinazione, inoltre, usare una delle alternative seguenti:

# <a name="script"></a>[script](#tab/script)

Scaricare ed estrarre il file *microsoft.cognitiveservices.speech.sdk.bundle.js* di <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK per JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> e inserirlo in una cartella accessibile per il file HTML.

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


## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true). Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione.

> [!NOTE]
> Sia che si esegua il riconoscimento vocale, la sintesi vocale, la traduzione o il riconoscimento finalità, sarà sempre necessario creare una configurazione.

Esistono diversi modi per inizializzare [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true):

* Con una sottoscrizione: passare una chiave e l'area associata.
* Con un endpoint: passare un endpoint del servizio Voce. La chiave e il token di autorizzazione sono facoltativi.
* Con un host: passare l'indirizzo di un host. La chiave e il token di autorizzazione sono facoltativi.
* Con un token di autorizzazione: passare un token di autorizzazione e l'area associata.

In questo esempio viene creato un oggetto [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) usando una chiave e un'area di sottoscrizione. Per ottenere queste credenziali, seguire la procedura descritta in [Provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free). Si crea anche un codice boilerplate di base da usare per la parte restante di questo articolo, che viene modificato per diverse personalizzazioni.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizzare la voce in un file

Successivamente, creare un oggetto [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest&preserve-view=true), che esegue le conversioni di sintesi vocale e invia l'output ad altoparlanti, file o altri flussi. [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest&preserve-view=true) accetta come parametri l'oggetto [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) creato nel passaggio precedente e un oggetto [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) che specifica come devono essere gestiti i risultati dell'output.

Per iniziare, creare un oggetto `AudioConfig` per scrivere automaticamente l'output in un file `.wav`, usando la funzione statica `fromAudioFileOutput()`.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Successivamente, creare un'istanza di `SpeechSynthesizer` passando l'oggetto `speechConfig` e l'oggetto `audioConfig` come parametri. Quindi, per eseguire la sintesi vocale e scrivere il risultato in un file, basta eseguire `speakTextAsync()` con una stringa di testo. Il callback del risultato è una valida posizione in cui eseguire la chiamata di `synthesizer.close()`, necessaria per il funzionamento corretto della sintesi.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Eseguire il programma. Un file `.wav` sintetizzato viene scritto nella posizione specificata. Si tratta di un esempio valido dell'utilizzo più semplice, ma in seguito viene illustrato come personalizzare l'output e gestire la relativa risposta in un flusso in memoria per scenari personalizzati.

## <a name="synthesize-to-speaker-output"></a>Sintetizzare l'output dell'altoparlante

In alcuni casi, si può scegliere di indirizzare la voce sintetizzata di output direttamente a un altoparlante. A questo scopo, creare un'istanza di `AudioConfig` usando la funzione statica `fromDefaultSpeakerOutput()`. L'output viene così indirizzato al dispositivo di output attivo corrente.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Ottenere il risultato come flusso in memoria

Per molti scenari di sviluppo di applicazioni vocali, i dati audio risultanti potrebbero essere necessari come flusso in memoria invece di essere scritti direttamente in un file. In questo modo è possibile creare un comportamento personalizzato per:

* Astrarre la matrice di byte risultante come flusso ricercabile per i servizi downstream personalizzati.
* Integrare il risultato con altri servizi o API.
* Modificare i dati audio, scrivere intestazioni di `.wav` personalizzate e così via.

È semplice apportare questa modifica dall'esempio precedente. Rimuovere prima di tutto il blocco `AudioConfig`, perché il comportamento di output verrà gestito manualmente da questo punto in poi per un maggior controllo. Passare quindi `undefined` per `AudioConfig` nel costruttore `SpeechSynthesizer`. 

> [!NOTE]
> Se si passa `undefined` per `AudioConfig`, invece di ometterlo come nel precedente esempio di output nell'altoparlante, l'audio non verrà riprodotto per impostazione predefinita nel dispositivo di output attivo corrente.

Questa volta il risultato viene salvato in una variabile [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest&preserve-view=true). La proprietà `SpeechSynthesisResult.audioData` restituisce un oggetto `ArrayBuffer` dei dati di output. È possibile usare tale `ArrayBuffer` manualmente.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Da qui è possibile implementare qualsiasi comportamento personalizzato usando l'oggetto `ArrayBuffer` risultante.

## <a name="customize-audio-format"></a>Personalizzare il formato audio

La sezione seguente illustra come personalizzare gli attributi dell'output audio, tra cui:

* Tipo di file audio
* Frequenza di campionamento
* Profondità di bit

Per cambiare il formato audio, usare la proprietà `speechSynthesisOutputFormat` nell'oggetto `SpeechConfig`. Questa proprietà prevede un oggetto `enum` di tipo [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest&preserve-view=true), che viene usato per selezionare il formato di output. Per un [elenco di formati audio](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest&preserve-view=true) disponibili, vedere la documentazione di riferimento.

Sono disponibili varie opzioni per tipi di file diversi a seconda dei requisiti. Si noti che, per definizione, i formati non elaborati come `Raw24Khz16BitMonoPcm` non includono intestazioni audio. Usare formati non elaborati solo quando si è certi che l'implementazione downstream possa decodificare un flusso di bit non elaborato o se si prevede di creare manualmente le intestazioni in base alla profondità di bit, alla frequenza di campionamento, al numero di canali e così via.

In questo esempio si specifica un formato RIFF ad alta fedeltà `Riff24Khz16BitMonoPcm` impostando `speechSynthesisOutputFormat` nell'oggetto `SpeechConfig`. Analogamente all'esempio riportato nella sezione precedente, ottenere i dati audio di `ArrayBuffer` e interagirvi.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Eseguendo il programma, anche in questo caso verrà scritto un file `.wav` nel percorso specificato.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Usare SSML per personalizzare le caratteristiche vocali

Il linguaggio SSML (Speech Synthesis Markup Language) consente di ottimizzare il tono, la pronuncia, la velocità del parlato, il volume e altro ancora dell'output della sintesi vocale inviando le richieste da XML Schema. Questa sezione illustra alcuni esempi pratici, ma per una guida dettagliata, vedere l'[articolo di procedure su SSML](../../../speech-synthesis-markup.md).

Per iniziare a usare SSML per la personalizzazione, è necessario apportare una semplice modifica che cambia la voce.
Creare prima di tutto un nuovo file XML per la configurazione di SSML nella directory radice del progetto, in questo esempio `ssml.xml`. L'elemento radice è sempre `<speak>` e il wrapping del testo in un elemento `<voice>` consente di cambiare la voce usando il parametro `name`. Questo esempio imposta una voce maschile in inglese (Regno Unito). Si noti che questa voce è **standard** e prevede prezzi e disponibilità differenti rispetto alle voci **neurali** . Vedere l' [elenco completo](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) di voci **standard** supportate.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Successivamente, è necessario cambiare la richiesta di sintesi vocale in modo che faccia riferimento al file XML. La richiesta è essenzialmente la stessa, ma invece di usare la funzione `speakTextAsync()` si usa `speakSsmlAsync()`. Questa funzione prevede una stringa XML, quindi creare prima di tutto una funzione per caricare un file XML e restituirlo come stringa.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Per altre informazioni su `readFileSync`, vedere <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">File system Node.js<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Da qui, l'oggetto risultato è esattamente lo stesso degli esempi precedenti.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

L'output funziona, ma è possibile apportare alcune altre semplici modifiche per rendere il suono più naturale. La velocità del parlato generale è leggermente troppo veloce, quindi aggiungere un tag `<prosody>` per ridurla al **90%** di quella predefinita. Inoltre, la pausa dopo la virgola nella frase è un po' troppo breve e risulta poco naturale. Per risolvere questo problema, aggiungere un tag `<break>` per ritardare la voce e impostare il parametro time su **200ms** . Eseguire di nuovo la sintesi per verificare l'effetto di queste personalizzazioni sull'output.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Voci neurali

Le voci neurali sono algoritmi di sintesi vocale basati su reti neurali profonde. Quando si usa una voce neurale, è praticamente impossibile distinguere la sintesi vocale dalle registrazioni umane. Con la prosodia naturale simile al linguaggio umano e l'articolazione chiara delle parole, le voci neurali riducono in modo significativo le difficoltà di ascolto durante l'interazione degli utenti con i sistemi di intelligenza artificiale.

Per passare a una voce neurale, impostare `name` su una delle [opzioni di voce neurale](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Aggiungere quindi uno spazio dei nomi XML per `mstts` ed eseguire il wrapping del testo nel tag `<mstts:express-as>`. Usare il parametro `style` per personalizzare lo stile di pronuncia. Questo esempio usa `cheerful`, ma provare a impostarlo su `customerservice` o su `chat` per vedere la differenza nello stile di pronuncia.

> [!IMPORTANT]
> Le voci neurali sono supportate **solo** per le risorse Voce create nelle aree *Stati Uniti orientali* , *Asia sud-orientale* ed *Europa occidentale* .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
