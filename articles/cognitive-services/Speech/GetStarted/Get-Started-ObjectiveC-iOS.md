---
title: Introduzione all'API Riconoscimento vocale Bing in Objective-C per dispositivi iOS | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Usare l'API Riconoscimento vocale Bing per sviluppare applicazioni iOS che convertono audio parlato in testo.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e42784e6d2751f7e76aec8caf1d6e1f9f09a9fd1
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965924"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Avvio rapido: Usare l'API Riconoscimento vocale Bing in Objective-C per dispositivi iOS

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Con l'API Riconoscimento vocale, è possibile sviluppare applicazioni iOS che usano il Servizio di riconoscimento vocale basato sul cloud per convertire audio parlato in testo. L'API supporta lo streaming in tempo reale, in modo che l'applicazione possa ricevere in modo asincrono e simultaneo risultati parziali di riconoscimento e inviare audio al servizio.

Questo articolo usa un'applicazione di esempio per illustrare i concetti di base relativi all'uso dell'API Riconoscimento vocale per sviluppare un'applicazione iOS. Per informazioni di riferimento complete sull'API, vedere l'[SDK client per il riconoscimento vocale per Android](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Prerequisiti

### <a name="platform-requirements"></a>Requisiti di piattaforma

Assicurarsi che l'ambiente di sviluppo integrato Mac XCode sia installato.

### <a name="get-the-client-library-and-examples"></a>Ottenere la libreria client e gli esempi

Gli esempi e la libreria client per il riconoscimento vocale per iOS sono disponibili nell'[SDK client per il riconoscimento vocale per iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Eseguire la sottoscrizione all'API Riconoscimento vocale e ottenere una chiave di sottoscrizione della versione di valutazione gratuita

Speech API fa parte di Servizi cognitivi (noto in precedenza come Project Oxford). È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita dalla pagina di [sottoscrizione di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/). Dopo aver selezionato Speech API, fare clic su **Ottieni la chiave API** per ottenere la chiave. Vengono restituite una chiave primaria e una chiave secondaria. Entrambe le chiavi sono legate alla stessa quota ed è quindi possibile usare indifferentemente una delle due.

Per usare il *riconoscimento con finalità*, è inoltre necessario eseguire l'iscrizione al servizio [LUIS (Language Understanding Intelligent Service)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Ottenere una chiave di sottoscrizione. Prima di poter usare le librerie client per il riconoscimento vocale, è necessario avere una [chiave di sottoscrizione](https://azure.microsoft.com/try/cognitive-services/).
>
> * Usare la chiave di sottoscrizione. Con l'applicazione di esempio iOS fornita, è necessario aggiornare il file Samples/SpeechRecognitionServerExample/settings.plist con la chiave di sottoscrizione. Per altre informazioni, vedere [Compilare ed eseguire esempi](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Usare la libreria client per il riconoscimento vocale

Per aggiungere la libreria client a un progetto XCode, attenersi alle [istruzioni](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library) seguenti.

Per trovare informazioni di riferimento sulla libreria client per iOS, vedere questa [pagina Web](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Compilare ed eseguire esempi

Per informazioni su come compilare ed eseguire gli esempi, vedere questa [pagina LEGGIMI](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Descrizione degli esempi

### <a name="create-recognition-clients"></a>Creare client di riconoscimento

Il codice nell'esempio seguente illustra come creare classi client di riconoscimento in base agli scenari utente:

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

La libreria client fornisce classi client di riconoscimento pre-implementate per scenari di riconoscimento vocale tipici:

* `DataRecognitionClient`: riconoscimento vocale con dati PCM, ad esempio da un'origine file o audio. I dati sono suddivisi in buffer e ogni buffer viene inviato al Servizio di riconoscimento vocale. Al buffer non viene apportata alcuna modifica, pertanto gli utenti possono applicare il sistema di rilevamento dei silenzi preferito. Se i dati vengono forniti da file in formato WAV, è possibile inviare dati dal file direttamente al server. Se si dispone di dati non elaborati, ad esempio audio trasferito tramite Bluetooth, inviare innanzitutto un'intestazione di formato al server seguita dai dati.
* `MicrophoneRecognitionClient`: riconoscimento vocale con audio proveniente dal microfono. Verificare che il microfono sia acceso e che i dati del microfono vengano inviati al Servizio di riconoscimento vocale. Un "rilevatore di silenzi" integrato viene applicato ai dati del microfono prima dell'invio al Servizio di riconoscimento.
* `DataRecognitionClientWithIntent` e `MicrophoneRecognitionClientWithIntent`: oltre a testo di riconoscimento, questi client restituiscono informazioni strutturate sulla finalità del parlante, che le applicazioni possono usare per eseguire altre azioni. Per usare il "rilevamento della finalità", è innanzitutto necessario istruire un modello tramite il servizio [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Lingua di riconoscimento

Quando si usa `SpeechRecognitionServiceFactory` per creare il client, è necessario selezionare una lingua. Per l'elenco completo delle lingue supportate dal Servizio di riconoscimento vocale, vedere [Lingue supportate](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Quando si crea il client con `SpeechRecognitionServiceFactory`, è inoltre necessario specificare `SpeechRecognitionMode`:

* `SpeechRecognitionMode_ShortPhrase`: un'espressione fino a 15 secondi di durata. Quando i dati vengono inviati al servizio, il client riceve più risultati parziali e un risultato finale con N scelte migliori.
* `SpeechRecognitionMode_LongDictation`: un'espressione fino a 2 minuti di durata. Quando i dati vengono inviati al servizio, il client riceve più risultati parziali e più risultati finali, in base alle posizioni di pausa della frase identificate dal server.

### <a name="attach-event-handlers"></a>Collegare i gestori di eventi

È possibile collegare più gestori di eventi al client creato:

* **Eventi con risultati parziali**: chiamati ogni volta che il Servizio di riconoscimento vocale formula un'ipotesi su ciò che l'utente potrebbe dire, ancora prima che finisca di parlare (se si usa `MicrophoneRecognitionClient`) o completi l'invio dei dati (se si usa `DataRecognitionClient`).
* **Eventi con errore**: chiamati quando il servizio rileva un errore.
* **Eventi con finalità**: chiamati nei client "WithIntent" (solo nella modalità ShortPhrase) dopo che il risultato finale di riconoscimento viene analizzato come finalità JSON strutturata.
* **Eventi con risultato**:
  * Nella modalità `SpeechRecognitionMode_ShortPhrase` vengono chiamati questi eventi e restituiscono N risultati migliori quando l'utente finisce di parlare.
  * Nella modalità `SpeechRecognitionMode_LongDictation` il gestore di eventi viene chiamato più volte, in base alle posizioni di pausa della frase identificate dal servizio.
  * **Per ognuna delle N scelte migliori**, vengono restituiti un valore di attendibilità e diverse forme del testo riconosciuto. Per altre informazioni, vedere [Formato di output](../Concepts.md#output-format).

## <a name="related-topics"></a>Argomenti correlati

* [Client library reference for iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html) (Informazioni di riferimento sulla libreria client per iOS)
* [Introduzione all'API Riconoscimento vocale Microsoft e/o al Riconoscimento finalità in Java per dispositivi Android](GetStartedJavaAndroid.md)
* [Introduzione all'API Riconoscimento vocale Microsoft in JavaScript](GetStartedJSWebsockets.md)
* [Introduzione all'API Riconoscimento vocale Microsoft tramite REST](GetStartedREST.md)
