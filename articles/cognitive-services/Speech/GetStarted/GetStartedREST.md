---
title: Introduzione all'API Riconoscimento vocale Bing tramite REST | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Usare REST per accedere all'API Riconoscimento vocale in Servizi cognitivi Microsoft per convertire audio parlato in testo.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: fc40f20349e3424dbcf50f5ca0395b06a818566f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224055"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Guida introduttiva: Usare l'API REST Riconoscimento vocale Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Con il servizio Riconoscimento vocale Bing basato sul cloud, è possibile sviluppare applicazioni usando l'API REST per convertire audio parlato in testo.

## <a name="prerequisites"></a>Prerequisiti

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Eseguire la sottoscrizione a Speech API e ottenere una chiave di sottoscrizione per la versione di valutazione gratuita

Speech API fa parte di Servizi cognitivi (noto in precedenza come Project Oxford). È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita dalla pagina di [sottoscrizione di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/). Dopo aver selezionato Speech API, fare clic su **Ottieni la chiave API** per ottenere la chiave. Vengono restituite una chiave primaria e una chiave secondaria. Entrambe le chiavi sono legate alla stessa quota ed è quindi possibile usare indifferentemente una delle due.

> [!IMPORTANT]
>* Ottenere una chiave di sottoscrizione. Prima di accedere all'API REST, è necessario disporre di una [chiave di sottoscrizione](https://azure.microsoft.com/try/cognitive-services/).
>
>* Usare la chiave di sottoscrizione. Negli esempi REST seguenti sostituire YOUR_SUBSCRIPTION_KEY con la propria chiave di sottoscrizione. 
>
>* Per informazioni su come ottenere una chiave di sottoscrizione, consultare la pagina di [autenticazione](../how-to/how-to-authentication.md).

### <a name="prerecorded-audio-file"></a>File audio preregistrato

In questo esempio, viene usato un file audio registrato per illustrare come usare l'API REST. Registrare un file audio mentre si pronuncia una frase breve. Ad esempio, "Che tempo fa oggi?" oppure "Trova film divertenti". L'API Riconoscimento vocale supporta anche input da microfono esterno.

> [!NOTE]
> Nell'esempio si presuppone che l'audio venga registrato come file in formato WAV con **singolo canale PCM (mono) a 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Creare una richiesta di riconoscimento e inviarla al Servizio di riconoscimento vocale

Il passaggio successivo per il riconoscimento vocale consiste nell'invio di una richiesta POST agli endpoint HTTP del servizio con intestazione e corpo della richiesta appropriati.

### <a name="service-uri"></a>URI del servizio

