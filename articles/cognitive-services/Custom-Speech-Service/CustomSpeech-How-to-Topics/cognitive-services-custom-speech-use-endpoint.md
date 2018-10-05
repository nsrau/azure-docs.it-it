---
title: Usare un endpoint personalizzato per il riconoscimento vocale - Servizio di riconoscimento vocale personalizzato
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare un endpoint personalizzato per il riconoscimento vocale con il Servizio di riconoscimento vocale personalizzato.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 98c1b58e58490199b0258dfcc8df183c3fe9a8bd
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223373"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Usare un endpoint personalizzato per il riconoscimento vocale
È possibile inviare richieste a un endpoint di riconoscimento vocale del Servizio di riconoscimento vocale personalizzato di Azure, seguendo una procedura simile a quella relativa all'endpoint di riconoscimento vocale predefinito di Servizi cognitivi. Questi endpoint sono funzionalmente identici agli endpoint predefiniti dell'API Riconoscimento vocale. Pertanto, le stesse funzionalità disponibili tramite la libreria client o l'API REST per l'API Riconoscimento vocale sono disponibili anche per l'endpoint personalizzato.

Gli endpoint creati usando questo servizio possono elaborare numeri diversi di richieste simultanee. Il volume dipende il piano tariffario associato alla sottoscrizione. Se vengono ricevute troppe richieste, si verifica un errore. Il livello gratuito prevede un limite mensile delle richieste.

Il servizio presuppone che i dati vengano trasmessi in tempo reale. Se vengono inviati più velocemente, la richiesta sarà considerata in esecuzione finché non sarà trascorsa la sua durata audio in tempo reale.

> [!NOTE]
> I [nuovi Web Socket](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) sono già supportati. Se si prevede di usare Web Socket con l'endpoint personalizzato per il riconoscimento vocale, seguire le istruzioni riportate qui.
>
> Il supporto per la nuova [API REST](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) sarà presto disponibile. Se si prevede di chiamare l'endpoint personalizzato per il riconoscimento vocale tramite HTTP, seguire le istruzioni riportate qui.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Inviare richieste usando la libreria client per il riconoscimento vocale

Per inviare richieste all'endpoint personalizzato usando la libreria client per il riconoscimento vocale, avviare il client di riconoscimento. Usare il Client Speech SDK fornito da [NuGet](http://nuget.org/). Cercare *speech recognition* e selezionare il pacchetto di riconoscimento vocale Microsoft per la piattaforma in uso. Su [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows) sono disponibili esempi di codice. Il Client Speech SDK fornisce una classe factory **SpeechRecognitionServiceFactory**, che offre i metodi seguenti:

  *   ```CreateDataClient(...)```: un client di riconoscimento dati.
  *   ```CreateDataClientWithIntent(...)```: un client di riconoscimento dati con finalità.
  *   ```CreateMicrophoneClient(...)```: un client di riconoscimento microfonico.
  *   ```CreateMicrophoneClientWithIntent(...)```: un client di riconoscimento microfonico con finalità.

Per informazioni dettagliate, vedere l'[API Riconoscimento vocale Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home). Gli endpoint del Servizio di riconoscimento vocale personalizzato supportano lo stesso SDK.

Il client di riconoscimento dati è appropriato per il riconoscimento vocale da dati, ad esempio file o altre origini audio. Il client di riconoscimento microfonico è appropriato per il riconoscimento vocale dal microfono. L'uso delle finalità in entrambi i client può restituire risultati strutturati sulla finalità dal [Language Understanding Intelligent Service](https://www.luis.ai/) (LUIS), se è stata creata un'applicazione LUIS per lo scenario.

Per tutti e quattro i tipi di client, la creazione di un'istanza può essere eseguita in due modi. Il primo prevede l'uso dell'API Riconoscimento vocale standard di Servizi cognitivi. Il secondo consente di specificare un URL corrispondente all'endpoint personalizzato creato con il Servizio di riconoscimento vocale personalizzato.

Ad esempio, è possibile creare un **DataRecognitionClient** che invia richieste a un endpoint personalizzato usando il metodo seguente:

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

I valori **your_subscriptionId** e **endpointURL** fanno riferimento rispettivamente alla chiave di sottoscrizione e all'URL dei Web Socket nella pagina **Informazioni distribuzione**.

Il valore **AuthenticationUri** viene usato per ricevere un token dal servizio di autenticazione. Questo URI deve essere impostato separatamente, come illustrato nell'esempio di codice seguente.

Questo codice di esempio mostra come usare l'SDK del client:

```csharp
var dataClient = SpeechRecognitionServiceFactory.CreateDataClient(
  SpeechRecognitionMode.LongDictation,
  "en-us",
  "your_subscriptionId",
  "your_subscriptionId",
  "endpointURL");
// set the authorization Uri
dataClient.AuthenticationUri = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
```

