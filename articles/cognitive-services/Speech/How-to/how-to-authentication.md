---
title: Eseguire l'autenticazione a Riconoscimento vocale Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Richiedere l'autenticazione per usare l'API Riconoscimento vocale Bing
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 11d6256fb63452b849a80abab181876d14b3b6a6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515046"
---
# <a name="authenticate-to-the-speech-api"></a>Eseguire l'autenticazione a Speech API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Riconoscimento vocale Bing supporta l'autenticazione tramite:

- Una chiave di sottoscrizione.
- Un token di autorizzazione.

## <a name="use-a-subscription-key"></a>Usare una chiave di sottoscrizione

Per usare il Servizio di riconoscimento vocale, è necessario prima di tutto eseguire la sottoscrizione all'API di questo servizio, che fa parte di Servizi cognitivi (noto in precedenza come Project Oxford). È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita dalla pagina di [sottoscrizione di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/). Dopo aver selezionato Speech API, fare clic su **Ottieni la chiave API** per ottenere la chiave. Vengono restituite una chiave primaria e una chiave secondaria. Entrambe le chiavi sono legate alla stessa quota ed è quindi possibile usare indifferentemente una delle due.

Per l'uso a lungo termine o per una quota maggiore, iscriversi per ottenere un [account Azure](https://azure.microsoft.com/free/).

Per usare l'API REST del Servizio di riconoscimento vocale, è necessario passare la chiave di sottoscrizione nel campo `Ocp-Apim-Subscription-Key` nell'intestazione della richiesta.

NOME| Format| Descrizione
----|-------|------------
Ocp-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Di seguito è riportato un esempio di intestazione di una richiesta:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> Se nell'applicazione si usano [librerie client](../GetStarted/GetStartedClientLibraries.md), verificare di poter ottenere il token di autorizzazione con la chiave di sottoscrizione, come descritto nella sezione seguente. Le librerie client usano la chiave di sottoscrizione per ottenere un token di autorizzazione e quindi usano il token per l'autenticazione.

## <a name="use-an-authorization-token"></a>Usare un token di autorizzazione

In alternativa è possibile usare un token di autorizzazione per l'autenticazione come prova di autenticazione/autorizzazione. Per ottenere questo token, è necessario prima di tutto ottenere una chiave di sottoscrizione da Speech API, come descritto nella [sezione precedente](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Ottenere un token di autorizzazione

Dopo aver creato una chiave di sottoscrizione valida, inviare una richiesta POST al servizio token di Servizi cognitivi. Nella risposta si riceve il token di autorizzazione come token JSON Web (JWT).

> [!NOTE]
> Il token ha una scadenza di 10 minuti. Per rinnovarlo, vedere la sezione seguente.

L'URI del servizio token è disponibile qui:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

L'esempio di codice seguente mostra come ottenere un token di accesso. Sostituire `YOUR_SUBSCRIPTION_KEY` con la propria chiave di sottoscrizione:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

L'esempio usa curl in Linux con bash. Se non è disponibile nella piattaforma, può essere necessario installare curl. L'esempio funziona anche in Cygwin in Windows, Git Bash, zsh e altre shell.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

Di seguito è riportata una richiesta POST di esempio:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Se non si riesce a ottenere un token di autorizzazione dal servizio token, verificare che la chiave di sottoscrizione sia ancora valida. Se si usa una chiave per una versione di valutazione gratuita, andare alla pagina di [sottoscrizione di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/), fare clic su "Accedi" per eseguire l'accesso con l'account usato per applicare la chiave di sottoscrizione e verificare se è scaduta o ha superato la quota.

### <a name="use-an-authorization-token-in-a-request"></a>Usare un token di autorizzazione in una richiesta

Ogni volta che si chiama Speech API, è necessario passare il token di autorizzazione nell'intestazione `Authorization`. L'intestazione `Authorization` deve contenere un token di accesso JWT.

L'esempio seguente mostra come usare un token di autorizzazione quando si chiama l'API REST del Servizio di riconoscimento vocale.

> [!NOTE]
> Sostituire `YOUR_AUDIO_FILE` con il percorso del file audio preregistrato. Sostituire `YOUR_ACCESS_TOKEN` con il token di autorizzazione ottenuto nel passaggio precedente [Ottenere un token di autorizzazione](#get-an-authorization-token).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
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

---

### <a name="renew-an-authorization-token"></a>Rinnovare un token di autorizzazione

Il token di autorizzazione scade dopo un determinato periodo di tempo (attualmente 10 minuti). È necessario rinnovarlo prima che scada.

Il codice seguente è un esempio di implementazione in C# relativo al rinnovo del token di autorizzazione:

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
