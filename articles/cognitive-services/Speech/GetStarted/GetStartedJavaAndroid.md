---
title: Introduzione all'API Riconoscimento vocale Microsoft in Java per dispositivi Android | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Usare l'API Riconoscimento vocale Microsoft per sviluppare applicazioni Android che convertono audio parlato in testo.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 147042e300e629dd7e354d4e9079cc4855a8146c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515189"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Guida introduttiva: Usare l'API Riconoscimento vocale Bing in Java per dispositivi Android

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Con l'API Riconoscimento vocale Bing è possibile sviluppare applicazioni Android che usano il Servizio di riconoscimento vocale Bing basato sul cloud per convertire audio parlato in testo. L'API supporta lo streaming in tempo reale, in modo che l'applicazione possa ricevere in modo asincrono e simultaneo risultati parziali di riconoscimento e inviare audio al servizio.

Questo articolo usa un'applicazione di esempio per illustrare come usare la libreria client per il riconoscimento vocale per Android per sviluppare applicazioni di riconoscimento vocale in Java per dispositivi Android.

## <a name="prerequisites"></a>Prerequisiti

### <a name="platform-requirements"></a>Requisiti di piattaforma

L'esempio viene sviluppato da [Android Studio](https://developer.android.com/sdk/index.html) per Windows in Java.

### <a name="get-the-client-library-and-sample-application"></a>Ottenere la libreria client e l'applicazione di esempio

Gli esempi e la libreria client per il riconoscimento vocale per Android sono disponibili nell'[SDK client per il riconoscimento vocale per Android](https://github.com/microsoft/cognitive-speech-stt-android). L'esempio per la compilazione è disponibile nella directory samples/SpeechRecoExample. Le due librerie da usare nelle app sono disponibili in SpeechSDK/libs nelle cartelle armeabi e x86. La dimensione del file libandroid_platform.so è 22 MB, ma si riduce a 4 MB in fase di distribuzione.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Eseguire la sottoscrizione a Speech API e ottenere una chiave di sottoscrizione per la versione di valutazione gratuita

Speech API fa parte di Servizi cognitivi (noto in precedenza come Project Oxford). È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita dalla pagina di [sottoscrizione di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/). Dopo aver selezionato Speech API, fare clic su **Ottieni la chiave API** per ottenere la chiave. Vengono restituite una chiave primaria e una chiave secondaria. Entrambe le chiavi sono legate alla stessa quota ed è quindi possibile usare indifferentemente una delle due.

Per usare il *riconoscimento con finalità*, è inoltre necessario eseguire l'iscrizione al servizio [LUIS (Language Understanding Intelligent Service)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Ottenere una chiave di sottoscrizione. Prima di poter usare le librerie client per il riconoscimento vocale, è necessario avere una [chiave di sottoscrizione](https://azure.microsoft.com/try/cognitive-services/).
>
>* Usare la chiave di sottoscrizione. Con l'applicazione di esempio Android fornita, aggiornare il file samples/SpeechRecoExample/res/values/strings.xml con le chiavi di sottoscrizione. Per altre informazioni, vedere [Compilare ed eseguire esempi](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Usare la libreria client per il riconoscimento vocale

