---
title: Informazioni di riferimento sulle API di sintesi vocale (REST) - Servizio di riconoscimento vocaleSpeech-to-text API reference (REST) - Speech service
titleSuffix: Azure Cognitive Services
description: Scopri come usare l'API REST dal riconoscimento vocale. In questo articolo vengono illustrate le opzioni di autorizzazione, le opzioni di query, come strutturare una richiesta e ricevere una risposta.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 759ea697e4093da5bfc1c082c886c6dfda636f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474799"
---
# <a name="speech-to-text-rest-api"></a>API REST di riconoscimento vocale

In alternativa [all'SDK di riconoscimento vocale,](speech-sdk.md)il servizio di riconoscimento vocale consente di convertire il riconoscimento vocale in testo tramite un'API REST. Ogni endpoint accessibile è associato a un'area. L'applicazione richiede una chiave di sottoscrizione per l'endpoint che si intende usare. L'API REST è molto limitata e deve essere usata solo nei casi in cui [Speech SDK](speech-sdk.md) non è in grado di.

Prima di usare l'API REST di sintesi vocale, comprendere:Before using the speech-to-text REST API, understand:

* Le richieste che utilizzano l'API REST e trasmettono audio direttamente possono contenere solo fino a 60 secondi di audio.
* L'API REST per il riconoscimento vocale restituisce solo i risultati finali. I risultati parziali non sono disponibili.

Se l'invio di audio più lungo è un requisito per l'applicazione, è consigliabile usare [Speech SDK](speech-sdk.md) o un'API REST basata su file, ad esempio [la trascrizione batch.](batch-transcription.md)

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Aree ed endpoint

L'endpoint per l'API REST ha questo formato:The endpoint for the REST API has this format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Sostituire `<REGION_IDENTIFIER>` con l'identificatore corrispondente all'area della sottoscrizione da questa tabella:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Il parametro di lingua deve essere aggiunto all'URL per evitare di ricevere un errore 4xx HTTP. Ad esempio, la lingua impostata su inglese (Stati Uniti) usando l'endpoint per l'area Stati Uniti occidentali è: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

## <a name="query-parameters"></a>Parametri di query

Questi parametri possono essere inclusi nella stringa di query della richiesta REST.

| Parametro | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `language` | Identifica la lingua parlata che viene riconosciuta. Vedere [Lingue supportate](language-support.md#speech-to-text). | Obbligatoria |
| `format` | Specifica il formato del risultato. I valori accettati sono `simple` e `detailed`. I risultati semplici includono `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. Le risposte dettagliate includono più risultati con valori di attendibilità e quattro diverse rappresentazioni. L'impostazione predefinita è `simple`. | Facoltativo |
| `profanity` | Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori `masked`accettati sono , che sostituisce la parola volgarità con `removed` `raw`asterischi, che rimuove tutte le volgarità dal risultato, o , che include la volgarità nel risultato. L'impostazione predefinita è `masked`. | Facoltativo |
| `cid` | Quando si usa il [portale di riconoscimento vocale personalizzato](how-to-custom-speech.md) per creare modelli personalizzati, è possibile usare i modelli personalizzati tramite l'ID endpoint disponibile nella pagina Distribuzione.When using the Custom Speech portal to create custom models, you can use custom models via their **Endpoint ID** found on the **Deployment** page. Utilizzare **l'ID endpoint** come `cid` argomento del parametro della stringa di query. | Facoltativo |

## <a name="request-headers"></a>Intestazioni della richiesta

Questa tabella elenca le intestazioni obbligatorie e facoltative per le richieste di riconoscimento vocale.

|Intestazione| Descrizione | Obbligatoria / Facoltativa |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | La chiave di sottoscrizione al Servizio di riconoscimento vocale dell'utente. | È necessaria questa intestazione o `Authorization`. |
| `Authorization` | Un token di autorizzazione preceduto dalla parola `Bearer`. Per altre informazioni, vedere [Autenticazione](#authentication). | È necessaria questa intestazione o `Ocp-Apim-Subscription-Key`. |
| `Content-type` | Descrive il formato e il codec dei dati audio forniti. I valori accettati sono `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Obbligatoria |
| `Transfer-Encoding` | Specifica che vengono inviati i dati audio in blocchi, anziché un singolo file. Utilizzare questa intestazione solo se vi è stata la suddivisione in blocchi dei dati audio. | Facoltativo |
| `Expect` | Se si usa il trasferimento in blocchi, inviare `Expect: 100-continue`. Il Servizio di riconoscimento vocale legge la richiesta iniziale e attende ulteriori dati.| Obbligatorio in caso di invio di dati audio in blocchi. |
| `Accept` | Se specificato, deve essere `application/json`. Il servizio di riconoscimento vocale fornisce i risultati in JSON. Alcuni framework di richiesta forniscono un valore predefinito incompatibile. È buona norma includere `Accept`sempre . | Facoltativo, ma consigliato. |

## <a name="audio-formats"></a>Formati audio

L'audio viene inviato nel corpo della richiesta HTTP `POST`. Deve essere in uno dei formati elencati in questa tabella:

| Format | Codec | Bitrate | Frequenza di campionamento  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16 bit  | 16 kHz, mono |
| OGG    | OPUS  | 16 bit  | 16 kHz, mono |

>[!NOTE]
>I formati precedenti sono supportati tramite l'API REST e WebSocket nel servizio di riconoscimento vocale. [Speech SDK](speech-sdk.md) supporta attualmente il formato WAV con codec PCM e [altri formati.](how-to-use-codec-compressed-audio-input-streams.md)

## <a name="sample-request"></a>Richiesta di esempio

L'esempio seguente include il nome host e le intestazioni richieste. È importante notare che il servizio prevede anche i dati audio, che non sono inclusi in questo esempio. Come accennato in precedenza, la suddivisione in blocchi è consigliabile, tuttavia, non è necessaria.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>Codici di stato HTTP

Il codice di stato HTTP di ogni risposta indica esito positivo o errori comuni.

| Stato codice HTTP | Descrizione | Possibile motivo |
|------------------|-------------|-----------------|
| `100` | Continue | La richiesta iniziale è stata accettata. Procedere con l'invio del resto dei dati. (Utilizzato con trasferimento in blocchi) |
| `200` | OK | La richiesta ha avuto esito positivo; il corpo della risposta è un oggetto JSON. |
| `400` | Richiesta non valida | Codice lingua non fornito, non una lingua supportata, un file audio non valido e così via. |
| `401` | Non autorizzata | La chiave di sottoscrizione o il token di autorizzazione non è valido nell'area specificata o l'endpoint non è valido. |
| `403` | Accesso negato | Manca la chiave di sottoscrizione o il token di autorizzazione. |

## <a name="chunked-transfer"></a>Trasferimento in blocchi

Il trasferimento`Transfer-Encoding: chunked`in blocchi ( ) consente di ridurre la latenza di riconoscimento. Consente al servizio di riconoscimento vocale di iniziare l'elaborazione del file audio durante la trasmissione. L'API REST non fornisce risultati provvisori o parziali.

Questo esempio di codice mostra come inviare audio in blocchi. Solo il primo blocco deve contenere l'intestazione del file audio. `request`è `HttpWebRequest` un oggetto connesso all'endpoint REST appropriato. `audioFile` è il percorso di un file audio su disco.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Parametri di risposta

I risultati vengono forniti in formato JSON. Il formato `simple` include i campi di primo livello seguenti.

| Parametro | Descrizione  |
|-----------|--------------|
|`RecognitionStatus`|Lo stato, ad esempio `Success` per il riconoscimento con esito positivo. Vedere la tabella successiva.|
|`DisplayText`|Il testo riconosciuto dopo l'uso delle maiuscole, la punteggiatura, la normalizzazione del testo inverso (conversione del testo parlato in forme più brevi, ad esempio 200 per "duecento" o "Dr. Smith" per "doctor smith"), e la mascheravolezza volgarità. Presente solo con esito positivo.|
|`Offset`|Il tempo (in unità di 100 nanosecondi) in corrispondenza del quale inizia l'input vocale riconosciuto nel flusso audio.|
|`Duration`|La durata (in unità di 100 nanosecondi) dell'input vocale riconosciuto nel flusso audio.|

Il campo `RecognitionStatus` può contenere questi valori:

| Stato | Descrizione |
|--------|-------------|
| `Success` | Il riconoscimento ha avuto esito positivo e il campo `DisplayText` è presente. |
| `NoMatch` | La parte parlata è stata rilevata nel flusso audio, ma non sono state trovate corrispondenze per alcuna parola nella lingua di destinazione. In genere significa che la lingua di riconoscimento è una lingua diversa da quella parlata dall'utente. |
| `InitialSilenceTimeout` | La parte iniziale del flusso audio conteneva solo silenzio e il servizio ha raggiunto il timeout in attesa della parte parlata. |
| `BabbleTimeout` | La parte iniziale del flusso audio conteneva solo rumore e il servizio ha raggiunto il timeout in attesa della parte parlata. |
| `Error` | Il servizio di riconoscimento ha rilevato un errore interno e non è in grado di continuare. Provare di nuovo, se possibile. |

> [!NOTE]
> Se l'audio è costituito solo da contenuto volgare e il parametro di query `profanity` è impostato su `remove`, il servizio non restituisce un risultato di riconoscimento vocale.

Il `detailed` formato include gli `simple` stessi dati `NBest`del formato, insieme a , un elenco di interpretazioni alternative dello stesso risultato di riconoscimento. Questi risultati sono classificati dal più probabile al meno probabile. La prima voce corrisponde al risultato del riconoscimento principale.  Quando si usa il formato `detailed`, `DisplayText` viene fornito come `Display` per ogni risultato nell'elenco `NBest`.

Ogni oggetto nell'elenco `NBest` include:

| Parametro | Descrizione |
|-----------|-------------|
| `Confidence` | Il punteggio di attendibilità della voce da 0.0 (nessuna attendibilità) a 1.0 (attendibilità completa) |
| `Lexical` | Il formato lessicale del testo riconosciuto: le parole effettive riconosciute. |
| `ITN` | Il testo riconosciuto dopo la normalizzazione inversa (forma "canonica"), con numeri di telefono, numeri, abbreviazioni ("Dottor Rossi" in "Dr. Rossi") e altre trasformazioni applicate. |
| `MaskedITN` | La forma ITN con la maschera per le volgarità applicata, se richiesta. |
| `Display` | Il modulo di visualizzazione del testo riconosciuto, con l'aggiunta di segni di punteggiatura e maiuscole. Questo parametro è uguale a `DisplayText` fornito quando il formato è impostato su `simple`. |

## <a name="sample-responses"></a>Risposte di esempio

Una risposta `simple` tipica per il riconoscimento:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Una risposta `detailed` tipica per il riconoscimento:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
