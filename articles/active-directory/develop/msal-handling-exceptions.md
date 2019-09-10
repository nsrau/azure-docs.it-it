---
title: Errori ed eccezioni (MSAL) | Azure
description: Informazioni su come gestire gli errori e le eccezioni, l'Accesso Condizionale e la richiesta di attestazioni nelle applicazioni MSAL.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: negoe
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 280746281fd45b3286cc76be5d3483f0cc65f90f
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70872795"
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

| Eccezione | Codice di errore | Attenuazione|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione con ID '{appId}' denominata '{appName}'. Inviare una richiesta di autorizzazione interattiva per questo utente e questa risorsa.| È prima necessario ottenere il consenso dell'utente. Se non si usa .NET Core (che non dispone di alcuna interfaccia utente Web), chiamare (una sola volta) `AcquireTokeninteractive`. Se si usa .NET Core o non si vuole eseguire un' `AcquireTokenInteractive`operazione, l'utente può passare a un URL per concedere il consenso:. https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read Per chiamare `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: l'utente deve usare l'autenticazione a più fattori.| Non è disponibile alcuna mitigazione: se l'autenticazione a più fattori è configurata per il tenant e AAD decide di applicarla, è necessario eseguire il fallback a un flusso interattivo, come `AcquireTokenInteractive` o `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: il tipo di concessione non è supportato tramite gli endpoint */common* o */consumers*. Usare */organizations* o l'endpoint specifico del tenant. È stato usato */common*.| Come spiegato nel messaggio di Azure AD, l'autorità deve disporre di un tenant oppure di */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: Il corpo della richiesta deve contenere il parametro seguente: 'client_secret or client_assertion'.| Questa eccezione può verificarsi se l'applicazione non è stata registrata come applicazione client pubblica in Azure AD. Nel portale di Azure modificare il manifesto per l'applicazione e impostare `allowPublicClient` su `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| Messaggio unknown_user: non è stato possibile identificare l'utente connesso| La libreria non è riuscita a eseguire una query sull'utente corrente di Windows o l'utente non è stato aggiunto ad AD o AAD (gli utenti aggiunti a un'area di lavoro non sono supportati). Mitigazione 1: nella piattaforma UWP verificare che l'applicazione disponga delle funzionalità seguenti: Autenticazione aziendale, Reti private (client e server), Informazioni account utente. Mitigazione 2: implementare la propria logica per recuperare il nome utente (ad esempio, john@contoso.com) e usare il modulo `AcquireTokenByIntegratedWindowsAuth`, che accetta il nome utente.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Questo metodo si basa su un protocollo esposto da Active Directory (AD). Se un utente è stato creato in Azure Active Directory senza il supporto di AD (utente "gestito"), questo metodo avrà esito negativo. Gli utenti creati in AD e supportati da AAD (utenti "federati") possono trarre vantaggio da questo metodo di autenticazione non interattiva. Mitigazione: usare l'autenticazione interattiva.|

### `MsalUiRequiredException`

Uno dei codici di stato comuni restituiti da MSAL.NET quando `AcquireTokenSilent()` si `MsalError.InvalidGrantError`chiama è. Questo codice di stato indica che l'applicazione deve chiamare nuovamente la libreria di autenticazione, ma in modalità interattiva (AcquireTokenInteractive o AcquireTokenByDeviceCodeFlow per le applicazioni client pubbliche ed eseguire una richiesta di verifica nelle app Web). Questo perché è necessaria un'interazione utente aggiuntiva prima di poter emettere il token di autenticazione.

Nella maggior parte dei casi `AcquireTokenSilent` , quando ha esito negativo, è perché la cache del token non ha token corrispondenti alla richiesta. I token di accesso scadono tra 1 ora e `AcquireTokenSilent` proveranno a recuperarne uno nuovo in base a un token di aggiornamento (in termini di OAuth2, questo è il flusso del token di aggiornamento). Questo flusso può anche avere esito negativo per diversi motivi, ad esempio se un amministratore tenant Configura criteri di accesso più rigorosi. 

