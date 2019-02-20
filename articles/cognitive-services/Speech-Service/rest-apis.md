---
title: API REST dei servizi Voce - Servizi Voce
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare le API REST per il riconoscimento vocale e la sintesi vocale. In questo articolo vengono illustrate le opzioni di autorizzazione, le opzioni di query, come strutturare una richiesta e ricevere una risposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 0ce33f20d44ac284655569ff66825533650b9d9c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998944"
---
# <a name="speech-service-rest-apis"></a>API REST del servizio Voce

Come alternativa per [Speech SDK](speech-sdk.md), il servizio di riconoscimento vocale consente di convertire il riconoscimento vocale e la sintesi vocale con un set di API REST. Ogni endpoint accessibile è associato a un'area. L'applicazione richiede una chiave di sottoscrizione per l'endpoint che si intende usare.

Prima di usare le API REST, comprendere:
* Le richieste per il riconoscimento vocale usando l'API REST possono contenere solo 10 secondi di audio registrato.
* L'API REST per il riconoscimento vocale restituisce solo i risultati finali. I risultati parziali non sono disponibili.
* L'API REST di sintesi vocale richiede un'intestazione dell'autorizzazione. Ciò significa che è necessario completare uno scambio di token per accedere al servizio. Per altre informazioni, vedere [Autenticazione](#authentication).

## <a name="authentication"></a>Authentication

Ogni richiesta all'API REST del riconoscimento vocale o della sintesi vocale richiede un'intestazione dell'autorizzazione. Questa tabella illustra quali intestazioni sono supportate per ogni servizio:

| Intestazione dell'autorizzazione supportata | Riconoscimento vocale | Sintesi vocale |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Sì | No  |
| Authorization: Bearer | Sì | Sì |

Quando viene usata l'intestazione `Ocp-Apim-Subscription-Key`, è sufficiente fornire la chiave di sottoscrizione. Ad esempio: 

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Quando si usa l'intestazione `Authorization: Bearer`, è necessario effettuare una richiesta per l'endpoint `issueToken`. In questa richiesta viene scambiata la chiave di sottoscrizione per un token di accesso che è valido per 10 minuti. Nelle sezioni successive verrà illustrato come ottenere un token, usare un token e aggiornare un token.

### <a name="how-to-get-an-access-token"></a>Come ottenere un token di accesso

Per ottenere un token di accesso, è necessario effettuare una richiesta all'endpoint `issueToken` usando la `Ocp-Apim-Subscription-Key` e la propria chiave di sottoscrizione.

Queste aree ed endpoint sono supportati:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Usare questi esempi per creare la richiesta di token di accesso.

#### <a name="http-sample"></a>Esempio di HTTP

Questo esempio è una semplice richiesta HTTP per ottenere un token. Sostituire `YOUR_SUBSCRIPTION_KEY` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Se la sottoscrizione non si trova nell'area Stati Uniti occidentali, sostituire l'intestazione `Host` con il nome host della propria area.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Il corpo della risposta contiene il token di accesso in formato JWT (JSON Web Token).

#### <a name="powershell-sample"></a>Esempio PowerShell

Questo esempio è un semplice script di PowerShell per ottenere un token di accesso. Sostituire `YOUR_SUBSCRIPTION_KEY` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Assicurarsi di usare l'endpoint corretto per l'area che corrisponde alla sottoscrizione. Questo esempio è attualmente impostato sugli Stati Uniti occidentali.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>esempio di cURL

cURL è uno strumento da riga di comando disponibile in Linux (e nel sottosistema Windows per Linux). Il comando cURL illustra come ottenere un token di accesso. Sostituire `YOUR_SUBSCRIPTION_KEY` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Assicurarsi di usare l'endpoint corretto per l'area che corrisponde alla sottoscrizione. Questo esempio è attualmente impostato sugli Stati Uniti occidentali.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Esempio C#

La classe C# illustra come ottenere un token di accesso. Passare la chiave di sottoscrizione del Servizio di riconoscimento vocale durante la creazione di un'istanza della classe. Se la sottoscrizione non è nell'area Stati Uniti occidentali, modificare il valore di `FetchTokenUri` in modo che corrisponda all'area della sottoscrizione.

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>Come usare un token di accesso

