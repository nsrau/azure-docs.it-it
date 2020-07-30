---
title: Codici di errore dell'API REST - Azure Key Vault
description: Questi codici di errore possono essere restituiti da un'operazione in un servizio Web Azure Key Vault.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: f0673732bd657b237ed0f4b4db276b8bf9a3a2b5
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424376"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Codici di errore dell'API REST di Azure Key Vault
 
I codici di errore seguenti possono essere restituiti da un'operazione in un servizio Web Azure Key Vault.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: richiesta non autenticata

401 indica che la richiesta non è autenticata per Key Vault. 

Una richiesta viene autenticata se:

- L'insieme di credenziali delle chiavi conosce l'identità del chiamante. e
- Il chiamante può provare ad accedere alle risorse Key Vault. 

Esistono diversi motivi per cui una richiesta può restituire 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Nessun token di autenticazione associato alla richiesta. 

Di seguito è riportato un esempio di richiesta PUT, che imposta il valore di un segreto:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

L'intestazione "Authorization" è il token di accesso richiesto con ogni chiamata al Key Vault per le operazioni del piano dati. Se l'intestazione non è presente, la risposta deve essere 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Al token manca la risorsa corretta associata. 

Quando si richiede un token di accesso dall'endpoint OAUTH di Azure, è obbligatorio un parametro denominato "Resource". Il valore è importante per il provider di token perché consente di definire l'ambito del token per l'utilizzo previsto. La risorsa per **tutti** i token per accedere a una Key Vault è *https: \/ /Vault.keyvault.NET* (senza barra finale).

### <a name="the-token-is-expired"></a>Il token è scaduto

