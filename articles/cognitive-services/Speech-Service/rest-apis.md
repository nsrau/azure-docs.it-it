---
title: API REST del Servizio di riconoscimento vocale
description: Informazioni di riferimento sulle API REST per il Servizio di riconoscimento vocale.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 6758cd658daf75beeea93bf9c719508cd271c8be
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032428"
---
# <a name="speech-service-rest-apis"></a>API REST del Servizio di riconoscimento vocale

Le API REST del Servizio di riconoscimento vocale unificato dei servizi cognitivi Azure sono simili alle API offerte dall'[API Riconoscimento vocale Bing](https://docs.microsoft.com/azure/cognitive-services/Speech). Gli endpoint sono diversi da quelli usati dal servizio di API Riconoscimento vocale Bing. Sono disponibili endpoint a livello di area ed è necessario usare una chiave di sottoscrizione che corrisponda all'endpoint in uso.

## <a name="speech-to-text"></a>Riconoscimento vocale

Gli endpoint per il riconoscimento vocale all'API REST di testo vengono visualizzati nella tabella seguente. Usare l'endpoint corrispondente all'area della propria sottoscrizione.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> Se il modello acustico o linguistico oppure la pronuncia sono stati personalizzati, usare l'endpoint personalizzato.

Questa API supporta brevi enunciati. Le richieste possono contenere fino a 10 secondi di audio e durare un massimo di 14 secondi complessivamente. L'API REST restituisce risultati solo finali, non risultati intermedi o parziali. Il servizio riconoscimento vocale ha anche un'API di [trascrizione batch](batch-transcription.md) che può trascrivere audio più lunghi.

### <a name="query-parameters"></a>Parametri di query

I parametri seguenti potrebbero essere inclusi nella stringa di query della richiesta REST.

|Nome parametro|Obbligatorio/Facoltativo|Significato|
|-|-|-|
|`language`|Obbligatoria|L'identificatore della lingua da riconoscere. Vedere [Lingue supportate](supported-languages.md#speech-to-text).|
|`format`|Facoltativo<br>default: `simple`|Formato dei risultati, `simple` o `detailed`. I risultati semplici includono `RecognitionStatus`, `DisplayText`, `Offset` e la durata. I risultati dettagliati includono più candidati con valori di attendibilità e quattro diverse rappresentazioni.|
|`profanity`|Facoltativo<br>default: `masked`|Come gestire il linguaggio volgare nei risultati del riconoscimento. È possibile `masked` (sostituisce volgarità con asterisco), `removed` (rimuove tutte le volgarità) oppure `raw` (include le volgarità).

### <a name="request-headers"></a>Intestazioni della richiesta

I seguenti campi sono inviati nell'intestazione della richiesta HTTP.

|Intestazione|Significato|
|------|-------|
|`Ocp-Apim-Subscription-Key`|La chiave di sottoscrizione al Servizio di riconoscimento vocale dell'utente. È necessario fornire l'intestazione oppure `Authorization`.|
|`Authorization`|Un token di autorizzazione preceduto dalla parola `Bearer`. È necessario fornire l'intestazione oppure `Ocp-Apim-Subscription-Key`. Vedere [Autenticazione](#authentication).|
|`Content-type`|Descrive il formato e il codec dei dati audio. Attualmente, questo valore deve essere impostato su `audio/wav; codec=audio/pcm; samplerate=16000`.|
|`Transfer-Encoding`|facoltativo. Se specificato, deve essere `chunked` per consentire l'invio dei dati audio in più blocchi di piccole dimensioni, anziché di un singolo file.|
|`Expect`|Se si usa il trasferimento in blocchi, inviare `Expect: 100-continue`. Il Servizio di riconoscimento vocale legge la richiesta iniziale e attende ulteriori dati.|
|`Accept`|facoltativo. Se specificato, deve includere `application/json`, perché il Servizio di riconoscimento vocale fornisce i risultati in formato JSON. (Alcuni framework per le richieste Web offrono un valore predefinito incompatibile se non se ne specifica uno, pertanto è buona norma includere sempre `Accept`.)|

### <a name="audio-format"></a>Formato audio

L'audio viene inviato nel corpo della richiesta HTTP `PUT`. Deve essere in formato WAV 16 bit con singolo canale PCM (mono) a 16 kHz.

### <a name="chunked-transfer"></a>Trasferimento in blocchi

