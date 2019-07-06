---
title: Riferimento API per il riconoscimento vocale (REST) - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API REST per il riconoscimento vocale. In questo articolo vengono illustrate le opzioni di autorizzazione, le opzioni di query, come strutturare una richiesta e ricevere una risposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9d967fa4d5ba54e4470dadc5e797067454e1769a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606345"
---
# <a name="speech-to-text-rest-api"></a>API REST di riconoscimento vocale

Come alternativa per il [Speech SDK](speech-sdk.md), servizi voce consentono di convertire per il riconoscimento vocale usando un'API REST. Ogni endpoint accessibile è associato a un'area. L'applicazione richiede una chiave di sottoscrizione per l'endpoint che si intende usare.

Prima di usare l'API REST per il riconoscimento vocale, comprensione:
* Le richieste che usano l'API REST possono contenere solo 10 secondi di audio registrato.
* L'API REST per il riconoscimento vocale restituisce solo i risultati finali. I risultati parziali non sono disponibili.

Se l'invio di un audio più lungo è un requisito per l'applicazione, è consigliabile usare [Speech SDK](speech-sdk.md) oppure [ trascrizione batch](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Aree ed endpoint

Queste aree sono supportate per la trascrizione vocale usando l'API REST. Assicurarsi di selezionare l'endpoint corrispondente all'area della propria sottoscrizione.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>Parametri di query

Questi parametri possono essere inclusi nella stringa di query della richiesta REST.

| Parametro | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `language` | Identifica la lingua parlata che viene riconosciuta. Vedere [Lingue supportate](language-support.md#speech-to-text). | Obbligatorio |
| `format` | Specifica il formato del risultato. I valori accettati sono `simple` e `detailed`. I risultati semplici includono `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. Le risposte dettagliate includono più risultati con valori di attendibilità e quattro diverse rappresentazioni. L'impostazione predefinita è `simple`. | Facoltativo |
| `profanity` | Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `masked` che sostituisce il contenuto volgare con gli asterischi, `removed` che rimuove tutto il contenuto volgare dal risultato, o `raw` che include il contenuto volgare nei risultati. L'impostazione predefinita è `masked`. | Facoltativo |

## <a name="request-headers"></a>Intestazioni della richiesta

Questa tabella elenca le intestazioni obbligatorie e facoltative per le richieste di riconoscimento vocale.

|Intestazione| Descrizione | Obbligatoria / Facoltativa |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | La chiave di sottoscrizione di servizi di riconoscimento vocale. | È necessaria questa intestazione o `Authorization`. |
| `Authorization` | Un token di autorizzazione preceduto dalla parola `Bearer`. Per altre informazioni, vedere [Autenticazione](#authentication). | È necessaria questa intestazione o `Ocp-Apim-Subscription-Key`. |
| `Content-type` | Descrive il formato e il codec dei dati audio forniti. I valori accettati sono `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Obbligatorio |
| `Transfer-Encoding` | Specifica che vengono inviati i dati audio in blocchi, anziché un singolo file. Utilizzare questa intestazione solo se vi è stata la suddivisione in blocchi dei dati audio. | Facoltativo |
| `Expect` | Se si usa il trasferimento in blocchi, inviare `Expect: 100-continue`. I servizi di riconoscimento vocale riconosca la richiesta iniziale e attende ulteriori dati.| Obbligatorio in caso di invio di dati audio in blocchi. |
| `Accept` | Se specificato, deve essere `application/json`. I servizi di riconoscimento vocale forniscono i risultati in formato JSON. Alcuni framework per le richieste Web offrono un valore predefinito incompatibile se non se ne specifica uno, pertanto è buona norma includere sempre `Accept`. | Facoltativo, ma consigliato. |

## <a name="audio-formats"></a>Formati audio

L'audio viene inviato nel corpo della richiesta HTTP `POST`. Deve essere in uno dei formati elencati in questa tabella:

| Format | Codec | Bitrate | Frequenza di campionamento |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bit | 16 kHz, mono |
| OGG | OPUS | 16 bit | 16 kHz, mono |

>[!NOTE]
>I formati indicati sopra sono supportati tramite l'API REST e WebSocket nei servizi di riconoscimento vocale. [Speech SDK](speech-sdk.md) attualmente supporta solo il formato WAV con codec PCM.

## <a name="sample-request"></a>Richiesta di esempio

Si tratta di una richiesta HTTP tipica. L'esempio seguente include il nome host e le intestazioni richieste. È importante notare che il servizio prevede anche i dati audio, che non sono inclusi in questo esempio. Come accennato in precedenza, la suddivisione in blocchi è consigliabile, tuttavia, non è necessaria.

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
| 100 | Continue | La richiesta iniziale è stata accettata. Procedere con l'invio del resto dei dati. (Usato con il trasferimento in blocchi.) |
| 200 | OK | La richiesta ha avuto esito positivo; il corpo della risposta è un oggetto JSON. |
| 400 | Richiesta non valida | Il codice lingua non è specificato o non è una lingua supportata. Il file audio non è valido. |
| 401 | Non autorizzata | La chiave di sottoscrizione o il token di autorizzazione non è valido nell'area specificata o l'endpoint non è valido. |
| 403 | Accesso negato | Manca la chiave di sottoscrizione o il token di autorizzazione. |

## <a name="chunked-transfer"></a>Trasferimento in blocchi

Trasferimento in blocco (`Transfer-Encoding: chunked`) consente di ridurre la latenza di riconoscimento perché consente di servizi di riconoscimento vocale iniziare l'elaborazione del file audio durante la trasmissione. L'API REST non fornisce risultati provvisori o parziali. Questa opzione è intesa unicamente a migliorare la velocità di risposta.

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

| Parametro | Descrizione  |
|-----------|--------------|
|`RecognitionStatus`|Lo stato, ad esempio `Success` per il riconoscimento con esito positivo. Vedere la tabella successiva.|
|`DisplayText`|Il testo riconosciuto dopo maiuscole/minuscole, punteggiatura, normalizzazione del testo inversa (conversione del testo parlato in forme più brevi, ad esempio 200 per "duecento" o "Dr. Rossi" per "Dottor Rossi") e la maschera per le espressioni volgari. Presente solo con esito positivo.|
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

Il `detailed` formato include gli stessi dati come i `simple` formattare, insieme con `NBest`, un elenco di alternative interpretazioni sullo stesso risultato di riconoscimento. Questi risultati vengono classificati dal probabilmente al meno probabile. La prima voce corrisponde al risultato del riconoscimento principale.  Quando si usa il formato `detailed`, `DisplayText` viene fornito come `Display` per ogni risultato nell'elenco `NBest`.

Ogni oggetto nell'elenco `NBest` include:

| Parametro | Descrizione |
|-----------|-------------|
| `Confidence` | Il punteggio di attendibilità della voce da 0.0 (nessuna attendibilità) a 1.0 (attendibilità completa) |
| `Lexical` | Il formato lessicale del testo riconosciuto: le parole effettive riconosciute. |
| `ITN` | Il testo riconosciuto dopo la normalizzazione inversa (forma "canonica"), con numeri di telefono, numeri, abbreviazioni ("Dottor Rossi" in "Dr. Rossi") e altre trasformazioni applicate. |
| `MaskedITN` | La forma ITN con la maschera per le volgarità applicata, se richiesta. |
| `Display` | Il modulo di visualizzazione del testo riconosciuto, con l'aggiunta di segni di punteggiatura e maiuscole. Questo parametro è uguale a `DisplayText` fornito quando il formato è impostato su `simple`. |

## <a name="sample-responses"></a>Risposte di esempio

Di seguito è riportata una tipica risposta per il riconoscimento `simple`.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Di seguito è riportata una tipica risposta per il riconoscimento `detailed`.

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
