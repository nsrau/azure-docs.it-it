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
ms.openlocfilehash: 9991e0a1fc54e6aa4a99b8bfbd93abdfe974b01b
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283070"
---
# <a name="speech-service-rest-apis"></a>API REST del Servizio di riconoscimento vocale

Le API REST del Servizio di riconoscimento vocale unificato sono simili alle API offerte dall'[API Riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/Speech) (in precedenza noto come Servizio di riconoscimento vocale Bing). Gli endpoint sono diversi da quelli usati dal Servizio di riconoscimento vocale precedente.

## <a name="speech-to-text"></a>Riconoscimento vocale

Rispetto alla precedente API Riconoscimento vocale del Servizio di riconoscimento vocale, nell'API Riconoscimento vocale solo gli endpoint usati sono diversi. I nuovi endpoint sono indicati nella tabella riportata di seguito. Usare l'endpoint corrispondente all'area della propria sottoscrizione.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

L'API Riconoscimento vocale è altrimenti simile all'[API REST](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) per la precedente API Riconoscimento vocale.

L'API REST Riconoscimento vocale supporta solo espressioni brevi. Le richieste possono contenere fino a 10 secondi di audio e durare un massimo di 14 secondi complessivamente. L'API REST restituisce solo risultati finali, non risultati parziali o intermedi.

> [!NOTE]
> Se il modello acustico o linguistico oppure la pronuncia sono stati personalizzati, usare l'endpoint personalizzato.

## <a name="text-to-speech"></a>Sintesi vocale

La nuova API Sintesi vocale supporta un output audio di 24 KHz. L'intestazione `X-Microsoft-OutputFormat` ora può contenere i valori riportati di seguito.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

Il Servizio di riconoscimento vocale ora offre due voci da 24 KHz:

Impostazioni locali | Linguaggio   | Sesso | Mapping nome del servizio
-------|------------|--------|------------
it-IT  | Inglese (Stati Uniti) | Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" 
it-IT  | Inglese (Stati Uniti) | Maschio   | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)"

Gli endpoint REST per l'API Sintesi vocale del Servizio di riconoscimento vocale unificato sono riportati di seguito. Usare l'endpoint corrispondente all'area della propria sottoscrizione.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Tenere presente queste differenze quando si consulta la [documentazione dell'API REST](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) per la precedente API Riconoscimento vocale.

## <a name="authentication"></a>Authentication

Per inviare una richiesta all'API REST del Servizio di riconoscimento vocale è necessario un token di accesso. È possibile ottenere un token immettendo la propria chiave di sottoscrizione nell'endpoint `issueToken` del Servizio di riconoscimento vocale di un'area, indicato nella tabella riportata di seguito. Usare l'endpoint corrispondente all'area della propria sottoscrizione.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Ogni token di accesso è valido per 10 minuti. È possibile ottenere un nuovo token in qualsiasi momento. Se si vuole, anche prima di ogni richiesta all'API REST del Servizio di riconoscimento vocale. Per ridurre al minimo il traffico di rete e la latenza, è tuttavia consigliabile usare lo stesso token per nove minuti.

Le sezioni seguenti illustrano come ottenere un token e come usarlo in una richiesta.

### <a name="getting-a-token-http"></a>Ottenere un token: HTTP

Di seguito è riportata una richiesta HTTP di esempio per ottenere un token. Sostituire `YOUR_SUBSCRIPTION_KEY` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Se la sottoscrizione non si trova nell'area Stati Uniti occidentali, sostituire l'intestazione `Host` con il nome host della propria area.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Il corpo della risposta a questa richiesta è il token di accesso in formato JWT (Java Web Token).

### <a name="getting-a-token-powershell"></a>Ottenere un token: PowerShell

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

### <a name="getting-a-token-curl"></a>Ottenere un token: cURL

cURL è uno strumento da riga di comando disponibile in Linux (e nel sottosistema Windows per Linux). Il comando cURL riportato di seguito illustra come ottenere un token di accesso. Sostituire `YOUR_SUBSCRIPTION_KEY` con la chiave di sottoscrizione per il Servizio di riconoscimento vocale. Se la sottoscrizione non si trova nell'area Stati Uniti occidentali, modificare di conseguenza il nome host dell'URI specificato.

> [!NOTE]
> Il comando viene visualizzato su più righe per migliorare la leggibilità, ma deve essere immesso in un'unica riga a un prompt della shell.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Ottenere un token: C#

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

### <a name="using-a-token"></a>Uso di un token

Per usare un token in una richiesta dell'API REST, specificarlo nell'intestazione `Authorization`, dopo la parola `Bearer`. Di seguito viene riportata ad esempio una richiesta REST di Sintesi vocale contenente un token. Sostituire `YOUR_ACCESS_TOKEN` con il proprio token e usare il nome host corretto nell'intestazione `Host`.

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

### <a name="renewing-authorization"></a>Rinnovo dell'autorizzazione

Il token di autorizzazione scade dopo 10 minuti. Rinnovare l'autorizzazione ottenendo un nuovo token prima che scada, ad esempio dopo nove minuti. 

Il codice C# riportato di seguito è una soluzione sostitutiva della classe presentata in precedenza. La classe `Authentication` ottiene automaticamente un nuovo token di accesso ogni nove minuti usando un timer. Questo approccio assicura che quando il programma è in esecuzione sia sempre disponibile un token valido.

> [!NOTE]
> Anziché usare un timer, è possibile archiviare un timestamp dell'ora in cui il token corrente è stato ottenuto e richiederne quindi uno nuovo solo se il token corrente è prossimo alla scadenza. Questo approccio consente di evitare di richiedere nuovi token se non è necessario e può risultare utile per i programmi che inviano richieste di riconoscimento vocale non frequenti.

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