Il trasferimento in blocchi (`Transfer-Encoding: chunked`) aiuta a ridurre la latenza di riconoscimento perché consente al Servizio di riconoscimento vocale di iniziare l'elaborazione del file audio durante la trasmissione. L'API REST non fornisce risultati provvisori o parziali. Questa opzione è intesa unicamente a migliorare la velocità di risposta.

Nel codice seguente viene illustrato come inviare l'audio in blocchi. `request` è un oggetto HTTPWebRequest connesso all'endpoint REST appropriato. `audioFile` è il percorso di un file audio su disco.

```csharp
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

### <a name="example-request"></a>Richiesta di esempio

Quella che segue è una richiesta tipica.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>Stato HTTP

Lo stato HTTP della risposta indica esito positivo o condizioni di errore comuni.

Codice HTTP|Significato|Possibile motivo
-|-|-|
100|Continue|La richiesta iniziale è stata accettata. Procedere con l'invio del resto dei dati. (Usato con il trasferimento in blocchi.)
200|OK|La richiesta ha avuto esito positivo; il corpo della risposta è un oggetto JSON.
400|Richiesta non valida|Il codice lingua non è specificato o non è una lingua supportata. Il file audio non è valido.
401|Non autorizzata|La chiave di sottoscrizione o il token di autorizzazione non è valido nell'area specificata o l'endpoint non è valido.
403|Accesso negato|Manca la chiave di sottoscrizione o il token di autorizzazione.

### <a name="json-response"></a>Risposta JSON

I risultati sono restituiti in formato JSON. Il formato `simple` include solo i campi di primo livello seguenti.

|Nome campo|Content|
|-|-|
|`RecognitionStatus`|Lo stato, ad esempio `Success` per il riconoscimento con esito positivo. Vedere la tabella successiva.|
|`DisplayText`|Il testo riconosciuto dopo maiuscole/minuscole, punteggiatura, normalizzazione del testo inversa (conversione del testo parlato in forme più brevi, ad esempio 200 per "duecento" o "Dr. Rossi" per "Dottor Rossi") e la maschera per le espressioni volgari. Presente solo con esito positivo.|
|`Offset`|Il tempo (in unità di 100 nanosecondi) in corrispondenza del quale inizia l'input vocale riconosciuto nel flusso audio.|
|`Duration`|La durata (in unità di 100 nanosecondi) dell'input vocale riconosciuto nel flusso audio.|

Il campo `RecognitionStatus` può contenere i valori seguenti.

|Valore di stato|DESCRIZIONE
|-|-|
| `Success` | Il riconoscimento ha avuto esito positivo e il campo DisplayText è presente. |
| `NoMatch` | La parte parlata è stata rilevata nel flusso audio, ma non sono state trovate corrispondenze per alcuna parola nella lingua di destinazione. In genere significa che la lingua di riconoscimento è una lingua diversa da quella parlata dall'utente. |
| `InitialSilenceTimeout` | La parte iniziale del flusso audio conteneva solo silenzio e il servizio ha raggiunto il timeout in attesa della parte parlata. |
| `BabbleTimeout` | La parte iniziale del flusso audio conteneva solo rumore e il servizio ha raggiunto il timeout in attesa della parte parlata. |
| `Error` | Il servizio di riconoscimento ha rilevato un errore interno e non è in grado di continuare. Provare di nuovo, se possibile. |

> [!NOTE]
> Se l'audio è costituito solo da contenuto volgare e il parametro di query `profanity` è impostato su `remove`, il servizio non restituisce un risultato di riconoscimento vocale. 


Il formato `detailed` include gli stessi campi del formato `simple`, accompagnati da un campo `NBest`. Il campo `NBest` è un elenco di interpretazioni alternative dello stesso discorso, classificate dalla più probabile alla meno probabile. La prima voce corrisponde al risultato del riconoscimento principale. Ciascuna voce include i campi seguenti:

|Nome campo|Content|
|-|-|
|`Confidence`|Il punteggio di attendibilità della voce da 0.0 (nessuna attendibilità) a 1.0 (attendibilità completa)
|`Lexical`|Il formato lessicale del testo riconosciuto: le parole effettive riconosciute.
|`ITN`|Il testo riconosciuto dopo la normalizzazione inversa (forma "canonica"), con numeri di telefono, numeri, abbreviazioni ("Dottor Rossi" in "Dr. Rossi") e altre trasformazioni applicate.
|`MaskedITN`| La forma ITN con la maschera per le volgarità applicata, se richiesta.
|`Display`| Il modulo di visualizzazione del testo riconosciuto, con l'aggiunta di segni di punteggiatura e maiuscole. Uguale a `DisplayText` nel risultato principale.

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

## <a name="text-to-speech"></a>Sintesi vocale

Gli endpoint REST per l'API Sintesi vocale del Servizio di riconoscimento vocale sono riportati di seguito. Usare l'endpoint corrispondente all'area della propria sottoscrizione.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

> [!NOTE]
> Se è stato creato un carattere voce personalizzato, usare invece l'endpoint personalizzato associato.

Il Servizio di riconoscimento vocale supporta output audio a 24 KHz, oltre all'output a 16 Khz, supportato dal Riconoscimento vocale Bing. Nell'intestazione HTTP `X-Microsoft-OutputFormat` sono disponibili per l'uso quattro formati di output a 24 KHz, così come due voci a 24 KHz, `Jessa24kRUS` e `Guy24kRUS`.

Impostazioni locali | Linguaggio   | Sesso | Mapping nome del servizio
-------|------------|--------|------------
it-IT  | Inglese (Stati Uniti) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" 
it-IT  | Inglese (Stati Uniti) | Maschio   | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)"

È disponibile un elenco completo delle voci disponibili in [Lingue supportate](supported-languages.md#text-to-speech).

### <a name="request-headers"></a>Intestazioni della richiesta

I seguenti campi sono inviati nell'intestazione della richiesta HTTP.

|Intestazione|Significato|
|------|-------|
|`Authorization`|Un token di autorizzazione preceduto dalla parola `Bearer`. Richiesto. Vedere [Autenticazione](#authentication).|
|`Content-Type`|Il tipo di contenuto di input: `application/ssml+xml`.|
|`X-Microsoft-OutputFormat`|Formato audio di output. Vedere la tabella successiva.|
|`User-Agent`|Nome dell'applicazione. Obbligatorio, deve contenere meno di 255 caratteri.|

I formati di output audio disponibili (`X-Microsoft-OutputFormat`) incorporano una velocità in bit e una codifica.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `raw-8khz-8bit-mono-mulaw`
`riff-8khz-8bit-mono-mulaw`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> Se la voce selezionata e il formato di output hanno velocità in bit diverse, il campionamento audio viene ripetuto secondo necessità. Tuttavia, le voci a 24 kHz non supportano i formati di output `audio-16khz-16kbps-mono-siren` e `riff-16khz-16kbps-mono-siren`. 

### <a name="request-body"></a>Corpo della richiesta

Il testo da convertire in audio viene inviato come corpo di una richiesta HTTP `POST` in testo normale (ASCII o UTF-8) o in formato [Speech Syntesis Markup Language](speech-synthesis-markup.md) (SSML) (UTF-8). Le richieste in testo normale usano la voce e la lingua predefinita del servizio. Inviare SSML per usare un'altra voce.

### <a name="sample-request"></a>Richiesta di esempio

La richiesta HTTP seguente usa un corpo SSML per scegliere la voce. Il corpo non deve superare i 1.000 caratteri.

```xml
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