L'interazione è finalizzata all'esecuzione di un'azione da parte dell'utente. Alcune di queste condizioni sono semplici per la risoluzione degli utenti (ad esempio, accettano le condizioni per l'utilizzo con un solo clic) e alcune non possono essere risolte con la configurazione corrente (ad esempio, il computer in questione deve connettersi a una specifica rete aziendale). Alcuni consentono all'utente di configurare l'autenticazione a più fattori o di installare Microsoft Authenticator nel dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`Enumerazione di classificazione

MSAL espone un `Classification` campo, che è possibile leggere per offrire un'esperienza utente migliore, ad esempio per indicare all'utente che la password è scaduta o che è necessario fornire il consenso per l'uso di alcune risorse. I valori supportati fanno parte dell' `UiRequiredExceptionClassification` enumerazione:

| classificazione    | Significato           | Gestione consigliata |
|-------------------|-------------------|----------------------|
| BasicAction | La condizione può essere risolta dall'interazione dell'utente durante il flusso di autenticazione interattiva. | Chiamare AcquireTokenInteractively (). |
| AdditionalAction | La condizione può essere risolta da un'interazione correttiva aggiuntiva con il sistema, all'esterno del flusso di autenticazione interattivo. | Chiamare AcquireTokenInteractively () per visualizzare un messaggio in cui viene illustrata l'azione correttiva. L'applicazione chiamante può scegliere di nascondere i flussi che richiedono additional_action se è improbabile che l'utente completi l'azione correttiva. |
| MessageOnly      | Non è possibile risolvere la condizione in questo momento. Se si avvia il flusso di autenticazione interattiva, viene visualizzato un messaggio che descrive la condizione. | Chiamare AcquireTokenInteractively () per visualizzare un messaggio in cui viene illustrata la condizione. AcquireTokenInteractively () restituirà l'errore UserCanceled dopo che l'utente legge il messaggio e chiude la finestra. L'applicazione chiamante può scegliere di nascondere i flussi che generano message_only se è improbabile che l'utente tragga vantaggio dal messaggio.|
| ConsentRequired  | Il consenso dell'utente è mancante o è stato revocato. | Chiamare AcquireTokenInteractively () per consentire all'utente di concedere il consenso. |
| UserPasswordExpired | La password dell'utente è scaduta. | Chiamare AcquireTokenInteractively () in modo che l'utente possa reimpostare la password. |
| PromptNeverFailed| È stata chiamata l'autenticazione interattiva con il prompt dei parametri = Never, forzando MSAL a basarsi sui cookie del browser e non visualizzare il browser. Questa operazione non è riuscita. | Chiamare AcquireTokenInteractively () senza prompt. None |
| AcquireTokenSilentFailed | MSAL SDK non dispone di informazioni sufficienti per recuperare un token dalla cache. Questo può essere dovuto al fatto che non sono presenti token nella cache o che non è stato trovato alcun account. Il messaggio di errore contiene maggiori dettagli.  | Chiamare AcquireTokenInteractively (). |
| Nessuna    | Non vengono forniti altri dettagli. La condizione può essere risolta dall'interazione dell'utente durante il flusso di autenticazione interattiva. | Chiamare AcquireTokenInteractively (). |

## <a name="code-example"></a>Esempio di codice

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
   res = await application.AcquireTokenInteractive(scopes)
                          .ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```


## <a name="javascript-errors"></a>Errori JavaScript

MSAL. js fornisce oggetti di errore che astraggono e classificano i diversi tipi di errori comuni. Fornisce inoltre l'interfaccia per accedere a dettagli specifici degli errori, ad esempio i messaggi di errore per gestirli in modo appropriato.

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
* **AuthError.message:**  Uguale a errorMessage.
* **AuthError.stack:** analisi dello stack per gli errori generati. Consente di risalire al punto di origine dell'errore.

**Tipi di errore**

Sono disponibili i tipi di errore seguenti:

* *AuthError:* classe Error di base per la libreria MSAL.js, usata anche per gli errori imprevisti.

* *ClientAuthError:* Classe Error, che indica un problema con l'autenticazione client. La maggior parte degli errori che provengono dalla libreria saranno ClientAuthError. Questi errori sono, ad esempio la chiamata a un metodo di accesso quando è in corso l'accesso, gli utenti che annullano l'accesso e così via. 

* *ClientConfigurationError:* Classe di errore che estende ClientAuthError prima che vengano eseguite richieste quando i parametri di configurazione utente specificati sono in formato non valido o mancante.

* *ServerError:* classe Error per rappresentare le stringhe di errore inviate dal server di autenticazione. Può trattarsi di errori come parametri o formati della richiesta non validi oppure di eventuali altri errori che impediscono al server di eseguire l'autenticazione o l'autorizzazione dell'utente.

* *InteractionRequiredAuthError:* Classe Error che estende ServerError per rappresentare gli errori del server, che richiedono una chiamata interattiva. Questo errore viene generato da `acquireTokenSilent` se l'utente è obbligato a interagire con il server per fornire le credenziali o il consenso per l'autenticazione/autorizzazione. I codici di errore includono "interaction_required", "login_required", "consent_required".

Per la gestione degli errori nei flussi di autenticazione con`loginRedirect`metodi `acquireTokenRedirect`di reindirizzamento (,), è necessario registrare il callback, che viene chiamato con esito positivo o negativo `handleRedirectCallback()` dopo il Reindirizzamento usando il metodo come indicato di seguito:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

I metodi per l'esperienza popup (`loginPopup`, `acquireTokenPopup`) restituiscono le promesse, quindi è possibile usare il modello di suggerimento (. then e. catch) per gestirli come illustrato:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Errori di interazione richiesta

Viene restituito un errore quando si tenta di usare un metodo non interattivo per acquisire un token (ad esempio, `acquireTokenSilent`) e MSAL non è riuscito a eseguirlo in modo invisibile all'utente.

Le possibili cause sono:

* l'utente deve eseguire l'accesso
* l'utente deve fornire il consenso
* l'utente deve effettuare un'esperienza di autenticazione a più fattori.

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
Quando si ottengono i token in modo invisibile all'utente, l'applicazione può ricevere errori quando una richiesta di [attestazione dell'accesso condizionale](conditional-access-dev-guide.md) , ad esempio i criteri di autenticazione a più fattori, è richiesta da un'API a cui si sta

Il modello per gestire questo errore consiste nell'acquisire in modo interattivo un token tramite MSAL. L'acquisizione di un token in modalità interattiva visualizza una richiesta all'utente e offre a quest'ultimo la possibilità di soddisfare i criteri di Accesso Condizionale richiesti.

Quando si chiama un'API che richiede l'Accesso Condizionale, in alcuni casi è possibile ricevere una richiesta di attestazioni nell'errore dall'API. Se ad esempio i criteri di Accesso Condizionale prevedono un dispositivo gestito (Intune), l'errore sarà simile a [AADSTS53000: è necessario un dispositivo gestito per l'accesso a questa risorsa](reference-aadsts-error-codes.md). In questo caso, è possibile passare le attestazioni nella chiamata per l'acquisizione del token, in modo che all'utente venga richiesto di soddisfare i criteri appropriati.

### <a name="net"></a>.NET
Quando si chiama un'API che richiede l'Accesso Condizionale da MSAL.NET, l'applicazione dovrà gestire le eccezioni relative alla richiesta di attestazione. Verrà generata un'eccezione [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), in cui la proprietà [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) non sarà vuota.

Per gestire la richiesta `.WithClaim()` `PublicClientApplicationBuilder` di attestazione, è necessario usare il metodo della classe.

### <a name="javascript"></a>JavaScript
Quando si ottengono i token in modo invisibile `acquireTokenSilent`all'utente (usando) con MSAL. js, l'applicazione può ricevere errori quando una richiesta di [attestazione dell'accesso condizionale](conditional-access-dev-guide.md) , ad esempio l'autenticazione a più fattori, è richiesta da un'API a cui si sta provando ad accedere.

Il modello per gestire questo errore consiste nell'effettuare una chiamata interattiva per acquisire il token in MSAL.js, ad esempio `acquireTokenPopup` o `acquireTokenRedirect`, come nell'esempio seguente:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

L'acquisizione del token in modalità interattiva visualizza una richiesta all'utente e offre a quest'ultimo la possibilità di soddisfare i criteri di Accesso Condizionale richiesti.

Quando si chiama un'API che richiede l'Accesso Condizionale, è possibile ricevere una richiesta di attestazioni nell'errore dall'API. In questo caso, è possibile passare le attestazioni restituite nell'errore al `claimsRequest` campo `AuthenticationParameters.ts` della classe per soddisfare i criteri appropriati. 

Per altri dettagli, vedere [richiesta di attestazioni aggiuntive](active-directory-optional-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Nuovi tentativi dopo errori ed eccezioni

Si prevede di implementare i criteri di ripetizione dei tentativi quando si chiama MSAL. MSAL esegue chiamate HTTP al servizio AAD e possono verificarsi errori occasionali, ad esempio la rete può diventare inattiva o il server è sovraccarico.  

### <a name="http-error-codes-500-600"></a>Codici di errore HTTP 500 e 600

MSAL.NET implementa un semplice meccanismo per l'esecuzione di un nuovo tentativo per gli errori con codici di errore HTTP 500 e 600.

### <a name="http-429"></a>HTTP 429

Quando il server del token del servizio (STS) viene sottoposto a overload con troppe richieste, viene restituito un errore HTTP 429 con un suggerimento su quando è possibile riprovare in termini di tempo. L'errore può essere letto dal `Retry-After` campo risposta.

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