I token sono con codifica Base64 e i valori possono essere decodificati in siti Web, ad esempio [http://jwt.calebb.net](http://jwt.calebb.net) . Il token precedente è stato decodificato:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

In questo token è possibile vedere molte parti importanti:

- AUD (audience): risorsa del token. Si noti che questo è <https://vault.azure.net> . Questo token non funzionerà per tutte le risorse che non corrispondono in modo esplicito a questo valore, ad esempio Graph.
- IAT (rilasciato a): numero di cicli dall'inizio dell'epoca in cui è stato emesso il token.
- NBF (not before): numero di cicli trascorsi dall'inizio dell'epoca in cui questo token diventa valido.
- Exp (scadenza): numero di cicli trascorsi dall'inizio dell'epoca in cui questo token scade.
- AppID (ID applicazione): il GUID dell'ID applicazione che effettua la richiesta.
- TID (ID tenant): GUID per l'ID tenant dell'entità che effettua la richiesta

È importante che tutti i valori siano identificati correttamente nel token affinché la richiesta funzioni. Se tutto è corretto, la richiesta non verrà restituita in 401.

### <a name="troubleshooting-401"></a>Risoluzione dei problemi 401

401S deve essere analizzato dal punto di generazione del token, prima che la richiesta venga effettuata all'insieme di credenziali delle chiavi. Il codice viene in genere usato per richiedere il token. Una volta ricevuto, il token viene passato nella richiesta Key Vault. Se il codice viene eseguito localmente, è possibile usare Fiddler per acquisire la richiesta/risposta a `https://login.microsoftonline.com` . Una richiesta ha un aspetto simile al seguente:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Le informazioni fornite dall'utente seguenti sono corrette:

- ID tenant dell'insieme di credenziali delle chiavi
- Il valore della risorsa è impostato su HTTPS %3 A %2 F %2 F Vault. Azure. NET (URL codificato)
- ID client
- Segreto client

Verificare che il resto della richiesta sia quasi identico.

Se è possibile ottenere solo il token di accesso della risposta, è possibile decodificarlo (come illustrato in precedenza) per assicurarsi che l'ID tenant, l'ID client (ID app) e la risorsa.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: autorizzazioni insufficienti

HTTP 403 indica che la richiesta è stata autenticata (conosce l'identità richiedente), ma l'identità non è autorizzata ad accedere alla risorsa richiesta. Esistono due cause:

- Non sono disponibili criteri di accesso per l'identità.
- L'indirizzo IP della risorsa richiedente non è inserito nell'elenco elementi consentiti nelle impostazioni del firewall di Key Vault.

Il protocollo HTTP 403 spesso si verifica quando l'applicazione del cliente non utilizza l'ID client che il cliente considera. Che in genere significa che i criteri di accesso non sono configurati correttamente per l'identità chiamante effettiva.

### <a name="troubleshooting-403"></a>Risoluzione dei problemi 403

Per prima cosa, attivare la registrazione. Per istruzioni su come eseguire questa operazione, vedere [Azure Key Vault registrazione](logging.md).

Dopo aver attivato la registrazione, è possibile determinare se 403 è dovuto a criteri di accesso o criteri del firewall.

#### <a name="error-due-to-firewall-policy"></a>Errore causato da criteri firewall

"L'indirizzo client (00.00.00.00) non è autorizzato e il chiamante non è un servizio attendibile"

È disponibile un elenco limitato di "servizi attendibili di Azure". I siti Web di Azure **non** sono un servizio di Azure attendibile. Per ulteriori informazioni, vedere il post [di blog Key Vault l'accesso al firewall da parte di app Azure Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Per poter funzionare, è necessario aggiungere l'indirizzo IP del sito Web di Azure al Key Vault.

Se a causa dei criteri di accesso, trovare l'ID oggetto per la richiesta e verificare che l'ID oggetto corrisponda all'oggetto a cui l'utente sta tentando di assegnare i criteri di accesso. In AAD saranno spesso presenti più oggetti con lo stesso nome, pertanto è molto importante scegliere quello corretto. Eliminando e aggiungendo nuovamente i criteri di accesso, è possibile verificare se esistono più oggetti con lo stesso nome.

Inoltre, la maggior parte dei criteri di accesso non richiede l'uso dell'"applicazione autorizzata", come illustrato nel portale. Le applicazioni autorizzate vengono usate per gli scenari di autenticazione "per conto di", rari. 


## <a name="http-429-too-many-requests"></a>HTTP 429: numero eccessivo di richieste

La limitazione si verifica quando il numero di richieste supera il limite massimo indicato per l'intervallo di tempo. Se si verifica una limitazione, la risposta del Key Vault sarà HTTP 429. Sono presenti valori massimi dichiarati per i tipi di richieste effettuate. Ad esempio: la creazione di una chiave HSM a 2048 bit è di 5 richieste per 10 secondi, ma tutte le altre transazioni HSM hanno un limite di 1000 richieste/10 secondi. È pertanto importante conoscere i tipi di chiamate effettuate durante la determinazione della limitazione.
In generale, le richieste al Key Vault sono limitate a 2000 richieste/10 secondi. Le eccezioni sono operazioni chiave, come documentato in [Key Vault limiti del servizio](service-limits.md)

### <a name="troubleshooting-429"></a>Risoluzione dei problemi 429
La limitazione delle richieste è ovvia all'uso di queste tecniche:

- Ridurre il numero di richieste effettuate al Key Vault determinando se sono presenti modelli per una risorsa richiesta e tentando di memorizzarli nella cache nell'applicazione chiamante. 

- Quando si verifica Key Vault limitazione, adattare il codice richiedente per usare un backoff esponenziale per riprovare. L'algoritmo è illustrato di seguito: [come limitare l'app](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Se il numero di richieste non può essere ridotto mediante la memorizzazione nella cache e il backoff temporizzato non funziona, provare a suddividere le chiavi in più insiemi di credenziali delle chiavi. Il limite di servizio per una singola sottoscrizione è 5x il singolo limite Key Vault. Se si usano più di 5 insiemi di credenziali delle chiavi, è necessario considerare l'uso di più sottoscrizioni. 

Indicazioni dettagliate, inclusa la richiesta di aumento dei limiti, sono disponibili qui: [linee guida per la limitazione Key Vault](overview-throttling.md)