### <a name="http-response"></a>Risposta HTTP

Lo stato HTTP della risposta indica esito positivo o condizioni di errore comuni.

Codice HTTP|Significato|Possibile motivo
-|-|-|
200|OK|La richiesta ha avuto esito positivo; il corpo della risposta è un file audio.
400 |Bad Request |Un parametro obbligatorio è mancante, vuoto o Null. In alternativa, il valore passato a un parametro obbligatorio o facoltativo non è valido. Un problema comune è la lunghezza eccessiva dell'intestazione.
401|Non autorizzata |La richiesta non è autorizzata. Assicurarsi che la chiave di sottoscrizione o il token sia valido e nell'area corretta.
413|Entità della richiesta troppo grande|La lunghezza dell'input SSML è maggiore di 1024 caratteri.
|502|Gateway non valido    | Problema di rete o lato server. Può anche indicare intestazioni non valide.

Se lo stato HTTP è `200 OK`, il corpo della risposta contiene un file audio nel formato richiesto. Questo file può essere riprodotto, poiché è trasferito o salvato in un buffer o file per essere riprodotto successivamente o per altri usi.

## <a name="authentication"></a>Authentication

L'invio di una richiesta all'API REST del Servizio di riconoscimento vocale richiede un token di accesso. In generale, è più semplice inviare direttamente la chiave di sottoscrizione. Il Servizio di riconoscimento vocale ottiene quindi il token di accesso per l'utente. Per ridurre al minimo il tempo di risposta, potrebbe essere meglio usare un token di accesso.

