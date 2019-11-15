---
title: Informazioni di riferimento sulle API di sintesi vocale (REST)-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API REST per sintesi vocale. In questo articolo vengono illustrate le opzioni di autorizzazione, le opzioni di query, come strutturare una richiesta e ricevere una risposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 137ab722df280d17fe5ccc5c07acfd323feb6531
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091216"
---
# <a name="speech-to-text-rest-api"></a>API REST di riconoscimento vocale

In alternativa all'SDK per la [sintesi](speech-sdk.md)vocale, servizi vocali consente di convertire il riconoscimento vocale usando un'API REST. Ogni endpoint accessibile è associato a un'area. L'applicazione richiede una chiave di sottoscrizione per l'endpoint che si intende usare.

Prima di usare l'API REST di sintesi vocale, comprendere quanto segue:

* Le richieste che usano l'API REST e trasmettono direttamente l'audio possono contenere solo fino a 60 secondi di audio.
* L'API REST per il riconoscimento vocale restituisce solo i risultati finali. I risultati parziali non sono disponibili.

Se l'invio di audio più lungo è un requisito per l'applicazione, prendere in considerazione l'uso dell' [SDK di riconoscimento vocale](speech-sdk.md) o di un'API REST basata su file, come la [trascrizione batch](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Aree ed endpoint

Queste aree sono supportate per la trascrizione vocale usando l'API REST. Assicurarsi di selezionare l'endpoint corrispondente all'area della propria sottoscrizione.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)] 

## <a name="query-parameters"></a>Parametri di query

Questi parametri possono essere inclusi nella stringa di query della richiesta REST.

