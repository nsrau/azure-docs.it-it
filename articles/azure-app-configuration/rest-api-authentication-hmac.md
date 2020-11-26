---
title: API REST di configurazione di app Azure-autenticazione HMAC
description: Usare HMAC per eseguire l'autenticazione a app Azure configurazione usando l'API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bd1667f6c17922b6c0b0bfba7a7329a3fc96b62e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182632"
---
# <a name="hmac-authentication---rest-api-reference"></a>Autenticazione HMAC: informazioni di riferimento sull'API REST

È possibile autenticare le richieste HTTP usando lo schema di autenticazione HMAC-SHA256. Per HMAC si intende il codice di autenticazione messaggi basato su hash. Queste richieste devono essere trasmesse su TLS.

## <a name="prerequisites"></a>Prerequisiti

- **Credenziali** - \<Access Key ID\>
- Valore della chiave di accesso con decodifica **Secret** -Base64. ``base64_decode(<Access Key Value>)``

I valori per Credential (chiamato anche `id` ) e Secret (chiamato anche `value` ) devono essere ottenuti dall'istanza di app Azure configurazione. A tale scopo, è possibile usare l'interfaccia della riga di comando [portale di Azure](https://portal.azure.com) o [Azure](/cli/azure/?preserve-view=true&view=azure-cli-latest).

Fornire ogni richiesta con tutte le intestazioni HTTP necessarie per l'autenticazione. Il valore minimo richiesto è:

|  Intestazione della richiesta | Descrizione  |
| --------------- | ------------ |
| **Host** | Host Internet e numero di porta. Per ulteriori informazioni, vedere la sezione  [3.2.2](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.2.2). |
| **Data** | Data e ora in cui è stata originata la richiesta. Non può superare i 15 minuti dall'ora UTC (ora di Greenwich) corrente. Il valore è una data HTTP, come descritto nella sezione [3.3.1](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3.1).
| **x-ms-date** | Come ```Date``` sopra. È invece possibile utilizzarlo quando l'agente non è in grado di accedere direttamente all' ```Date``` intestazione della richiesta o se viene modificato da un proxy. Se ```x-ms-date``` e ```Date``` sono entrambi specificati, ```x-ms-date``` avrà la precedenza. |
| **x-ms-content-SHA256** | hash SHA256 con codifica Base64 del corpo della richiesta. Deve essere fornito anche se non è presente alcun corpo. ```base64_encode(SHA256(body))```|
| **Autorizzazione** | Informazioni di autenticazione richieste dallo schema HMAC-SHA256. Il formato e i dettagli sono descritti più avanti in questo articolo. |

**Esempio:**

```http
Host: {myconfig}.azconfig.io
Date: Fri, 11 May 2018 18:48:36 GMT
x-ms-content-sha256: {SHA256 hash of the request body}
Authorization: HMAC-SHA256 Credential={Access Key ID}&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={Signature}
```

## <a name="authorization-header"></a>Authorization header (Intestazione dell'autorizzazione)

### <a name="syntax"></a>Sintassi

``Authorization``: **HMAC-SHA256**```Credential```=\<value\>&```SignedHeaders```=\<value\>&```Signature```=\<value\>

|  Argomento | Descrizione  |
| ------ | ------ |
| **HMAC-SHA256** | Schema di autorizzazione. _necessaria_ |
| **Credenziali** | ID della chiave di accesso utilizzata per calcolare la firma. _necessaria_ |
| **SignedHeaders** | Intestazioni di richiesta HTTP aggiunte alla firma. _necessaria_ |
| **Firma** | HMACSHA256 con codifica Base64 di stringa da firmare. _necessaria_|

### <a name="credential"></a>Credenziale

ID della chiave di accesso utilizzata per calcolare la firma.

### <a name="signed-headers"></a>Intestazioni firmate

Nomi di intestazione della richiesta HTTP, separati da punti e virgola, necessari per firmare la richiesta. Queste intestazioni HTTP devono essere fornite correttamente anche con la richiesta. Non usare gli spazi vuoti.

### <a name="required-http-request-headers"></a>Intestazioni richieste HTTP obbligatorie

```x-ms-date```[o ```Date``` ]; ```host``` ;```x-ms-content-sha256```

Tutte le altre intestazioni di richiesta HTTP possono essere aggiunte anche alla firma. È sufficiente accodarli all' ```SignedHeaders``` argomento.

**Esempio:**

x-ms-date; host; x-ms-content-SHA256; ```Content-Type``` ;```Accept```

### <a name="signature"></a>Firma

Hash HMACSHA256 con codifica Base64 della stringa da firmare. Usa la chiave di accesso identificata da `Credential` .
```base64_encode(HMACSHA256(String-To-Sign, Secret))```

### <a name="string-to-sign"></a>Stringa da firmare

Si tratta di una rappresentazione canonica della richiesta:

_Stringa da firmare =_

**HTTP_METHOD** +' \n ' + **path_and_query** +' \n ' + **signed_headers_values**

|  Argomento | Descrizione  |
| ------ | ------ |
| **HTTP_METHOD** | Nome del metodo HTTP in maiuscolo utilizzato con la richiesta. Per ulteriori informazioni, vedere la [sezione 9](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). |
|**path_and_query** | Concatenazione del percorso URI assoluto della richiesta e della stringa di query. Per ulteriori informazioni, vedere la [sezione 3,3](https://tools.ietf.org/html/rfc3986#section-3.3).
| **signed_headers_values** | Valori delimitati da punti e virgola di tutte le intestazioni di richiesta HTTP elencate in `SignedHeaders` . Il formato segue la `SignedHeaders` semantica. |

**Esempio:**

```js
string-To-Sign=
            "GET" + '\n' +                                                                                  // VERB
            "/kv?fields=*&api-version=1.0" + '\n' +                                                         // path_and_query
            "Fri, 11 May 2018 18:48:36 GMT;{myconfig}.azconfig.io;{value of ms-content-sha256 header}"      // signed_headers_values
```


### <a name="errors"></a>Errors

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Motivo:** Non è stata specificata l'intestazione della richiesta di autorizzazione con lo schema HMAC-SHA256.

**Soluzione:** Specificare un' ```Authorization``` intestazione di richiesta HTTP valida.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="The access token has expired", Bearer
```

**Motivo:** ```Date``` o ```x-ms-date``` l'intestazione della richiesta supera i 15 minuti dall'ora UTC (ora di Greenwich) corrente.

**Soluzione:** Specificare la data e l'ora corrette.


```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid access token date", Bearer
```

**Motivo:** Intestazione della richiesta mancante o non valida ```Date``` ```x-ms-date``` .

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="[Credential][SignedHeaders][Signature] is required", Bearer
```

**Motivo:** Manca un parametro obbligatorio dall' ```Authorization``` intestazione della richiesta.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Credential", Bearer
```

**Motivo:** Il [ ```Host``` ]/[ID chiave di accesso] specificato non è stato trovato.

**Soluzione:** Verificare il ```Credential``` parametro dell' ```Authorization``` intestazione della richiesta. Verificare che sia un ID valido per la chiave di accesso e assicurarsi che l' ```Host``` intestazione punti all'account registrato.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature", Bearer
```

**Motivo:** L'oggetto ```Signature``` fornito non corrisponde a quello previsto dal server.

**Soluzione:** Verificare che ```String-To-Sign``` sia corretto. Verificare che ```Secret``` sia corretto e utilizzato correttamente (decodifica Base64 prima di usare).

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Signed request header 'xxx' is not provided", Bearer
```

**Motivo:** Manca l'intestazione della richiesta richiesta dal ```SignedHeaders``` parametro nell'  ```Authorization``` intestazione.

**Soluzione:** Specificare l'intestazione obbligatoria, con il valore corretto.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="XXX is required as a signed header", Bearer
```

**Motivo:** Manca il parametro in ```SignedHeaders``` .

**Soluzione:** Verificare i requisiti minimi delle intestazioni firmate.

## <a name="code-snippets"></a>Frammenti di codice

### <a name="javascript"></a>JavaScript

*Prerequisiti*: [Crypto-JS](https://code.google.com/archive/p/crypto-js/)

```js
function signRequest(host, 
                     method,      // GET, PUT, POST, DELETE
                     url,         // path+query
                     body,        // request body (undefined of none)
                     credential,  // access key id
                     secret)      // access key value (base64 encoded)
{
        var verb = method.toUpperCase();
        var utcNow = new Date().toUTCString();
        var contentHash = CryptoJS.SHA256(body).toString(CryptoJS.enc.Base64);

        //
        // SignedHeaders
        var signedHeaders = "x-ms-date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = 
            verb + '\n' +                              // VERB
            url + '\n' +                               // path_and_query
            utcNow + ';' + host + ';' + contentHash;   // Semicolon separated SignedHeaders values

        //
        // Signature
        var signature = CryptoJS.HmacSHA256(stringToSign, CryptoJS.enc.Base64.parse(secret)).toString(CryptoJS.enc.Base64);

        //
        // Result request headers
        return [
            { name: "x-ms-date", value: utcNow },
            { name: "x-ms-content-sha256", value: contentHash },
            { name: "Authorization", value: "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signedHeaders + "&Signature=" + signature }
        ];
}
```

### <a name="c"></a>C#

```csharp
using (var client = new HttpClient())
{
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://{config store name}.azconfig.io/kv?api-version=1.0"),
        Method = HttpMethod.Get
    };

    //
    // Sign the request
    request.Sign(<Credential>, <Secret>);

    await client.SendAsync(request);
}

static class HttpRequestMessageExtensions
{
    public static HttpRequestMessage Sign(this HttpRequestMessage request, string credential, byte[] secret)
    {
        string host = request.RequestUri.Authority;
        string verb = request.Method.ToString().ToUpper();
        DateTimeOffset utcNow = DateTimeOffset.UtcNow;
        string contentHash = Convert.ToBase64String(request.Content.ComputeSha256Hash());

        //
        // SignedHeaders
        string signedHeaders = "date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = $"{verb}\n{request.RequestUri.PathAndQuery}\n{utcNow.ToString("r")};{host};{contentHash}";

        //
        // Signature
        string signature;

        using (var hmac = new HMACSHA256(secret))
        {
            signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign)));
        }

        //
        // Add headers
        request.Headers.Date = utcNow;
        request.Headers.Add("x-ms-content-sha256", contentHash);
        request.Headers.Authorization = new AuthenticationHeaderValue("HMAC-SHA256", $"Credential={credential}&SignedHeaders={signedHeaders}&Signature={signature}");

        return request;
    }
}

static class HttpContentExtensions
{
    public static byte[] ComputeSha256Hash(this HttpContent content)
    {
        using (var stream = new MemoryStream())
        {
            if (content != null)
            {
                content.CopyToAsync(stream).Wait();
                stream.Seek(0, SeekOrigin.Begin);
            }

            using (var alg = SHA256.Create())
            {
                return alg.ComputeHash(stream.ToArray());
            }
        }
    }
}
```

### <a name="java"></a>Java

```java
public CloseableHttpResponse signRequest(HttpUriRequest request, String credential, String secret)
        throws IOException, URISyntaxException {
    Map<String, String> authHeaders = generateHeader(request, credential, secret);
    authHeaders.forEach(request::setHeader);

    return httpClient.execute(request);
}

private static Map<String, String> generateHeader(HttpUriRequest request, String credential, String secret) 
        throws URISyntaxException, IOException {
    String requestTime = GMT_DATE_FORMAT.format(new Date());

    String contentHash = buildContentHash(request);
    // SignedHeaders
    String signedHeaders = "x-ms-date;host;x-ms-content-sha256";

    // Signature
    String methodName = request.getRequestLine().getMethod().toUpperCase();
    URIBuilder uri = new URIBuilder(request.getRequestLine().getUri());
    String scheme = uri.getScheme() + "://";
    String requestPath = uri.toString().substring(scheme.length()).substring(uri.getHost().length());
    String host = new URIBuilder(request.getRequestLine().getUri()).getHost();
    String toSign = String.format("%s\n%s\n%s;%s;%s", methodName, requestPath, requestTime, host, contentHash);

    byte[] decodedKey = Base64.getDecoder().decode(secret);
    String signature = Base64.getEncoder().encodeToString(new HmacUtils(HMAC_SHA_256, decodedKey).hmac(toSign));

    // Compose headers
    Map<String, String> headers = new HashMap<>();
    headers.put("x-ms-date", requestTime);
    headers.put("x-ms-content-sha256", contentHash);

    String authorization = String.format("HMAC-SHA256 Credential=%s, SignedHeaders=%s, Signature=%s",
            credential, signedHeaders, signature);
    headers.put("Authorization", authorization);

    return headers;
}

private static String buildContentHash(HttpUriRequest request) throws IOException {
    String content = "";
    if (request instanceof HttpEntityEnclosingRequest) {
        try {
            StringWriter writer = new StringWriter();
            IOUtils.copy(((HttpEntityEnclosingRequest) request).getEntity().getContent(), writer,
                    StandardCharsets.UTF_8);

            content = writer.toString();
        }
        finally {
            ((HttpEntityEnclosingRequest) request).getEntity().getContent().close();
        }
    }

    byte[] digest = new DigestUtils(SHA_256).digest(content);
    return Base64.getEncoder().encodeToString(digest);
}
```

### <a name="golang"></a>Golang

```golang
import (
    "bytes"
    "crypto/hmac"
    "crypto/sha256"
    "encoding/base64"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

//SignRequest Setup the auth header for accessing Azure App Configuration service
func SignRequest(id string, secret string, req *http.Request) error {
    method := req.Method
    host := req.URL.Host
    pathAndQuery := req.URL.Path
    if req.URL.RawQuery != "" {
        pathAndQuery = pathAndQuery + "?" + req.URL.RawQuery
    }

    content, err := ioutil.ReadAll(req.Body)
    if err != nil {
        return err
    }
    req.Body = ioutil.NopCloser(bytes.NewBuffer(content))

    key, err := base64.StdEncoding.DecodeString(secret)
    if err != nil {
        return err
    }

    timestamp := time.Now().UTC().Format(http.TimeFormat)
    contentHash := getContentHashBase64(content)
    stringToSign := fmt.Sprintf("%s\n%s\n%s;%s;%s", strings.ToUpper(method), pathAndQuery, timestamp, host, contentHash)
    signature := getHmac(stringToSign, key)

    req.Header.Set("x-ms-content-sha256", contentHash)
    req.Header.Set("x-ms-date", timestamp)
    req.Header.Set("Authorization", "HMAC-SHA256 Credential="+id+", SignedHeaders=x-ms-date;host;x-ms-content-sha256, Signature="+signature)

    return nil
}

func getContentHashBase64(content []byte) string {
    hasher := sha256.New()
    hasher.Write(content)
    return base64.StdEncoding.EncodeToString(hasher.Sum(nil))
}

func getHmac(content string, key []byte) string {
    hmac := hmac.New(sha256.New, key)
    hmac.Write([]byte(content))
    return base64.StdEncoding.EncodeToString(hmac.Sum(nil))
}
```

### <a name="python"></a>Python

```python

import base64
import hashlib
import hmac
from datetime import datetime
import six

def sign_request(host,
                method,     # GET, PUT, POST, DELETE
                url,        # Path + Query
                body,       # Request body 
                credential, # Access Key ID
                secret):    # Access Key Value
    verb = method.upper()

    utc_now = str(datetime.utcnow().strftime("%b, %d %Y %H:%M:%S ")) + "GMT"

    if six.PY2:
        content_digest = hashlib.sha256(bytes(body)).digest()
    else:
        content_digest = hashlib.sha256(bytes(body, 'utf-8')).digest()

    content_hash = base64.b64encode(content_digest).decode('utf-8')

    # Signed Headers
    signed_headers = "x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names

    # String-To-Sign
    string_to_sign = verb + '\n' + \
                    url + '\n' + \
                    utc_now + ';' + host + ';' + content_hash  # Semicolon separated SignedHeaders values

    # Decode secret
    if six.PY2:
        decoded_secret = base64.b64decode(secret)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign), hashlib.sha256).digest()
    else:
        decoded_secret = base64.b64decode(secret, validate=True)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign, 'utf-8'), hashlib.sha256).digest()

    # Signature
    signature = base64.b64encode(digest).decode('utf-8')

    # Result request headers
    return {
        "x-ms-date": utc_now,
        "x-ms-content-sha256": content_hash,
        "Authorization": "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signed_headers + "&Signature=" + signature
    }
```

### <a name="powershell"></a>PowerShell

```PowerShell
function Sign-Request(
    [string] $hostname,
    [string] $method,      # GET, PUT, POST, DELETE
    [string] $url,         # path+query
    [string] $body,        # request body
    [string] $credential,  # access key id
    [string] $secret       # access key value (base64 encoded)
)
{  
    $verb = $method.ToUpperInvariant()
    $utcNow = (Get-Date).ToUniversalTime().ToString("R", [Globalization.DateTimeFormatInfo]::InvariantInfo)
    $contentHash = Compute-SHA256Hash $body

    $signedHeaders = "x-ms-date;host;x-ms-content-sha256";  # Semicolon separated header names

    $stringToSign = $verb + "`n" +
                    $url + "`n" +
                    $utcNow + ";" + $hostname + ";" + $contentHash  # Semicolon separated signedHeaders values

    $signature = Compute-HMACSHA256Hash $secret $stringToSign

    # Return request headers
    return @{
        "x-ms-date" = $utcNow;
        "x-ms-content-sha256" = $contentHash;
        "Authorization" = "HMAC-SHA256 Credential=" + $credential + "&SignedHeaders=" + $signedHeaders + "&Signature=" + $signature
    }
}

function Compute-SHA256Hash(
    [string] $content
)
{
    $sha256 = [System.Security.Cryptography.SHA256]::Create()
    try {
        return [Convert]::ToBase64String($sha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $sha256.Dispose()
    }
}

function Compute-HMACSHA256Hash(
    [string] $secret,      # base64 encoded
    [string] $content
)
{
    $hmac = [System.Security.Cryptography.HMACSHA256]::new([Convert]::FromBase64String($secret))
    try {
        return [Convert]::ToBase64String($hmac.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $hmac.Dispose()
    }
}

# Stop if any error occurs
$ErrorActionPreference = "Stop"

$uri = [System.Uri]::new("https://{myconfig}.azconfig.io/kv?api-version=1.0")
$method = "GET"
$body = $null
$credential = "<Credential>"
$secret = "<Secret>"

$headers = Sign-Request $uri.Authority $method $uri.PathAndQuery $body $credential $secret
Invoke-RestMethod -Uri $uri -Method $method -Headers $headers -Body $body
```

### <a name="bash"></a>Bash

*Prerequisiti*:

| Prerequisito | Comando | Versioni testate |
| ------------ | ------- | --------------- |
| [Bash](https://www.gnu.org/software/bash/) | bash | 3.5.27, 4.4.23 |
| [Coreutils](https://www.gnu.org/software/coreutils/) | tr | 8,28 |
| [curl](https://curl.haxx.se/) | curl | 7.55.1, 7.58.0 |
| [OpenSSL](https://www.openssl.org/) | openssl | 1.1.0 g, 1.1.1 a |
| [util-linux](https://github.com/karelzak/util-linux/) | Hexdump | 2.14.1, 2.31.1 |

```bash
#!/bin/bash

sign_request () {
    local host="$1"
    local method="$2"      # GET, PUT, POST, DELETE
    local url="$3"         # path+query
    local body="$4"        # request body
    local credential="$5"  # access key id
    local secret="$6"      # access key value (base64 encoded)

    local verb=$(printf "$method" | tr '[:lower:]' '[:upper:]')
    local utc_now="$(date -u '+%a, %d %b %Y %H:%M:%S GMT')"
    local content_hash="$(printf "$body" | openssl sha256 -binary | base64)"

    local signed_headers="x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names
    local string_to_sign="$verb\n$url\n$utc_now;$host;$content_hash"  # Semicolon separated signed_headers values

    local decoded_secret="$(printf "$secret" | base64 -d | hexdump -v -e '/1 "%02x"')"
    local signature="$(printf "$string_to_sign" | openssl sha256 -mac HMAC -macopt hexkey:"$decoded_secret" -binary | base64)"

    # Output request headers
    printf '%s\n' \
           "x-ms-date: $utc_now" \
           "x-ms-content-sha256: $content_hash" \
           "Authorization: HMAC-SHA256 Credential=$credential&SignedHeaders=$signed_headers&Signature=$signature"
}

host="{config store name}.azconfig.io"
method="GET"
url="/kv?api-version=1.0"
body=""
credential="<Credential>"
secret="<Secret>"

headers=$(sign_request "$host" "$method" "$url" "$body" "$credential" "$secret")

while IFS= read -r line; do
    header_args+=("-H$line")
done <<< "$headers"
curl -X "$method" -d "$body" "${header_args[@]}" "https://$host$url"
```