È possibile ottenere un token immettendo la propria chiave di sottoscrizione nell'endpoint `issueToken` del Servizio di riconoscimento vocale di un'area, indicato nella tabella riportata di seguito. Usare l'endpoint corrispondente all'area della propria sottoscrizione.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Ogni token di accesso è valido per 10 minuti. È possibile ottenere un nuovo token in qualsiasi momento. Se si desidera, è possibile ottenere un token appena prima di ogni richiesta API REST di riconoscimento vocale. Per ridurre al minimo il traffico di rete e la latenza, è consigliabile usare lo stesso token per nove minuti.

Le sezioni seguenti illustrano come ottenere un token e come usarlo in una richiesta.

### <a name="get-a-token-http"></a>Ottenere un token: HTTP

Di seguito è riportato un esempio di richiesta HTTP per ottenere un token. Sostituire `YOUR_SUBSCRIPTION_KEY` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Se la sottoscrizione non si trova nell'area Stati Uniti occidentali, sostituire l'intestazione `Host` con il nome host della propria area.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Il corpo della risposta a questa richiesta è il token di accesso in formato JWT (Java Web Token).

### <a name="get-a-token-powershell"></a>Ottenere un token: PowerShell

Lo script Windows PowerShell riportato di seguito illustra come ottenere un token di accesso. Sostituire `YOUR_SUBSCRIPTION_KEY` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Se la sottoscrizione non si trova nell'area Stati Uniti occidentali, modificare di conseguenza il nome host dell'URI specificato.

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

### <a name="get-a-token-curl"></a>Ottenere un token: cURL

cURL è uno strumento da riga di comando disponibile in Linux (e nel sottosistema Windows per Linux). Il comando cURL riportato di seguito illustra come ottenere un token di accesso. Sostituire `YOUR_SUBSCRIPTION_KEY` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Se la sottoscrizione non si trova nell'area Stati Uniti occidentali, modificare di conseguenza il nome host dell'URI specificato.

> [!NOTE]
> Il comando viene visualizzato su più righe per migliorare la leggibilità, ma deve essere immesso in un'unica riga in un prompt della shell.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="get-a-token-c"></a>Ottenere un token: C#

La classe C# riportata di seguito illustra come ottenere un token di accesso. Passare la chiave di sottoscrizione del Servizio di riconoscimento vocale durante la creazione di un'istanza della classe. Se la sottoscrizione non si trova nell'area Stati Uniti occidentali, modificare in modo appropriato il nome host di `FetchTokenUri`.

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

### <a name="use-a-token"></a>Usare di un token

Per usare un token in una richiesta dell'API REST, specificarlo nell'intestazione `Authorization`, dopo la parola `Bearer`. Di seguito viene riportato un esempio di richiesta REST di Sintesi vocale contenente un token. Sostituire il token effettivo per `YOUR_ACCESS_TOKEN`. Usare il nome host corretto nell'intestazione `Host`.

```xml
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

### <a name="renew-authorization"></a>Rinnovare l'autorizzazione

Il token di autorizzazione scade dopo 10 minuti. Rinnovare l'autorizzazione ottenendo un nuovo token prima che scada. Ad esempio, è possibile ottenere un nuovo token dopo 9 minuti.

Il codice C# riportato di seguito è una soluzione sostitutiva della classe presentata in precedenza. La classe `Authentication` ottiene automaticamente un nuovo token di accesso ogni nove minuti usando un timer. Questo approccio assicura che quando il programma è in esecuzione sia sempre disponibile un token valido.

> [!NOTE]
> Invece di usare un timer, è possibile archiviare un timestamp di quando è stato ottenuto l'ultimo token. Quindi è possibile richiederne uno nuovo solo se è prossimi alla scadenza. Questo approccio consente di evitare di richiedere nuovi token se non è necessario e può risultare utile per i programmi che inviano richieste di riconoscimento vocale non frequenti.

Come in precedenza, verificare che il valore `FetchTokenUri` corrisponda all'area della propria sottoscrizione. Passare la chiave di sottoscrizione durante la creazione di un'istanza della classe.

```cs
/*
    * This class demonstrates how to maintain a valid access token.
    */
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

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)