| . | DESCRIZIONE | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `language` | Identifica la lingua parlata che viene riconosciuta. Vedere [Lingue supportate](language-support.md#speech-to-text). | obbligatori |
| `format` | Specifica il formato del risultato. I valori accettati sono `simple` e `detailed`. I risultati semplici includono `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. Le risposte dettagliate includono più risultati con valori di attendibilità e quattro diverse rappresentazioni. L'impostazione predefinita è `simple`. | Facoltativo |
| `profanity` | Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `masked`, che sostituisce la volgarità con asterischi, `removed`, che rimuove tutti i messaggi profani dal risultato, o `raw`, che include la volgarità nel risultato. L'impostazione predefinita è `masked`. | Facoltativo |

## <a name="request-headers"></a>Intestazioni della richiesta

Questa tabella elenca le intestazioni obbligatorie e facoltative per le richieste di riconoscimento vocale.

|Intestazione| DESCRIZIONE | Obbligatoria / Facoltativa |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Chiave di sottoscrizione di servizi vocali. | È necessaria questa intestazione o `Authorization`. |
| `Authorization` | Un token di autorizzazione preceduto dalla parola `Bearer`. Per altre informazioni, vedere [Autenticazione](#authentication). | È necessaria questa intestazione o `Ocp-Apim-Subscription-Key`. |
| `Content-type` | Descrive il formato e il codec dei dati audio forniti. I valori accettati sono `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | obbligatori |
| `Transfer-Encoding` | Specifica che vengono inviati i dati audio in blocchi, anziché un singolo file. Utilizzare questa intestazione solo se vi è stata la suddivisione in blocchi dei dati audio. | Facoltativo |
| `Expect` | Se si usa il trasferimento in blocchi, inviare `Expect: 100-continue`. I servizi di riconoscimento vocale riconoscono la richiesta iniziale e attendono dati aggiuntivi.| Obbligatorio in caso di invio di dati audio in blocchi. |
| `Accept` | Se specificato, deve essere `application/json`. I servizi di riconoscimento vocale forniscono i risultati in formato JSON. Alcuni framework di richiesta forniscono un valore predefinito incompatibile. È consigliabile includere sempre `Accept`. | Facoltativo, ma consigliato. |

## <a name="audio-formats"></a>Formati audio

L'audio viene inviato nel corpo della richiesta HTTP `POST`. Deve essere in uno dei formati elencati in questa tabella:

| Format | Codec | Bitrate | Frequenza di campionamento |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bit | 16 kHz, mono |
| OGG | OPUS | 16 bit | 16 kHz, mono |

>[!NOTE]
>I formati precedenti sono supportati tramite l'API REST e WebSocket nei servizi di riconoscimento vocale. [Speech SDK](speech-sdk.md) attualmente supporta solo il formato WAV con codec PCM.

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

| Stato codice HTTP | DESCRIZIONE | Possibile motivo |
|------------------|-------------|-----------------|
| 100 | Continue | La richiesta iniziale è stata accettata. Procedere con l'invio del resto dei dati. (Usato con il trasferimento in blocchi.) |
| 200 | OK | La richiesta ha avuto esito positivo; il corpo della risposta è un oggetto JSON. |
| 400 | Richiesta non valida | Il codice della lingua non è disponibile, non è un linguaggio supportato, un file audio non valido e così via. |
| 401 | Non autorizzata | La chiave di sottoscrizione o il token di autorizzazione non è valido nell'area specificata o l'endpoint non è valido. |
| 403 | Accesso negato | Manca la chiave di sottoscrizione o il token di autorizzazione. |

## <a name="chunked-transfer"></a>Trasferimento in blocchi

Il trasferimento in blocchi (`Transfer-Encoding: chunked`) può contribuire a ridurre la latenza di riconoscimento. Consente ai servizi di riconoscimento vocale di iniziare l'elaborazione del file audio durante la trasmissione. L'API REST non fornisce risultati provvisori o parziali.

Questo esempio di codice mostra come inviare audio in blocchi. Solo il primo blocco deve contenere l'intestazione del file audio. `request` è un oggetto HTTPWebRequest connesso all'endpoint REST appropriato. `audioFile` è il percorso di un file audio su disco.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
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

## <a name="response-parameters"></a>Parametri di risposta

I risultati vengono forniti in formato JSON. Il formato `simple` include i campi di primo livello seguenti.

| . | DESCRIZIONE  |
|-----------|--------------|
|`RecognitionStatus`|Lo stato, ad esempio `Success` per il riconoscimento con esito positivo. Vedere la tabella successiva.|
|`DisplayText`|Testo riconosciuto dopo l'utilizzo di maiuscole e minuscole, la punteggiatura, la normalizzazione del testo inverso (conversione del testo vocale in forme più brevi, ad esempio 200 per "200" o "Dr. Smith" per "Doctor Smith") e la maschera volgare. Presente solo con esito positivo.|
|`Offset`|Il tempo (in unità di 100 nanosecondi) in corrispondenza del quale inizia l'input vocale riconosciuto nel flusso audio.|
|`Duration`|La durata (in unità di 100 nanosecondi) dell'input vocale riconosciuto nel flusso audio.|

Il campo `RecognitionStatus` può contenere questi valori:

| Stato | DESCRIZIONE |
|--------|-------------|
| `Success` | Il riconoscimento ha avuto esito positivo e il campo `DisplayText` è presente. |
| `NoMatch` | La parte parlata è stata rilevata nel flusso audio, ma non sono state trovate corrispondenze per alcuna parola nella lingua di destinazione. In genere significa che la lingua di riconoscimento è una lingua diversa da quella parlata dall'utente. |
| `InitialSilenceTimeout` | La parte iniziale del flusso audio conteneva solo silenzio e il servizio ha raggiunto il timeout in attesa della parte parlata. |
| `BabbleTimeout` | La parte iniziale del flusso audio conteneva solo rumore e il servizio ha raggiunto il timeout in attesa della parte parlata. |
| `Error` | Il servizio di riconoscimento ha rilevato un errore interno e non è in grado di continuare. Provare di nuovo, se possibile. |

> [!NOTE]
> Se l'audio è costituito solo da contenuto volgare e il parametro di query `profanity` è impostato su `remove`, il servizio non restituisce un risultato di riconoscimento vocale.

Il formato `detailed` include gli stessi dati del formato `simple`, insieme a `NBest`, un elenco di interpretazioni alternative dello stesso risultato del riconoscimento. Questi risultati sono classificati in base alla probabilità meno probabile. La prima voce corrisponde al risultato del riconoscimento principale.  Quando si usa il formato `detailed`, `DisplayText` viene fornito come `Display` per ogni risultato nell'elenco `NBest`.

Ogni oggetto nell'elenco `NBest` include:

| . | DESCRIZIONE |
|-----------|-------------|
| `Confidence` | Il punteggio di attendibilità della voce da 0.0 (nessuna attendibilità) a 1.0 (attendibilità completa) |
| `Lexical` | Il formato lessicale del testo riconosciuto: le parole effettive riconosciute. |
| `ITN` | Il testo riconosciuto dopo la normalizzazione inversa (forma "canonica"), con numeri di telefono, numeri, abbreviazioni ("Dottor Rossi" in "Dr. Rossi") e altre trasformazioni applicate. |
| `MaskedITN` | La forma ITN con la maschera per le volgarità applicata, se richiesta. |
| `Display` | Il modulo di visualizzazione del testo riconosciuto, con l'aggiunta di segni di punteggiatura e maiuscole. Questo parametro è uguale a `DisplayText` fornito quando il formato è impostato su `simple`. |

## <a name="sample-responses"></a>Risposte di esempio

Risposta tipica per il riconoscimento `simple`:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Risposta tipica per il riconoscimento `detailed`:

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
