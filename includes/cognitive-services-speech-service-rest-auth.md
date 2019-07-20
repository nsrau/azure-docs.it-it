---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 22a95be43f06e95a6067b179b3023ba94ee5795d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362549"
---
## <a name="authentication"></a>Authentication

Ogni richiesta richiede un'intestazione di autorizzazione. Questa tabella illustra quali intestazioni sono supportate per ogni servizio:

| Intestazione dell'autorizzazione supportata | Riconoscimento vocale | Sintesi vocale |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Yes | No |
| Authorization: Bearer | Sì | Sì |

Quando viene usata l'intestazione `Ocp-Apim-Subscription-Key`, è sufficiente fornire la chiave di sottoscrizione. Ad esempio:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Quando si usa l'intestazione `Authorization: Bearer`, è necessario effettuare una richiesta per l'endpoint `issueToken`. In questa richiesta viene scambiata la chiave di sottoscrizione per un token di accesso che è valido per 10 minuti. Nelle prossime sezioni verrà illustrato come ottenere un token e come usare un token.

### <a name="how-to-get-an-access-token"></a>Come ottenere un token di accesso

Per ottenere un token di accesso, è necessario effettuare una richiesta all'endpoint `issueToken` usando la `Ocp-Apim-Subscription-Key` e la propria chiave di sottoscrizione.

Queste aree ed endpoint sono supportati:

[!INCLUDE [](./cognitive-services-speech-service-endpoints-token-service.md)]

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

#### <a name="python-sample"></a>Esempio Python

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Come usare un token di accesso

Il token di accesso deve essere inviato al servizio come intestazione `Authorization: Bearer <TOKEN>`. Ogni token di accesso è valido per 10 minuti. È possibile ottenere un nuovo token in qualsiasi momento, tuttavia per ridurre al minimo il traffico di rete e la latenza, è consigliabile usare lo stesso token per nove minuti.

Ecco un esempio di richiesta HTTP all'API REST di sintesi vocale:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
