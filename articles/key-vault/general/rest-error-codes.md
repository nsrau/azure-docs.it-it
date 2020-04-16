---
title: Codici di errore dell'API REST - Azure Key Vault
description: Questi codici di errore potrebbero essere restituiti da un'operazione in un servizio Web dell'insieme di chiavi di Azure.These error codes could be returned by an operation on an Azure Key Vault web service.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: bbb30c0ad41babca4158391c9e4e5c5d4d25cbf9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432060"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault REST API Error Codes
 
I codici di errore seguenti potrebbero essere restituiti da un'operazione in un servizio Web dell'insieme di chiavi di Azure.The following error codes could be returned by an operation on an Azure Key Vault web service.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Unauthenticated Request

401 indica che la richiesta non è autenticata per l'insieme di credenziali delle chiavi. 

Una richiesta viene autenticata se:A request is authenticated if:

- L'insieme di credenziali delle chiavi conosce l'identità del chiamante. E
- Il chiamante può tentare di accedere alle risorse dell'insieme di credenziali delle chiavi. 

Ci sono diversi motivi per cui una richiesta può restituire 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Nessun token di autenticazione collegato alla richiesta. 

Di seguito è riportato un esempio di richiesta PUT, che imposta il valore di un segreto:Here is an example PUT request, setting the value of a secret:

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

L'intestazione "Autorizzazione" è il token di accesso necessario a ogni chiamata all'insieme di credenziali delle chiavi per le operazioni del piano dati. Se l'intestazione non è presente, la risposta deve essere 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Il token non dispone della risorsa corretta associata. 

Quando si richiede un token di accesso dall'endpoint OAUTH di Azure, un parametro denominato "risorsa" è obbligatorio. Il valore è importante per il provider di token perché ha l'ambito del token per l'uso previsto. La risorsa per **tutti i** token per accedere a un insieme di credenziali delle chiavi è *https:\//vault.keyvault.net* (senza barra finale).

### <a name="the-token-is-expired"></a>Il token è scaduto