Il token di accesso deve essere inviato al servizio come intestazione `Authorization: Bearer <TOKEN>`. Ogni token di accesso è valido per 10 minuti. È possibile ottenere un nuovo token in qualsiasi momento, tuttavia per ridurre al minimo il traffico di rete e la latenza, è consigliabile usare lo stesso token per nove minuti.

Ecco un esempio di richiesta HTTP all'API REST di sintesi vocale:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>Come rinnovare un token di accesso usando C#

Il codice C# è una soluzione sostitutiva della classe presentata in precedenza. La classe `Authentication` ottiene automaticamente un nuovo token di accesso ogni nove minuti usando un timer. Questo approccio assicura che quando il programma è in esecuzione sia sempre disponibile un token valido.

> [!NOTE]
> Invece di usare un timer, è possibile archiviare un timestamp di quando è stato ottenuto l'ultimo token. Quindi è possibile richiederne uno nuovo solo se è prossimi alla scadenza. Questo approccio consente di evitare di richiedere nuovi token se non è necessario e può risultare utile per i programmi che inviano richieste di riconoscimento vocale non frequenti.

Come in precedenza, verificare che il valore `FetchTokenUri` corrisponda all'area della propria sottoscrizione. Passare la chiave di sottoscrizione durante la creazione di un'istanza della classe.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>API di riconoscimento vocale

L'API REST di riconoscimento vocale supporta solo espressioni brevi. Le richieste possono contenere fino a 10 secondi di audio con una durata totale di 14 secondi. L'API REST restituisce solo i risultati finali, non i risultati parziali o intermedi.

Se l'invio di un audio più lungo è un requisito per l'applicazione, è consigliabile usare [Speech SDK](speech-sdk.md) oppure [ trascrizione batch](batch-transcription.md).

### <a name="regions-and-endpoints"></a>Aree ed endpoint

Queste aree sono supportate per la trascrizione vocale usando l'API REST. Assicurarsi di selezionare l'endpoint corrispondente all'area della propria sottoscrizione.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>Parametri di query

Questi parametri possono essere inclusi nella stringa di query della richiesta REST.