L'URI del Servizio di riconoscimento vocale è definita in base alle [modalità di riconoscimento](../concepts.md#recognition-modes) e alle [lingue di riconoscimento](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` specifica la modalità di riconoscimento e deve essere uno dei valori seguenti: `interactive`, `conversation` o `dictation`. Si tratta di un percorso della risorsa obbligatorio nell'URI. Per altre informazioni, vedere [Modalità di riconoscimento](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` è un parametro obbligatorio nella stringa di query. Definisce la lingua di destinazione per la conversione audio: ad esempio `en-US` per l'inglese (Stati Uniti). Per altre informazioni, vedere [Modalità di riconoscimento](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` è un parametro facoltativo nella stringa di query. I valori consentiti sono `simple` e `detailed`. Per impostazione predefinita, il servizio restituisce i risultati in formato `simple`. Per altre informazioni, vedere [Formato di output](../concepts.md#output-format).

Alcuni esempi di URI del servizio sono elencati nella tabella seguente.

| Modalità di riconoscimento  | Linguaggio | Formato di output | URI del servizio |
|---|---|---|---|
| `interactive` | pt-BR | Predefinito | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Dettagliato |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Semplice | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> L'URI del servizio è necessario solo quando l'applicazione usa le API REST per chiamare il Servizio di riconoscimento vocale. Se si usa una delle [librerie client](GetStartedClientLibraries.md), in genere non occorre conoscere quale URI viene usato. Le librerie client possono usare diversi URI del servizio applicabili solo a una libreria client specifica. Per altre informazioni, vedere la libreria client scelta.

### <a name="request-headers"></a>Intestazioni della richiesta

I campi seguenti devono essere impostati nell'intestazione della richiesta:

- `Ocp-Apim-Subscription-Key`: Ogni volta che si chiama il servizio, è necessario passare la chiave di sottoscrizione nell'intestazione `Ocp-Apim-Subscription-Key`. Il Servizio di riconoscimento vocale supporta anche il passaggio dei token di autorizzazione anziché delle chiavi di sottoscrizione. Per altre informazioni, vedere [Autenticazione](../How-to/how-to-authentication.md).
- `Content-type`: Il campo `Content-type` descrive il formato e il codec del flusso audio. Attualmente, sono supportati solo i file in formato WAV e la codifica PCM 16000 mono. Il valore di Content-type per questo formato è `audio/wav; codec=audio/pcm; samplerate=16000`.

Il campo `Transfer-Encoding` è facoltativo. Se si imposta questo campo su `chunked`, è possibile suddividere l'audio in piccoli blocchi. Per altre informazioni, vedere [Trasferimento in blocchi](../How-to/how-to-chunked-transfer.md).

Di seguito è riportata un'intestazione della richiesta di esempio:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Inviare una richiesta al servizio

L'esempio seguente illustra come inviare una richiesta di riconoscimento vocale agli endpoint REST di riconoscimento vocale. Viene usata la modalità di riconoscimento `interactive`.

> [!NOTE]
> Sostituire `YOUR_AUDIO_FILE` con il percorso del file audio preregistrato. Sostituire `YOUR_SUBSCRIPTION_KEY` con la propria chiave di sottoscrizione.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

L'esempio usa curl in Linux con bash. Se non è disponibile nella piattaforma, può essere necessario installare curl. L'esempio funziona anche in Cygwin in Windows, Git Bash, zsh e altre shell.

> [!NOTE]
> Mantenere il simbolo `@` prima del nome del file audio quando si sostituisce `YOUR_AUDIO_FILE` con il percorso del file audio preregistrato, in quanto indica che il valore di `--data-binary` fa riferimento a un nome di file, non ai dati.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

## <a name="process-the-speech-recognition-response"></a>Elaborare la risposta di riconoscimento vocale

Dopo l'elaborazione della richiesta, il Servizio di riconoscimento vocale restituisce i risultati in una risposta in formato JSON.

> [!NOTE]
> Se il codice precedente restituisce un errore, vedere [Risoluzione dei problemi](../troubleshooting.md) per individuarne la causa.

Il frammento di codice seguente illustra come è possibile leggere la risposta dal flusso.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

In questo esempio, curl restituisce direttamente il messaggio di risposta in una stringa. Se si vuole visualizzare il messaggio in formato JSON, è possibile usare strumenti aggiuntivi, ad esempio jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

L'esempio seguente è una risposta JSON:

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Limitazioni

L'API REST presenta alcune limitazioni:

- Supporta flusso audio solo fino a 15 secondi.
- Non supporta i risultati intermedi durante il riconoscimento. Gli utenti ricevono solo il risultato di riconoscimento finale.

Per eliminare queste limitazioni, usare le [librerie client](GetStartedClientLibraries.md) per il riconoscimento vocale. In alternativa, è possibile usare direttamente il [protocollo WebSocket del Servizio di riconoscimento vocale](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Passaggi successivi

- Per informazioni sull'uso dell'API REST in C#, Java e così via, vedere queste [applicazioni di esempio](../samples.md).
- Per individuare e correggere errori, vedere [Risoluzione dei problemi](../troubleshooting.md).
- Per usare funzionalità più avanzate, vederne un'introduzione usando le [librerie client](GetStartedClientLibraries.md) per il riconoscimento vocale.

### <a name="license"></a>Licenza

Tutti gli SDK e gli esempi di Servizi cognitivi sono concessi su licenza con la licenza MIT. Per altre informazioni, vedere [Licenza](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
