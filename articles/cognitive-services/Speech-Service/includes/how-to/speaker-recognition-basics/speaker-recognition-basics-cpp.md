---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 489514068d83f7b2953732415ba066a2d4555df8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015489"
---
Questo argomento di avvio rapido illustra i modelli di progettazione di base per Riconoscimento del parlante tramite Speech SDK, tra cui:

* Verifica dipendente dal testo e indipendente dal testo
* Identificazione voce per identificare un campione di voce tra un gruppo di voci
* Eliminazione dei profili vocali

Per informazioni generali sui concetti di Riconoscimento del parlante, vedere l'articolo [Panoramica](../../../speaker-recognition-overview.md).

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> La funzionalità Riconoscimento del parlante è attualmente supportata *solo* nelle risorse Voce di Azure create nell'area `westus`.

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare Speech SDK. A seconda della piattaforma, usare le istruzioni seguenti:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi di questo articolo, aggiungere le istruzioni seguenti all'inizio del file CPP.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Per chiamare il servizio Voce con Speech SDK, è necessario creare una classe [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig). Questa classe include informazioni sulla sottoscrizione, ad esempio la chiave e l'area associata, l'endpoint, l'host o il token di autorizzazione.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>Verifica dipendente dal testo

Verifica voce è l'atto di confermare che un parlante corrisponde a una voce nota o **registrata**. Il primo passaggio consiste nel **registrare** un profilo vocale, in modo che il servizio disponga di un profilo da confrontare con i campioni di voce futuri. In questo esempio, il profilo viene registrato usando una strategia **dipendente dal testo**, che richiede una passphrase specifica da usare per la registrazione e la verifica. Per un elenco delle passphrase supportate, vedere la [documentazione di riferimento](/rest/api/speakerrecognition/).

### <a name="textdependentverification-function"></a>Funzione TextDependentVerification

Per iniziare, creare la funzione `TextDependentVerification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

Questa funzione crea un oggetto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) con il metodo [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync). Si noti che esistono tre [tipi](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype) di profili `VoiceProfile`:

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

In questo caso, si passerà `VoiceProfileType::TextDependentVerification` a `CreateProfileAsync`.

Verranno chiamate quindi due funzioni helper che verranno definite avanti, `AddEnrollmentsToTextDependentProfile` e `SpeakerVerify`. Infine, si chiamerà [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) per eseguire la pulizia del profilo.

### <a name="addenrollmentstotextdependentprofile-function"></a>Funzione AddEnrollmentsToTextDependentProfile

Definire la funzione seguente per registrare un profilo vocale.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

In questa funzione si registrano quindi i campioni audio in un ciclo `while` che tiene traccia del numero di campioni rimanenti e necessari per la registrazione. In ogni iterazione [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) chiederà di pronunciare la passphrase nel microfono e aggiungerà il campione al profilo vocale.

### <a name="speakerverify-function"></a>Funzione SpeakerVerify

Definire `SpeakerVerify` come indicato di seguito.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

In questa funzione viene creato un oggetto [SpeakerVerificationModel](/cpp/cognitive-services/speech/speakerverificationmodel) con il metodo [SpeakerVerificationModel::FromProfile](/cpp/cognitive-services/speech/speakerverificationmodel#fromprofile), passando l'oggetto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) creato in precedenza.

Successivamente, [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) richiede di pronunciare di nuovo la passphrase, ma questa volta la convaliderà in base al profilo vocale e restituirà un punteggio di somiglianza compreso tra 0,0 e 1,0. L'oggetto [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speakerrecognitionresult) restituisce anche `Accept` o `Reject`, a seconda che la passphrase corrisponda o meno.

## <a name="text-independent-verification"></a>Verifica indipendente dal testo

A differenza della verifica **dipendente dal testo**, la verifica **indipendente dal testo**:

* Non richiede che venga pronunciata una passphrase specifica, ma è possibile pronunciare le parole desiderate
* Non richiede tre campioni audio, ma *richiede* 20 secondi di audio totale

### <a name="textindependentverification-function"></a>Funzione TextIndependentVerification

Per iniziare, creare la funzione `TextIndependentVerification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