| Parametro | DESCRIZIONE | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `language` | Identifica la lingua parlata che viene riconosciuta. Vedere [Lingue supportate](language-support.md#speech-to-text). | Obbligatoria |
| `format` | Specifica il formato del risultato. I valori accettati sono `simple` e `detailed`. I risultati semplici includono `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. Le risposte dettagliate includono più risultati con valori di attendibilità e quattro diverse rappresentazioni. L'impostazione predefinita è `simple`. | Facoltativo |
| `profanity` | Specifica come gestire il linguaggio volgare nei risultati del riconoscimento. I valori accettati sono `masked` che sostituisce il contenuto volgare con gli asterischi, `removed` che rimuove tutto il contenuto volgare dal risultato, o `raw` che include il contenuto volgare nei risultati. L'impostazione predefinita è `masked`. | Facoltativo |

### <a name="request-headers"></a>Intestazioni della richiesta

Questa tabella elenca le intestazioni obbligatorie e facoltative per le richieste di riconoscimento vocale.

|Intestazione| DESCRIZIONE | Obbligatoria / Facoltativa |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | La chiave di sottoscrizione al Servizio di riconoscimento vocale dell'utente. | È necessaria questa intestazione o `Authorization`. |
| `Authorization` | Un token di autorizzazione preceduto dalla parola `Bearer`. Per altre informazioni, vedere [Autenticazione](#authentication). | È necessaria questa intestazione o `Ocp-Apim-Subscription-Key`. |
| `Content-type` | Descrive il formato e il codec dei dati audio forniti. I valori accettati sono `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Obbligatoria |
| `Transfer-Encoding` | Specifica che vengono inviati i dati audio in blocchi, anziché un singolo file. Utilizzare questa intestazione solo se vi è stata la suddivisione in blocchi dei dati audio. | Facoltativo |
| `Expect` | Se si usa il trasferimento in blocchi, inviare `Expect: 100-continue`. Il Servizio di riconoscimento vocale legge la richiesta iniziale e attende ulteriori dati.| Obbligatorio in caso di invio di dati audio in blocchi. |
| `Accept` | Se specificato, deve essere `application/json`. Il servizio di riconoscimento vocale fornisce i risultati in formato JSON. Alcuni framework per le richieste Web offrono un valore predefinito incompatibile se non se ne specifica uno, pertanto è buona norma includere sempre `Accept`. | Facoltativo, ma consigliato. |

### <a name="audio-formats"></a>Formati audio

L'audio viene inviato nel corpo della richiesta HTTP `POST`. Deve essere in uno dei formati elencati in questa tabella:

| Format | Codec | Bitrate | Frequenza di campionamento |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bit | 16 kHz, mono |
| OGG | OPUS | 16 bit | 16 kHz, mono |

>[!NOTE]
>I formati indicati sono supportati tramite l'API REST e WebSocket nel servizio Voce. [Speech SDK](speech-sdk.md) attualmente supporta solo il formato WAV con codec PCM.

### <a name="sample-request"></a>Richiesta di esempio

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

### <a name="http-status-codes"></a>Codici di stato HTTP

Il codice di stato HTTP di ogni risposta indica esito positivo o errori comuni.

| Stato codice HTTP | DESCRIZIONE | Possibile motivo |
|------------------|-------------|-----------------|
| 100 | Continue | La richiesta iniziale è stata accettata. Procedere con l'invio del resto dei dati. (Usato con il trasferimento in blocchi.) |
| 200 | OK | La richiesta ha avuto esito positivo; il corpo della risposta è un oggetto JSON. |
| 400 | Richiesta non valida | Il codice lingua non è specificato o non è una lingua supportata. Il file audio non è valido. |
| 401 | Non autorizzata | La chiave di sottoscrizione o il token di autorizzazione non è valido nell'area specificata o l'endpoint non è valido. |
| 403 | Accesso negato | Manca la chiave di sottoscrizione o il token di autorizzazione. |

### <a name="chunked-transfer"></a>Trasferimento in blocchi

Il trasferimento in blocchi (`Transfer-Encoding: chunked`) aiuta a ridurre la latenza di riconoscimento perché consente al Servizio di riconoscimento vocale di iniziare l'elaborazione del file audio durante la trasmissione. L'API REST non fornisce risultati provvisori o parziali. Questa opzione è intesa unicamente a migliorare la velocità di risposta.

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

### <a name="response-parameters"></a>Parametri di risposta

I risultati vengono forniti in formato JSON. Il formato `simple` include i campi di primo livello seguenti.

| Parametro | DESCRIZIONE  |
|-----------|--------------|
|`RecognitionStatus`|Lo stato, ad esempio `Success` per il riconoscimento con esito positivo. Vedere la tabella successiva.|
|`DisplayText`|Il testo riconosciuto dopo maiuscole/minuscole, punteggiatura, normalizzazione del testo inversa (conversione del testo parlato in forme più brevi, ad esempio 200 per "duecento" o "Dr. Rossi" per "Dottor Rossi") e la maschera per le espressioni volgari. Presente solo con esito positivo.|
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

Il formato `detailed` include gli stessi dati del formato `simple`, insieme a `NBest`, un elenco di interpretazioni alternative sullo stesso risultato di riconoscimento vocale. Questi risultati vengono classificati dal più probabile al meno probabile La prima voce è la stessa del risultato principale del riconoscimento.  Quando si usa il formato `detailed`, `DisplayText` viene fornito come `Display` per ogni risultato nell'elenco `NBest`.

Ogni oggetto nell'elenco `NBest` include:

| Parametro | DESCRIZIONE |
|-----------|-------------|
| `Confidence` | Il punteggio di attendibilità della voce da 0.0 (nessuna attendibilità) a 1.0 (attendibilità completa) |
| `Lexical` | Il formato lessicale del testo riconosciuto: le parole effettive riconosciute. |
| `ITN` | Il testo riconosciuto dopo la normalizzazione inversa (forma "canonica"), con numeri di telefono, numeri, abbreviazioni ("Dottor Rossi" in "Dr. Rossi") e altre trasformazioni applicate. |
| `MaskedITN` | La forma ITN con la maschera per le volgarità applicata, se richiesta. |
| `Display` | Il modulo di visualizzazione del testo riconosciuto, con l'aggiunta di segni di punteggiatura e maiuscole. Questo parametro è uguale a `DisplayText` fornito quando il formato è impostato su `simple`. |

### <a name="sample-responses"></a>Risposte di esempio

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

## <a name="text-to-speech-api"></a>API Sintesi vocale

L'API REST Sintesi vocale supporta voci neurali e standard, ognuna delle quali supporta a sua volta una lingua e una lingua regionale specifiche, identificate dalle impostazioni locali.

* Per un elenco completo delle voci, vedere [Supporto per le lingue](language-support.md#text-to-speech).
* Per informazioni sulla disponibilità a livello di area, vedere [Aree](regions.md#text-to-speech).

> [!IMPORTANT]
> I costi variano per voci standard, personalizzate e neurali. Per altre informazioni, vedere [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="request-headers"></a>Intestazioni della richiesta

Questa tabella elenca le intestazioni obbligatorie e facoltative per le richieste di riconoscimento vocale.

| Intestazione | DESCRIZIONE | Obbligatoria / Facoltativa |
|--------|-------------|---------------------|
| `Authorization` | Un token di autorizzazione preceduto dalla parola `Bearer`. Per ulteriori informazioni, vedere [Autenticazione](#authentication). | Obbligatoria |
| `Content-Type` | Specifica il tipo di contenuto per il testo specificato. Valore accettato: `application/ssml+xml`. | Obbligatoria |
| `X-Microsoft-OutputFormat` | Specifica il formato di output audio. Per un elenco completo dei valori accettati, vedere [output audio](#audio-outputs). | Obbligatoria |
| `User-Agent` | Il nome applicazione. Deve essere inferiore a 255 caratteri. | Obbligatoria |

### <a name="audio-outputs"></a>Output audio

Questo è un elenco dei formati audio supportati che vengono inviati in ogni richiesta come intestazione `X-Microsoft-OutputFormat`. Ognuno incorpora una velocità in bit e il tipo di codifica. Il servizio Voce supporta output audio a 24, 16 e 8 kHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Se la voce selezionata e il formato di output hanno velocità in bit diverse, il campionamento audio viene ripetuto secondo necessità. Tuttavia, le voci a 24 kHz non supportano i formati di output `audio-16khz-16kbps-mono-siren` e `riff-16khz-16kbps-mono-siren`.

### <a name="request-body"></a>Corpo della richiesta

Il corpo di ogni richiesta `POST` viene inviato come [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). SSML consente di scegliere la voce e la lingua della sintesi vocale restituita dal servizio di sintesi vocale. Per un elenco completo delle voci supportate, consultare [Supporto per le lingue](language-support.md#text-to-speech).

> [!NOTE]
> Se si usa una voce personalizzata, il corpo della richiesta può essere inviato come testo normale (ASCII o UTF-8).

### <a name="sample-request"></a>Richiesta di esempio

Questa richiesta HTTP utilizza SSML per specificare la lingua e la voce. Il corpo non può superare i 1000 caratteri.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>Codici di stato HTTP

Il codice di stato HTTP di ogni risposta indica esito positivo o errori comuni.

| Stato codice HTTP | DESCRIZIONE | Possibile motivo |
|------------------|-------------|-----------------|
| 200 | OK | La richiesta ha avuto esito positivo; il corpo della risposta è un file audio. |
| 400 | Bad Request | Un parametro obbligatorio è mancante, vuoto o Null. In alternativa, il valore passato a un parametro obbligatorio o facoltativo non è valido. Un problema comune è la lunghezza eccessiva dell'intestazione. |
| 401 | Non autorizzata | La richiesta non è autorizzata. Assicurarsi che la chiave di sottoscrizione o il token sia valido e nell'area corretta. |
| 413 | Entità della richiesta troppo grande | La lunghezza dell'input SSML è maggiore di 1024 caratteri. |
| 429 | Troppe richieste | È stata superata la quota o la frequenza di richieste consentite per la sottoscrizione. |
| 502 | Gateway non valido | Problema di rete o lato server. Può anche indicare intestazioni non valide. |

Se lo stato HTTP è `200 OK`, il corpo della risposta contiene un file audio nel formato richiesto. Questo file può essere riprodotto, poiché è trasferito, salvato in un buffer o salvato in un file.

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