I token sono codificati in base64 e i valori [http://jwt.calebb.net](http://jwt.calebb.net)possono essere decodificati in siti Web come . Ecco il token di cui sopra decodificato:

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

Possiamo vedere molte parti importanti in questo token:

- aud (audience): la risorsa del token. Si noti <https://vault.azure.net>che si tratta di . Questo token NON funzionerà per qualsiasi risorsa che non corrisponde in modo esplicito a questo valore, ad esempio il grafico.
- iat (emesso a): il numero di zecche dall'inizio dell'epoca in cui è stato emesso il token.
- nbf (non prima): il numero di tick dall'inizio dell'epoca in cui questo token diventa valido.
- exp (scadenza): il numero di tick dall'inizio dell'epoca alla scadenza di questo token.
- appid (ID applicazione): GUID per l'ID applicazione che effettua la richiesta.
- tid (ID tenant): GUID per l'ID tenant dell'entità che effettua questa richiesta

È importante che tutti i valori vengano identificati correttamente nel token affinché la richiesta funzioni. Se tutto è corretto, la richiesta non comporterà 401.

### <a name="troubleshooting-401"></a>Risoluzione dei problemi 401

I 401 devono essere esaminati dal punto di generazione del token, prima che venga effettuata la richiesta all'insieme di credenziali delle chiavi. In genere il codice viene utilizzato per richiedere il token. Una volta ricevuto, il token viene passato alla richiesta dell'insieme di credenziali delle chiavi. Se il codice è in esecuzione localmente, è possibile utilizzare `https://login.microsoftonline.com`Fiddler per acquisire la richiesta/risposta a . Una richiesta è simile alla seguente:A request looks like this:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Le seguenti informazioni fornite dall'utente mush sono corrette:

- ID tenant dell'insieme di credenziali delle chiavi
- Valore della risorsa impostato su https%3A%2F%2Fvault.azure.net (codificato in URL)Resource value set to https%3A%2F%2Fvault.azure.net (URL encoded)
- ID client
- Segreto client

Assicurarsi che il resto della richiesta sia quasi identico.

Se è possibile ottenere solo il token di accesso alla risposta, è possibile decodificarlo (come illustrato in precedenza) per garantire l'ID tenant, l'ID client (ID app) e la risorsa.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Autorizzazioni insufficienti

HTTP 403 significa che la richiesta è stata autenticata (conosce l'identità richiedente) ma l'identità non dispone dell'autorizzazione per accedere alla risorsa richiesta. Ci sono due cause:

- Non esistono criteri di accesso per l'identità.
- L'indirizzo IP della risorsa richiedente non è inserito nella whitelist nelle impostazioni del firewall dell'insieme di credenziali delle chiavi.

HTTP 403 si verifica spesso quando l'applicazione del cliente non utilizza l'ID client che il cliente pensa che sia. Ciò significa in genere che i criteri di accesso non sono impostati correttamente per l'identità chiamante effettiva.

### <a name="troubleshooting-403"></a>Risoluzione dei problemi 403

In primo luogo, attivare la registrazione. Per istruzioni su come eseguire questa operazione, vedere [Registrazione dell'insieme](logging.md)di credenziali delle chiavi di Azure ).

Una volta attivata la registrazione, è possibile determinare se il 403 è dovuto ai criteri di accesso o ai criteri del firewall.

#### <a name="error-due-to-firewall-policy"></a>Errore dovuto a criteri firewall

"L'indirizzo client (00.00.00.00) non è autorizzato e il chiamante non è un servizio attendibile"

È disponibile un elenco limitato di "Servizi attendibili di Azure". I siti Web di Azure **non** sono un servizio di Azure attendibile. Per altre informazioni, vedere il post di blog Accesso al [firewall dell'insieme di credenziali](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)delle chiavi da parte dei servizi app di Azure.

È necessario aggiungere l'indirizzo IP del sito Web di Azure all'insieme di credenziali delle chiavi affinché funzioni.

Se dovuto ai criteri di accesso: trovare l'ID oggetto per la richiesta e assicurarsi che l'ID oggetto corrisponda all'oggetto a cui l'utente sta tentando di assegnare il criterio di accesso. Ci saranno spesso più oggetti in AAD che hanno lo stesso nome, quindi scegliere quello corretto è molto importante. Eliminando e aggiungendo nuovamente i criteri di accesso, è possibile verificare se esistono più oggetti con lo stesso nome.

Inoltre, la maggior parte dei criteri di accesso non richiede l'utilizzo dell'"applicazione autorizzata" come illustrato nel portale. L'applicazione autorizzata viene utilizzata per scenari di autenticazione "per conto di", che sono rari. 


## <a name="http-429-too-many-requests"></a>HTTP 429: Troppe richieste

La limitazione si verifica quando il numero di richieste supera il valore massimo indicato per l'intervallo di tempo. Se si verifica la limitazione, la risposta dell'insieme di credenziali delle chiavi sarà HTTP 429.If throttling occurs, the Key Vault's response will be HTTP 429. Sono indicati i massimi per i tipi di richieste effettuate. Ad esempio: la creazione di una chiave HSM a 2048 bit è di 5 richieste ogni 10 secondi, ma tutte le altre transazioni HSM hanno un limite di 1000 richieste/10 secondi. È pertanto importante comprendere quali tipi di chiamate vengono effettuate quando si determina la causa della limitazione delle richieste.
In generale, le richieste all'insieme di credenziali delle chiavi sono limitate a 2000 richieste e 10 secondi. Le eccezioni sono operazioni chiave, come documentato nei limiti del servizio Key VaultExceptions are Key Operations, as documented in [Key Vault service limits](service-limits.md)

### <a name="troubleshooting-429"></a>Risoluzione dei problemi 429
La limitazione delle richieste viene elaborata utilizzando queste tecniche:Throttling is worked around using these techniques:

- Ridurre il numero di richieste effettuate all'insieme di credenziali delle chiavi determinando se sono presenti modelli a una risorsa richiesta e tentando di memorizzarle nella cache nell'applicazione chiamante. 

- Quando si verifica la limitazione dell'insieme di credenziali delle chiavi, adattare il codice richiedente per usare un backoff esponenziale per riprovare. L'algoritmo è spiegato qui: [Come limitare l'app](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Se il numero di richieste non può essere ridotto mediante la memorizzazione nella cache e il backoff a tempo non funziona, è consigliabile suddividere le chiavi in più insiemi di credenziali delle chiavi. Il limite di servizio per una singola sottoscrizione è 5 volte il limite dell'insieme di chiavi individuale. Se si utilizzano più di 5 insiemi di credenziali delle chiavi, è necessario considerare l'utilizzo di più sottoscrizioni. 

Indicazioni dettagliate, inclusa la richiesta di aumentare i limiti, sono disponibili qui: [Guida alla limitazione delle richieste dell'insieme](overview-throttling.md) di credenziali delle chiavi