Per usare la libreria client nell'applicazione, seguire le [istruzioni](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Le informazioni di riferimento relative alla libreria client per Android sono disponibili nella cartella docs dell'[SDK client per il riconoscimento vocale per Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Compilare ed eseguire esempi

Per informazioni su come compilare ed eseguire gli esempi, vedere questa [pagina LEGGIMI](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Descrizione degli esempi

### <a name="create-recognition-clients"></a>Creare client di riconoscimento

Il codice nell'esempio seguente illustra come creare classi client di riconoscimento in base agli scenari utente:

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

La libreria client fornisce classi client di riconoscimento pre-implementate per scenari di riconoscimento vocale tipici:

* `DataRecognitionClient`: riconoscimento vocale con dati PCM, ad esempio da un'origine file o audio. I dati sono suddivisi in buffer e ogni buffer viene inviato al Servizio di riconoscimento vocale. Al buffer non viene apportata alcuna modifica, pertanto l'utente può applicare il sistema di rilevamento dei silenzi preferito. Se i dati vengono forniti da file in formato WAV, è possibile inviare dati dal file direttamente al Servizio di riconoscimento vocale. Se si dispone di dati non elaborati, ad esempio audio trasferito tramite Bluetooth, inviare innanzitutto un'intestazione di formato al Servizio di riconoscimento vocale seguita dai dati.
* `MicrophoneRecognitionClient`: riconoscimento vocale con audio proveniente dal microfono. Verificare che il microfono sia acceso e che i dati del microfono vengano inviati al Servizio di riconoscimento vocale. Un "rilevatore di silenzi" integrato viene applicato ai dati del microfono prima dell'invio al Servizio di riconoscimento.
* `DataRecognitionClientWithIntent` e `MicrophoneRecognitionClientWithIntent`: questi client restituiscono, oltre a testo di riconoscimento, informazioni strutturate sulla finalità del parlante, che le applicazioni possono usare per altre azioni. Per usare il "rilevamento della finalità", è innanzitutto necessario istruire un modello tramite il servizio [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Lingua di riconoscimento

Quando si usa `SpeechRecognitionServiceFactory` per creare il client, è necessario selezionare una lingua. Per l'elenco completo delle lingue supportate dal Servizio di riconoscimento vocale, vedere [Lingue supportate](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Quando si crea il client con `SpeechRecognitionServiceFactory`, è inoltre necessario specificare `SpeechRecognitionMode`:

* `ShortPhrase`: un'espressione fino a 15 secondi di durata. Quando i dati vengono inviati al servizio, il client riceve più risultati parziali e un risultato finale con N scelte migliori.
* `LongDictation`: un'espressione fino a 2 minuti di durata. Quando i dati vengono inviati al servizio, il client riceve più risultati parziali e più risultati finali, in base alle posizioni di pausa della frase identificate dal servizio.

### <a name="attach-event-handlers"></a>Collegare i gestori di eventi

È possibile collegare più gestori di eventi al client creato:

* **Eventi con risultati parziali**: Questi eventi vengono chiamati ogni volta che il Servizio di riconoscimento vocale formula un'ipotesi su ciò che l'utente potrebbe dire, ancora prima che finisca di parlare (se si usa `MicrophoneRecognitionClient`) o completi l'invio dei dati (se si usa `DataRecognitionClient`).
* **Eventi con errore**: chiamati quando il servizio rileva un errore.
* **Eventi con finalità**: chiamati nei client "WithIntent" (solo nella modalità `ShortPhrase`) dopo che il risultato finale di riconoscimento viene analizzato come finalità JSON strutturata.
* **Eventi con risultato**:
  * Nella modalità `ShortPhrase` vengono chiamati questi eventi e restituiscono N risultati migliori quando l'utente finisce di parlare.
  * Nella modalità `LongDictation` il gestore di eventi viene chiamato più volte, in base alle posizioni di pausa della frase identificate dal servizio.
  * **Per ognuna delle N scelte migliori**, vengono restituiti un valore di attendibilità e diverse forme del testo riconosciuto. Per altre informazioni, vedere [Formato di output](../Concepts.md#output-format).

## <a name="related-topics"></a>Argomenti correlati

* [Client library reference for Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs) (Informazioni di riferimento sulla libreria client per Android)
* [Introduzione all'API Riconoscimento vocale Microsoft in C# per .NET per dispositivi Windows](GetStartedCSharpDesktop.md)
* [Introduzione all'API Riconoscimento vocale Microsoft in Objective-C per dispositivi iOS](Get-Started-ObjectiveC-iOS.md)
* [Introduzione all'API Riconoscimento vocale Microsoft in JavaScript](GetStartedJSWebsockets.md)
* [Introduzione all'API Riconoscimento vocale Microsoft tramite REST](GetStartedREST.md)