Come la funzione `TextDependentVerification`, questa funzione crea un oggetto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) con il metodo [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync).

In questo caso, si passerà `VoiceProfileType::TextIndependentVerification` a `CreateProfileAsync`.

Si chiamano quindi due funzioni helper: `AddEnrollmentsToTextIndependentProfile`, che verrà definita in seguito e `SpeakerVerify`, che è già stata definita. Infine, si chiamerà [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) per eseguire la pulizia del profilo.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Definire la funzione seguente per registrare un profilo vocale.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

In questa funzione si registrano quindi i campioni audio in un ciclo `while` che tiene traccia del numero di secondi di audio rimanenti e necessari per la registrazione. In ogni iterazione [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) chiederà di pronunciare parole nel microfono e aggiungerà il campione al profilo vocale.

## <a name="speaker-identification"></a>Identificazione voce

La funzionalità Identificazione voce viene usata per determinare **chi** parla da un gruppo specifico di voci registrate. Il processo è molto simile a quello di **verifica indipendente dal testo**, con la differenza principale che può eseguire la verifica rispetto a più profili vocali contemporaneamente, anziché rispetto a un singolo profilo.

### <a name="textindependentidentification-function"></a>Funzione TextIndependentIdentification

Per iniziare, creare la funzione `TextIndependentIdentification`.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

Come le funzioni `TextDependentVerification` e `TextIndependentVerification`, questa funzione crea un oggetto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) con il metodo [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync).

In questo caso, si passerà `VoiceProfileType::TextIndependentIdentification` a `CreateProfileAsync`.

Si chiamano quindi due funzioni helper: `AddEnrollmentsToTextIndependentProfile`, che è già stata definita e `SpeakerIdentify`, che verrà definita in seguito. Infine, si chiamerà [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) per eseguire la pulizia del profilo.

### <a name="speakeridentify-function"></a>Funzione SpeakerIdentify

Definire la funzione `SpeakerIdentify` come indicato di seguito.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

In questa funzione viene creato un oggetto [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speakeridentificationmodel) con il metodo [SpeakerIdentificationModel::FromProfiles](/cpp/cognitive-services/speech/speakeridentificationmodel#fromprofiles). `SpeakerIdentificationModel::FromProfiles` accetta un elenco di oggetti [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile). In questo caso, si passerà solo l'oggetto `VoiceProfile` creato in precedenza. Tuttavia, se si desidera, è possibile passare più oggetti `VoiceProfile`, ognuno dei quali è registrato con campioni audio da un'altra voce.

Successivamente, [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) richiederà di parlare ancora. Questa volta confronta la voce con i profili vocali registrati e restituisce il profilo vocale più simile.

## <a name="main-function"></a>Funzione main

Infine, definire la funzione `main` come indicato di seguito.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

Questa funzione chiama semplicemente le funzioni definite in precedenza. Per prima cosa, tuttavia, crea un oggetto [VoiceProfileClient](/cpp/cognitive-services/speech/voiceprofileclient) e un oggetto [SpeakerRecognizer](/cpp/cognitive-services/speech/speakerrecognizer).

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

L'oggetto `VoiceProfileClient` viene usato per creare, registrare ed eliminare i profili vocali. L'oggetto `SpeakerRecognizer` viene usato per convalidare i campioni vocali rispetto a uno o più profili vocali registrati.

## <a name="changing-audio-input-type"></a>Modifica del tipo di input audio

Gli esempi in questo articolo usano il microfono del dispositivo predefinito come input per i campioni audio. Tuttavia, negli scenari in cui è necessario usare file audio anziché l'input del microfono, è sufficiente modificare la riga seguente:

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

in:

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

In alternativa, è anche possibile sostituire l'uso di `audio_config` con [Audio::AudioConfig::FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput). È anche possibile usare una combinazione di input, ad esempio un microfono per la registrazione e i file per la verifica.