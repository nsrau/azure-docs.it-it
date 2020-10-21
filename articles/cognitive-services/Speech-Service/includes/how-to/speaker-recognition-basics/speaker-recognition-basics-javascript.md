---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: bb78a60b911823da96c52a104a3e06ecfc634da6
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210853"
---
Questo argomento di avvio rapido illustra i modelli di progettazione di base per Riconoscimento del parlante tramite Speech SDK, tra cui:

* Verifica dipendente dal testo e indipendente dal testo
* Identificazione voce per identificare un campione di voce tra un gruppo di voci
* Eliminazione dei profili vocali

Per informazioni generali sui concetti di Riconoscimento del parlante, vedere l'articolo [Panoramica](../../../speaker-recognition-overview.md).

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> La funzionalità Riconoscimento del parlante è attualmente supportata *solo* nelle risorse Voce di Azure create nell'area `westus`.

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

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi di questo articolo, aggiungere le istruzioni seguenti all'inizio del file JS.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Queste istruzioni importano le librerie necessarie e recuperano la chiave di sottoscrizione e l'area del servizio Voce dalle variabili di ambiente. Specificano inoltre i percorsi dei file audio che verranno usati nelle attività seguenti.

## <a name="create-helper-function"></a>Creare la funzione helper

Aggiungere la funzione helper seguente per la lettura dei file audio nei flussi per l'uso da parte del servizio Voce.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

