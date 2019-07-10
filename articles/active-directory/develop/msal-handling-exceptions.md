---
title: Errori ed eccezioni (MSAL) | Azure
description: Informazioni su come gestire gli errori e le eccezioni, l'Accesso Condizionale e la richiesta di attestazioni nelle applicazioni MSAL.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 30ab8a3fec459bef1a85c44e9a7cdb91b541fa2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111387"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Gestione di errori ed eccezioni tramite MSAL
Le eccezioni in Microsoft Authentication Library (MSAL) sono destinate agli sviluppatori delle app per la risoluzione dei problemi, anziché per la visualizzazione agli utenti finali. I messaggi di eccezione non sono localizzati.

Durante l'elaborazione di eccezioni ed errori, è possibile usare il tipo di eccezione e il codice di errore per distinguere le eccezioni.  Per un elenco dei codici di errore, vedere [Codici di errore di autenticazione e autorizzazione](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>Eccezioni .NET
Durante l'elaborazione delle eccezioni, è possibile usare il tipo di eccezione e il membro `ErrorCode` per distinguere le eccezioni. I valori di `ErrorCode` sono costanti di tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

È anche possibile esaminare i campi di [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) e [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Se viene generata l'eccezione [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), il codice di errore può contenere un codice che è possibile trovare in [Codici di errore di autenticazione e autorizzazione](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Eccezioni comuni
Ecco le eccezioni più comuni che possono essere generate e alcune possibili mitigazioni.

| Eccezione | Codice di errore | Mitigazione|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione con ID '{appId}' denominata '{appName}'. Inviare una richiesta di autorizzazione interattiva per questo utente e questa risorsa.| È prima necessario ottenere il consenso dell'utente. Se non si usa .NET Core (che non dispone di alcuna interfaccia utente Web), chiamare (una sola volta) `AcquireTokeninteractive`. Se si usa .NET Core o non si vuole eseguire `AcquireTokenInteractive`, l'utente può visitare un URL per fornire il consenso: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. Per chiamare `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: l'utente deve usare l'autenticazione a più fattori.| Non è disponibile alcuna mitigazione: se l'autenticazione a più fattori è configurata per il tenant e AAD decide di applicarla, è necessario eseguire il fallback a un flusso interattivo, come `AcquireTokenInteractive` o `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: il tipo di concessione non è supportato tramite gli endpoint */common* o */consumers*. Usare */organizations* o l'endpoint specifico del tenant. È stato usato */common*.| Come spiegato nel messaggio di Azure AD, l'autorità deve disporre di un tenant oppure di */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: Il corpo della richiesta deve contenere il parametro seguente: 'client_secret or client_assertion'.| Questa situazione può verificarsi se l'applicazione non è stata registrata come un'applicazione client pubblica in Azure AD. Nel portale di Azure modificare il manifesto per l'applicazione e impostare `allowPublicClient` su `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| Messaggio unknown_user: non è stato possibile identificare l'utente connesso| La libreria non è riuscita a eseguire una query sull'utente corrente di Windows o l'utente non è stato aggiunto ad AD o AAD (gli utenti aggiunti a un'area di lavoro non sono supportati). Mitigazione 1: nella piattaforma UWP verificare che l'applicazione disponga delle funzionalità seguenti: Autenticazione aziendale, Reti private (client e server), Informazioni account utente. Mitigazione 2: implementare la propria logica per recuperare il nome utente (ad esempio, john@contoso.com) e usare il modulo `AcquireTokenByIntegratedWindowsAuth`, che accetta il nome utente.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Questo metodo si basa su un protocollo esposto da Active Directory (AD). Se un utente è stato creato in Azure Active Directory senza il supporto di AD (utente "gestito"), questo metodo avrà esito negativo. Gli utenti creati in AD e supportati da AAD (utenti "federati") possono trarre vantaggio da questo metodo di autenticazione non interattiva. Mitigazione: usare l'autenticazione interattiva.|

## <a name="javascript-errors"></a>Errori JavaScript

MSAL.js fornisce oggetti Error che astraggono e classificano i diversi tipi di errori comuni e dispongono di un'interfaccia per accedere agli specifici dettagli degli errori, ad esempio i messaggi di errore per gestirli adeguatamente.

**Oggetto Error**

```javascript                                
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```                
Estendendo la classe Error, è possibile accedere alle proprietà seguenti:
* **AuthError.message:** equivale a errorMessage.
* **AuthError.stack:** analisi dello stack per gli errori generati. Consente di risalire al punto di origine dell'errore.

**Tipi di errore**

Sono disponibili i tipi di errore seguenti:

* *AuthError:* classe Error di base per la libreria MSAL.js, usata anche per gli errori imprevisti.

* *ClientAuthError:* classe Error che indica un problema con l'autenticazione del client. La maggior parte degli errori che provengono dalla libreria saranno ClientAuthError. Potrebbe trattarsi di errori come la chiamata di un metodo di accesso quando l'accesso è in corso, l'annullamento dell'accesso da parte degli utenti e così via.

* *ClientConfigurationError:* classe Error che estende ClientAuthError generata prima che le richieste vengano effettuate quando i parametri di configurazione dell'utente specificato non sono corretti o sono mancanti.

* *ServerError:* classe Error per rappresentare le stringhe di errore inviate dal server di autenticazione. Può trattarsi di errori come parametri o formati della richiesta non validi oppure di eventuali altri errori che impediscono al server di eseguire l'autenticazione o l'autorizzazione dell'utente.

* *InteractionRequiredAuthError:* classe Error che estende ServerError per rappresentare gli errori del server che richiedono una chiamata interattiva. Questa eccezione viene generata da `acquireTokenSilent` se è necessario che l'utente interagisca con il server per fornire le credenziali o il consenso per l'autenticazione/autorizzazione. I codici di errore includono "interaction_required", "login_required", "consent_required".

Per la gestione degli errori nei flussi di autenticazione con metodi di reindirizzamento (`loginRedirect`, `acquireTokenRedirect`), sarà necessario registrare il callback che viene chiamato con esito positivo o negativo dopo il reindirizzamento tramite il metodo `handleRedirectCallback()` come indicato di seguito:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

I metodi per l'esperienza popup (`loginPopup`, `acquireTokenPopup`) restituiscono promesse, pertanto è possibile usare il modello per le promesse (.then e .catch) per gestirli, come illustrato di seguito:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Errori di interazione richiesta

Viene restituito un errore quando è necessaria un'interazione nell'interfaccia utente. Ciò significa che si è tentato di usare un metodo non interattivo per l'acquisizione di un token (ad esempio, `acquireTokenSilent`), ma MSAL non ha potuto eseguire questa operazione automaticamente. Le possibili cause sono:

* è necessario eseguire l'accesso
* è necessario fornire il consenso
* è necessario usare un'esperienza con autenticazione a più fattori.

La correzione consiste nel chiamare un metodo interattivo, ad esempio `acquireTokenPopup` o `acquireTokenRedirect`:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="conditional-access-and-claims-challenges"></a>Accesso Condizionale e richieste di attestazioni
Quando si recuperano automaticamente i token, l'applicazione potrebbe ricevere errori quando una [richiesta di attestazioni con Accesso Condizionale](conditional-access-dev-guide.md), ad esempio criteri di autenticazione a più fattori, è necessaria per un'API a cui si sta tentando di accedere.

Il modello per gestire questo errore consiste nell'acquisire in modo interattivo un token tramite MSAL. L'acquisizione di un token in modalità interattiva visualizza una richiesta all'utente e offre a quest'ultimo la possibilità di soddisfare i criteri di Accesso Condizionale richiesti.

Quando si chiama un'API che richiede l'Accesso Condizionale, in alcuni casi è possibile ricevere una richiesta di attestazioni nell'errore dall'API. Se ad esempio i criteri di Accesso Condizionale prevedono un dispositivo gestito (Intune), l'errore sarà simile a [AADSTS53000: è necessario un dispositivo gestito per l'accesso a questa risorsa](reference-aadsts-error-codes.md). In questo caso, è possibile passare le attestazioni nella chiamata per l'acquisizione del token, in modo che all'utente venga richiesto di soddisfare i criteri appropriati.

### <a name="net"></a>.NET
Quando si chiama un'API che richiede l'Accesso Condizionale da MSAL.NET, l'applicazione dovrà gestire le eccezioni relative alla richiesta di attestazione. Verrà generata un'eccezione [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), in cui la proprietà [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) non sarà vuota.

Per gestire la richiesta di attestazione, sarà necessario usare il metodo `.WithClaim()` della classe `PublicClientApplicationBuilder`.

### <a name="javascript"></a>JavaScript
Quando si recuperano automaticamente i token (tramite `acquireTokenSilent`) usando MSAL.js, l'applicazione potrebbe ricevere errori quando una [richiesta di attestazioni con Accesso Condizionale](conditional-access-dev-guide.md), ad esempio criteri di autenticazione a più fattori, è necessaria per un'API a cui si sta tentando di accedere.

Il modello per gestire questo errore consiste nell'effettuare una chiamata interattiva per acquisire il token in MSAL.js, ad esempio `acquireTokenPopup` o `acquireTokenRedirect`, come nell'esempio seguente:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

L'acquisizione del token in modalità interattiva visualizza una richiesta all'utente e offre a quest'ultimo la possibilità di soddisfare i criteri di Accesso Condizionale richiesti.

Quando si chiama un'API che richiede l'Accesso Condizionale, è possibile ricevere una richiesta di attestazioni nell'errore dall'API. In questo caso, è possibile passare le attestazioni restituite nell'errore come `extraQueryParameters` nella chiamata per l'acquisizione dei token, in modo che all'utente venga richiesto di soddisfare i criteri appropriati:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Nuovi tentativi dopo errori ed eccezioni

### <a name="http-error-codes-500-600"></a>Codici di errore HTTP 500 e 600
MSAL.NET implementa un semplice meccanismo per l'esecuzione di un nuovo tentativo per gli errori con codici di errore HTTP 500 e 600.

### <a name="http-429"></a>HTTP 429
Quando il server di token del servizio (STS) è occupato a causa di un "numero eccessivo di richieste", viene restituito un errore HTTP 429 con un'indicazione del momento in cui sarà possibile effettuare un nuovo tentativo (il campo Retry-After della risposta), come un ritardo in secondi o una data.

#### <a name="net"></a>.NET
L'eccezione [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) presenta `System.Net.Http.Headers.HttpResponseHeaders` come una proprietà `namedHeaders`. È pertanto possibile sfruttare le informazioni aggiuntive nel codice di errore per migliorare l'affidabilità delle applicazioni. Nel caso che abbiamo appena descritto è possibile usare `RetryAfterproperty` (di tipo `RetryConditionHeaderValue`) e calcolare quando effettuare il nuovo tentativo.

Di seguito è riportato un esempio per un'applicazione daemon (con il flusso di credenziali client), ma è possibile adattarlo a qualsiasi dei metodi di acquisizione di un token.

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