> [!NOTE]
> Quando si usano metodi **Create** nell'SDK è necessario fornire l'ID sottoscrizione due volte, a causa dell'overload dei metodi **Create**.
>

Il Servizio di riconoscimento vocale personalizzato usa due URL diversi per il riconoscimento dettatura breve e il riconoscimento dettatura continua. Entrambi sono elencati nella pagina **Distribuzioni**. Usare l'URL dell'endpoint corretto per lo specifico tipo di riconoscimento da usare.

Per altre informazioni sulla chiamata dei vari client di riconoscimento con l'endpoint personalizzato, vedere la classe [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop). La documentazione in questa pagina si riferisce all'adattamento del modello acustico, ma si applica a tutti gli endpoint creati tramite il Servizio di riconoscimento vocale personalizzato.

## <a name="send-requests-by-using-the-speech-protocol"></a>Inviare richieste usando il protocollo di riconoscimento vocale

Gli endpoint visualizzati per il [protocollo di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) sono endpoint per il protocollo WebSocket open source di riconoscimento vocale.

Attualmente, l'unica implementazione client ufficiale è per [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Se si vuole iniziare con l'esempio fornito qui, apportare le modifiche seguenti al codice e compilare di nuovo l'esempio:

1. In _src\sdk\speech.browser\SpeechConnectionFactory.ts_ sostituire il nome host "wss://speech.platform.bing.com" con il nome host mostrato nella pagina dei dettagli della distribuzione. Non inserire l'URI completo, ma solo lo schema del protocollo *wss* e il nome host. Ad esempio: 

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Impostare il parametro _recognitionMode_ in _samples\browser\Samples.html_ in base ai propri requisiti:
    * _RecognitionMode.Interactive_ supporta richieste fino a 15 secondi.
    * _RecognitionMode.Conversation_ e _RecognitionMode.Dictation_ (equivalenti nel Servizio di riconoscimento vocale personalizzato) supportano richieste fino a 10 minuti.

3. Compilare l'esempio usando la "compilazione gulp" prima di usarlo.

> [!NOTE]
> Assicurarsi di usare l'URI corretto per questo protocollo. Lo schema richiesto è *wss* e non *http* come nel protocollo client. 

Per altre informazioni, vedere la documentazione dell'[API Riconoscimento vocale Bing](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries).

## <a name="send-requests-by-using-http"></a>Inviare richieste tramite HTTP

La procedura per inviare una richiesta all'endpoint personalizzato mediante un'operazione HTTP POST è simile a quella per inviare una richiesta tramite HTTP all'API Riconoscimento vocale Bing di Servizi cognitivi. Modificare l'URL in base all'indirizzo della distribuzione personalizzata.

Esistono alcune restrizioni relative alle richieste inviate tramite HTTP, sia per l'endpoint di riconoscimento vocale di Servizi cognitivi, sia per gli endpoint personalizzati creati con questo servizio. La richiesta HTTP non può restituire risultati parziali durante il processo di riconoscimento. Inoltre, la durata delle richieste è limitata a 10 secondi per il contenuto audio e 14 secondi complessivi.

Per creare una richiesta POST, seguire la stessa procedura usata per l'API Riconoscimento vocale di Servizi cognitivi.

1. Ottenere un token di accesso usando l'ID sottoscrizione personale. Questo token è necessario per accedere all'endpoint di riconoscimento. Può essere riutilizzato per 10 minuti.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      Il valore **subscriptionId** va impostato sull'ID sottoscrizione usato per questa distribuzione. La risposta è il token necessario per la richiesta successiva.

2. Inviare l'audio all'endpoint usando di nuovo POST.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    Il valore **token** è il token di accesso ricevuto con la chiamata precedente. Il valore **https_endpoint** è l'indirizzo completo dell'endpoint personalizzato per il riconoscimento vocale, mostrato nella pagina **Informazioni distribuzione**.

Per altre informazioni sui parametri HTTP POST e il formato della risposta, vedere la pagina sull'[API HTTP Riconoscimento vocale Bing di Servizi cognitivi](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Inviare richieste usando la libreria di servizi
La libreria di servizi consente al servizio di usare il cloud di trascrizione del Servizio di riconoscimento vocale Microsoft per convertire il parlato in testo in tempo reale, in modo che l'app client possa inviare audio e ricevere risultati di riconoscimento parziali simultaneamente e in modo asincrono. I dettagli sull'SDK del servizio sono disponibili [qui](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> Quando si usa la libreria di servizi è necessario modificare l'URI del provider di autorizzazione nell'implementazione di **IAuthorizationProvider** in https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>Passaggi successivi
* Migliorare l'accuratezza con un [modello acustico personalizzato](cognitive-services-custom-speech-create-acoustic-model.md).
* Migliorare l'accuratezza con un [modello linguistico personalizzato](cognitive-services-custom-speech-create-language-model.md).