In questa funzione si usano i metodi [AudioInputStream.createPushStream](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream?view=azure-node-latest&preserve-view=true#createpushstream-audiostreamformat-) e [AudioConfig.fromStreamInput](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) per creare un oggetto [AudioConfig](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true). L'oggetto `AudioConfig` rappresenta un flusso audio. Verranno usati numerosi oggetti `AudioConfig` nel corso delle attività seguenti.

## <a name="text-dependent-verification"></a>Verifica dipendente dal testo

Verifica voce è l'atto di confermare che un parlante corrisponde a una voce nota o **registrata**. Il primo passaggio consiste nel **registrare** un profilo vocale, in modo che il servizio disponga di un profilo da confrontare con i campioni di voce futuri. In questo esempio, il profilo viene registrato usando una strategia **dipendente dal testo**, che richiede una passphrase specifica da usare per la registrazione e la verifica. Per un elenco delle passphrase supportate, vedere la [documentazione di riferimento](https://docs.microsoft.com/rest/api/speakerrecognition/).

### <a name="textdependentverification-function"></a>Funzione TextDependentVerification

Per iniziare, creare la funzione `TextDependentVerification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Questa funzione crea un oggetto [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) con il metodo [VoiceProfileClient.createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-). Si noti che esistono tre [tipi](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype?view=azure-node-latest&preserve-view=true) di profili `VoiceProfile`:

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

In questo caso, si passerà `VoiceProfileType.TextDependentVerification` a `VoiceProfileClient.createProfileAsync`.

Verranno chiamate quindi due funzioni helper che verranno definite avanti, `AddEnrollmentsToTextDependentProfile` e `SpeakerVerify`. Infine, si chiamerà [VoiceProfileClient.deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) per rimuovere il profilo.

### <a name="addenrollmentstotextdependentprofile-function"></a>Funzione AddEnrollmentsToTextDependentProfile

Definire la funzione seguente per registrare un profilo vocale.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

In questa funzione viene chiamata la funzione `GetAudioConfigFromFile` definita in precedenza per creare `AudioConfig` oggetti dai campioni audio. Questi campioni audio contengono una passphrase, ad esempio "My voice is my passport, verify me". Questi campioni audio verranno quindi registrati usando il metodo [VoiceProfileClient.enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-).

### <a name="speakerverify-function"></a>Funzione SpeakerVerify

Definire `SpeakerVerify` come indicato di seguito.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

In questa funzione viene creato un oggetto [SpeakerVerificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest&preserve-view=true) con il metodo [SpeakerVerificationModel.FromProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest&preserve-view=true#fromprofile-voiceprofile-), passando l'oggetto [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) creato in precedenza.

Verrà quindi chiamato il metodo [SpeechRecognizer.recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) per convalidare un campione audio che contiene la stessa passphrase dei campioni audio registrati in precedenza. `SpeechRecognizer.recognizeOnceAsync` restituisce un oggetto [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest&preserve-view=true), la cui proprietà `score` contiene un punteggio di somiglianza compreso tra 0,0 e 1,0. L'oggetto `SpeakerRecognitionResult` contiene inoltre una proprietà `reason` di tipo [ResultReason](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason?view=azure-node-latest&preserve-view=true). Se la verifica ha avuto esito positivo, il valore della proprietà `reason` sarà `RecognizedSpeaker`.

## <a name="text-independent-verification"></a>Verifica indipendente dal testo

A differenza della verifica **dipendente dal testo**, la verifica **indipendente dal testo**:

* Non richiede che venga pronunciata una passphrase specifica, ma è possibile pronunciare le parole desiderate
* Non richiede tre campioni audio, ma *richiede* 20 secondi di audio totale

### <a name="textindependentverification-function"></a>Funzione TextIndependentVerification

Per iniziare, creare la funzione `TextIndependentVerification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

Come la funzione `TextDependentVerification`, questa funzione crea un oggetto [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) con il metodo [VoiceProfileClient.createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-).

In questo caso, si passerà `VoiceProfileType.TextIndependentVerification` a `createProfileAsync`.

Si chiamano quindi due funzioni helper: `AddEnrollmentsToTextIndependentProfile`, che verrà definita in seguito e `SpeakerVerify`, che è già stata definita. Infine, si chiamerà [VoiceProfileClient.deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) per rimuovere il profilo.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Definire la funzione seguente per registrare un profilo vocale.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

In questa funzione viene chiamata la funzione `GetAudioConfigFromFile` definita in precedenza per creare `AudioConfig` oggetti dai campioni audio. Questi campioni audio verranno quindi registrati usando il metodo [VoiceProfileClient.enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-).

## <a name="speaker-identification"></a>Identificazione voce

La funzionalità Identificazione voce viene usata per determinare **chi** parla da un gruppo specifico di voci registrate. Il processo è simile a quello di **verifica indipendente dal testo**, con la differenza principale che può eseguire la verifica rispetto a più profili vocali contemporaneamente, anziché rispetto a un singolo profilo.

### <a name="textindependentidentification-function"></a>Funzione TextIndependentIdentification

Per iniziare, creare la funzione `TextIndependentIdentification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

Come le funzioni `TextDependentVerification` e `TextIndependentVerification`, questa funzione crea un oggetto [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) con il metodo [VoiceProfileClient.createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-).

In questo caso, si passerà `VoiceProfileType.TextIndependentIdentification` a `VoiceProfileClient.createProfileAsync`.

Si chiamano quindi due funzioni helper: `AddEnrollmentsToTextIndependentProfile`, che è già stata definita e `SpeakerIdentify`, che verrà definita in seguito. Infine, si chiamerà [VoiceProfileClient.deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) per rimuovere il profilo.

### <a name="speakeridentify-function"></a>Funzione SpeakerIdentify

Definire la funzione `SpeakerIdentify` come indicato di seguito.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

In questa funzione viene creato un oggetto [SpeakerIdentificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest&preserve-view=true) con il metodo [SpeakerIdentificationModel.fromProfiles](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest&preserve-view=true#fromprofiles-voiceprofile---), passando l'oggetto [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) creato in precedenza.

Verrà quindi chiamato il metodo [SpeechRecognizer.recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) e verrà passato un campione audio.
`SpeechRecognizer.recognizeOnceAsync` tenta di identificare la voce per questo campione audio in base agli oggetti `VoiceProfile` usati per creare il modello `SpeakerIdentificationModel`. Restituisce un oggetto [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest&preserve-view=true), la cui proprietà `profileId` identifica la corrispondenza `VoiceProfile`, se presente, mentre la proprietà `score` contiene un punteggio di somiglianza compreso tra 0,0 e 1,0.

## <a name="main-function"></a>Funzione main

Infine, definire la funzione `main` come indicato di seguito.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Questa funzione crea un oggetto [VoiceProfileClient](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true), che viene usato per creare, registrare ed eliminare i profili vocali, e quindi chiama le funzioni definite in precedenza.
