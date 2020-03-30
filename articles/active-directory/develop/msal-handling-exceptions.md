---
title: Errori ed eccezioni (MSAL)
titleSuffix: Microsoft identity platform
description: Informazioni su come gestire errori ed eccezioni, accesso condizionale e problemi relativi alle attestazioni nelle applicazioni MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: f78c64fc0ba25dc3310b24e873dbae266ea2f281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050330"
---
# <a name="handle-msal-exceptions-and-errors"></a>Gestire le eccezioni e gli errori MSAL

In questo articolo viene fornita una panoramica dei diversi tipi di errori e suggerimenti per la gestione degli errori di accesso comuni.

## <a name="msal-error-handling-basics"></a>Nozioni di base sulla gestione degli errori MSAL

Le eccezioni nella libreria di autenticazione Microsoft (MSAL) sono destinate agli sviluppatori di app per risolvere i problemi, non per la visualizzazione agli utenti finali. I messaggi di eccezione non sono localizzati.

Durante l'elaborazione di eccezioni ed errori, è possibile usare il tipo di eccezione e il codice di errore per distinguere le eccezioni.  Per un elenco dei codici di errore, vedere [Codici di errore di autenticazione e autorizzazione](reference-aadsts-error-codes.md).

Durante l'esperienza di accesso, è possibile riscontrare errori relativi a consensi, accesso condizionale (MFA, gestione dei dispositivi, restrizioni basate sulla posizione), rilascio di token e riscatto e proprietà utente.

Per ulteriori informazioni sulla gestione degli errori per l'app, vedere la sezione seguente che corrisponde alla lingua in uso.

## <a name="net"></a>[.NET](#tab/dotnet)

Durante l'elaborazione di eccezioni .NET, `ErrorCode` è possibile utilizzare il tipo di eccezione stesso e il membro per distinguere tra le eccezioni. `ErrorCode`i valori sono costanti di tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

È inoltre possibile esaminare i campi di [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)e [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Se [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) viene generata, provare i codici di [errore di autenticazione e autorizzazione](reference-aadsts-error-codes.md) per verificare se il codice è elencato.

### <a name="common-net-exceptions"></a>Eccezioni .NET comuni

Di seguito sono riportate le eccezioni comuni che potrebbero essere generate e alcune possibili attenuazioni:Here are the common exceptions that might be thrown and some possible mitigations:  

| Eccezione | Codice di errore | Strategia di riduzione del rischio|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: l'utente o l'amministratore non ha acconsentito all'utilizzo dell'applicazione con ID ''appId'' denominato ''appName''. Inviare una richiesta di autorizzazione interattiva per questo utente e questa risorsa.| È prima necessario ottenere il consenso dell'utente. Se non si utilizza .NET Core (che non dispone di alcuna `AcquireTokeninteractive`interfaccia utente Web), chiamare (solo una volta) . Se si utilizza .NET core o non `AcquireTokenInteractive`si desidera eseguire un' operazione , `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`l'utente può passare a un URL per dare il consenso: . per `AcquireTokenInteractive`chiamare :`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: l'utente deve utilizzare l'autenticazione a più fattori (MFA).| Non c'è mitigazione. Se l'autenticazione a più fattori è configurata per il tenant e Azure Active Directory `AcquireTokenInteractive` (AAD) decide di applicarlo, è necessario eseguire il fallback a un flusso interattivo, ad esempio . `AcquireTokenByDeviceCode`|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: il tipo di concessione non è supportato sugli endpoint */common* o */consumers.* Usare */organizations* o l'endpoint specifico del tenant. È stato usato */common*.| Come spiegato nel messaggio di Azure AD, l'autorità deve disporre di un tenant oppure di */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: il corpo della richiesta `client_secret or client_assertion`deve contenere il parametro seguente: .| Questa eccezione può essere generata se l'applicazione non è stata registrata come applicazione client pubblica in Azure AD. Nel portale di Azure modificare il manifesto `allowPublicClient` `true`per l'applicazione e impostare su . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: impossibile identificare l'utente connesso| La libreria non è stata in grado di eseguire una query sull'utente di accesso a Windows corrente o l'utente non è ad Active Directory o AAD aggiunto (gli utenti uniti al luogo di lavoro non sono supportati). Mitigazione 1: nella piattaforma UWP, verificare che l'applicazione disponga delle seguenti funzionalità: Autenticazione aziendale, Reti private (client e Server), Informazioni sull'account utente. Mitigazione 2: implementare la propria logica per john@contoso.comrecuperare il `AcquireTokenByIntegratedWindowsAuth` nome utente (ad esempio, ) e utilizzare il modulo che accetta il nome utente.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Questo metodo si basa su un protocollo esposto da Active Directory (AD). Se un utente è stato creato in Azure Active Directory senza il supporto di AD (utente "gestito"), questo metodo avrà esito negativo. Gli utenti creati in AD e supportati da AAD (utenti "federati") possono trarre vantaggio da questo metodo di autenticazione non interattiva. Mitigazione: usare l'autenticazione interattiva.|

### `MsalUiRequiredException`

Uno dei codici di stato comuni `AcquireTokenSilent()` `MsalError.InvalidGrantError`restituiti da MSAL.NET quando si chiama è . Questo codice di stato indica che l'applicazione deve chiamare nuovamente la libreria di autenticazione, ma in modalità interattiva (AcquireTokenInteractive o AcquireTokenByDeviceCodeFlow per le applicazioni client pubbliche ed eseguire una sfida nelle applicazioni Web). Ciò è dovuto al fatto che è necessaria un'interazione aggiuntiva da parte dell'utente prima di poter emettere il token di autenticazione.

Nella maggior parte `AcquireTokenSilent` dei momenti in cui non riesce, è perché la cache dei token non dispone di token corrispondenti alla richiesta. I token di accesso scadono `AcquireTokenSilent` tra 1 ora e tenteranno di recuperarne uno nuovo in base a un token di aggiornamento (in termini OAuth2, questo è il flusso del "token di aggiornamento"). Questo flusso può avere esito negativo anche per vari motivi, ad esempio se un amministratore tenant configura criteri di accesso più rigorosi. 

L'interazione mira a far fare all'utente un'azione. Alcune di queste condizioni sono facili da risolvere per gli utenti (ad esempio, accettare le Condizioni per l'utilizzo con un solo clic) e alcune non possono essere risolte con la configurazione corrente (ad esempio, la macchina in questione deve connettersi a una rete aziendale specifica). Alcuni aiutano l'utente a configurare l'autenticazione a più fattori o a installare Microsoft Authenticator nel proprio dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`enumerazione di classificazione

MSAL espone `Classification` un campo, che è possibile leggere per fornire un'esperienza utente migliore, ad esempio per comunicare all'utente che la password è scaduta o che sarà necessario fornire il consenso per l'utilizzo di alcune risorse. I valori supportati fanno `UiRequiredExceptionClassification` parte dell'enumerazione:

| Classificazione    | Significato           | Maneggevolezza consigliata |
|-------------------|-------------------|----------------------|
| Azione di baseBasicAction | La condizione può essere risolta tramite l'interazione dell'utente durante il flusso di autenticazione interattivo. | Chiamare AcquireTokenInteractively(). |
| AdditionalAction | La condizione può essere risolta mediante un'interazione correttiva aggiuntiva con il sistema, all'esterno del flusso di autenticazione interattivo. | Chiamare AcquireTokenInteractively() per visualizzare un messaggio che spiega l'azione correttiva. L'applicazione chiamante può scegliere di nascondere i flussi che richiedono additional_action se è improbabile che l'utente completi l'azione correttiva. |
| MessageOnly (Solo messaggio)      | Impossibile risolvere la condizione in questo momento. L'avvio del flusso di autenticazione interattivo mostrerà un messaggio che spiega la condizione. | Chiamare AcquireTokenInteractively() per visualizzare un messaggio che spiega la condizione. AcquireTokenInteractively() restituirà l'errore UserCanceled dopo che l'utente ha letto il messaggio e chiude la finestra. L'applicazione chiamante può scegliere di nascondere i flussi che generano message_only se è improbabile che l'utente tragga vantaggio dal messaggio.|
| ConsensoObbligatorio  | Il consenso dell'utente è mancante o è stato revocato. | Chiamare AcquireTokenInteractively() per consentire all'utente di dare il consenso. |
| UserPasswordExpired | La password dell'utente è scaduta. | Chiamare AcquireTokenInteractively() in modo che l'utente possa reimpostare la password. |
| PromptNeverFailed| L'autenticazione interattiva è stata chiamata con il parametro prompt, mai, costringendo MSAL a fare affidamento sui cookie del browser e a non visualizzare il browser. Questo non è riuscito. | Chiamare AcquireTokenInteractively() senza Prompt.None |
| AcquireTokenSilentFailed | MSAL SDK non dispone di informazioni sufficienti per recuperare un token dalla cache. Ciò può essere dovuto al fatto che non sono presenti token nella cache o che non è stato trovato un account. Il messaggio di errore contiene ulteriori dettagli.  | Chiamare AcquireTokenInteractively(). |
| nessuno    | Non vengono forniti ulteriori dettagli. La condizione può essere risolta dall'interazione dell'utente durante il flusso di autenticazione interattivo. | Chiamare AcquireTokenInteractively(). |

## <a name="net-code-example"></a>Esempio di codice .NET

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
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
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

## <a name="javascript"></a>[Javascript](#tab/javascript)

MSAL.js fornisce oggetti di errore che astraggono e classificano i diversi tipi di errori comuni. Fornisce inoltre un'interfaccia per accedere a dettagli specifici degli errori, ad esempio messaggi di errore, per gestirli in modo appropriato.

### <a name="error-object"></a>Oggetto errore

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
- `AuthError.message`: uguale `errorMessage`al file .
- `AuthError.stack`: analisi dello stack per individuare gli errori generati.

### <a name="error-types"></a>Tipi di errore

Sono disponibili i tipi di errore seguenti:

- `AuthError`: classe di errore di base per la libreria MSAL.js, utilizzata anche per errori imprevisti.

- `ClientAuthError`: classe di errore che indica un problema con l'autenticazione client. La maggior parte degli errori che provengono dalla libreria saranno ClientAuthError. Questi errori derivano da cose come la chiamata di un metodo di accesso quando l'accesso è già in corso, l'utente annulla l'accesso e così via.

- `ClientConfigurationError`: classe Error, estende generato `ClientAuthError` prima che vengano effettuate le richieste quando i parametri di configurazione utente specificati non sono in formato valido o mancanti.

- `ServerError`: classe Error, rappresenta le stringhe di errore inviate dal server di autenticazione. Può trattarsi di errori come parametri o formati della richiesta non validi oppure di eventuali altri errori che impediscono al server di eseguire l'autenticazione o l'autorizzazione dell'utente.

- `InteractionRequiredAuthError`: classe error, si estende `ServerError` per rappresentare gli errori del server, che richiedono una chiamata interattiva. Questo errore viene `acquireTokenSilent` generato se l'utente deve interagire con il server per fornire le credenziali o il consenso per l'autenticazione/autorizzazione. I codici `"interaction_required"` `"login_required"`di `"consent_required"`errore includono , , e .

Per la gestione degli errori`loginRedirect`nei `acquireTokenRedirect`flussi di autenticazione con i metodi di reindirizzamento ( `handleRedirectCallback()` , ), è necessario registrare il callback, che viene chiamato con esito positivo o negativo dopo il reindirizzamento utilizzando il metodo come segue:

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

### <a name="errors-that-require-interaction"></a>Errori che richiedono interazione

Viene restituito un errore quando si tenta di utilizzare un metodo `acquireTokenSilent`non interattivo di acquisizione di un token, ad esempio , ma MSAL non è stato in grado di eseguire automaticamente.

Le possibili cause sono:

- è necessario eseguire l'accesso
- è necessario fornire il consenso
- è necessario usare un'esperienza con autenticazione a più fattori.

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

## <a name="python"></a>[Python](#tab/python)

In MSAL per Python, la maggior parte degli errori viene trasmessa come valore restituito dalla chiamata API. L'errore è rappresentato come un dizionario contenente la risposta JSON dalla piattaforma di identità Microsoft.The error is represented as a dictionary containing the JSON response from the Microsoft identity platform.

* Una risposta corretta `"access_token"` contiene la chiave. Il formato della risposta è definito dal protocollo OAuth2. Per ulteriori informazioni, vedere [5.1 Risposta corretta](https://tools.ietf.org/html/rfc6749#section-5.1)
* Una risposta `"error"` di `"error_description"`errore contiene e in genere . Il formato della risposta è definito dal protocollo OAuth2. Per altre informazioni, vedere [5.2 Risposta di erroreFor more information, see 5.2 Error Response](https://tools.ietf.org/html/rfc6749#section-5.2)

Quando viene restituito un `"error_description"` errore, la chiave contiene un messaggio leggibile; che a sua volta in genere contiene un codice di errore della piattaforma di identità Microsoft. Per informazioni dettagliate sui vari codici di errore, vedere Codici di errore di [autenticazione e autorizzazione](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

In MSAL per Python, le eccezioni sono rare perché la maggior parte degli errori vengono gestiti restituendo un valore di errore. L'eccezione `ValueError` viene generata solo quando si verifica un problema con il modo in cui si sta tentando di utilizzare la libreria, ad esempio quando i parametri API non sono in formato corretto.

## <a name="java"></a>[Java](#tab/java)

In MSAL per Java sono disponibili tre `MsalClientException` `MsalServiceException`tipi `MsalInteractionRequiredException`di eccezioni: , , e ; tutto ciò `MsalException`che eredita da .

- `MsalClientException`viene generata un'eccezione quando si verifica un errore locale alla libreria o al dispositivo.
- `MsalServiceException`viene generata quando il servizio token di sicurezza restituisce una risposta di errore o si verifica un altro errore di rete.
- `MsalInteractionRequiredException`viene generata quando l'interazione dell'interfaccia utente è necessaria per l'autenticazione abbia esito positivo.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`espone le intestazioni HTTP restituite nelle richieste al servizio token di sicurezza. Accedi te tramite`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Uno dei codici di stato comuni restituiti `AcquireTokenSilently()` `InvalidGrantError`da MSAL per Java durante la chiamata è . Ciò significa che è necessaria un'interazione aggiuntiva da parte dell'utente prima di poter emettere un token di autenticazione. L'applicazione deve chiamare nuovamente la libreria di `AuthorizationCodeParameters` `DeviceCodeParameters` autenticazione, ma in modalità interattiva inviando o per le applicazioni client pubbliche.

Nella maggior parte `AcquireTokenSilently` dei momenti in cui ha esito negativo, è perché la cache dei token non dispone di un token corrispondente alla richiesta. I token di accesso scadono `AcquireTokenSilently` tra un'ora e tenteranno di ottenerli di nuovo in base a un token di aggiornamento. In termini OAuth2, questo è il flusso del token di aggiornamento. Questo flusso può anche avere esito negativo per vari motivi, ad esempio quando un amministratore tenant configura criteri di accesso più rigorosi.

Alcune condizioni che causano questo errore sono facili da risolvere per gli utenti. Ad esempio, potrebbe essere necessario accettare le Condizioni per l'utilizzo. O forse la richiesta non può essere soddisfatta con la configurazione corrente perché il computer deve connettersi a una rete aziendale specifica.

MSAL espone `reason` un campo, che è possibile utilizzare per fornire un'esperienza utente migliore. Ad esempio, `reason` il campo potrebbe indurre l'utente a comunicare all'utente che la password è scaduta o che dovrà fornire il consenso per utilizzare alcune risorse. I valori supportati fanno `InteractionRequiredExceptionReason` parte dell'enumerazione:

| Motivo | Significato | Movimentazione consigliata |
|---------|-----------|-----------------------------|
| `BasicAction` | La condizione può essere risolta tramite l'interazione dell'utente durante il flusso di autenticazione interattivoCondition can be resolved by user interaction during the interactive authentication flow | Chiamata `acquireToken` con parametri interattivi |
| `AdditionalAction` | La condizione può essere risolta mediante un'interazione correttiva aggiuntiva con il sistema all'esterno del flusso di autenticazione interattivo. | Chiama `acquireToken` con parametri interattivi per mostrare un messaggio che spiega l'azione correttiva da intraprendere. L'app chiamante può scegliere di nascondere i flussi che richiedono un'azione aggiuntiva se è improbabile che l'utente completi l'azione correttiva. |
| `MessageOnly` | Impossibile risolvere la condizione in questo momento. Avviare il flusso di autenticazione interattivo per visualizzare un messaggio che spiega la condizione. | Chiamare `acquireToken` con parametri interattivi per visualizzare un messaggio che spiega la condizione. `acquireToken`restituirà `UserCanceled` l'errore dopo che l'utente ha letto il messaggio e chiude la finestra. L'app può scegliere di nascondere i flussi che generano messaggi se è improbabile che l'utente tragga vantaggio dal messaggio. |
| `ConsentRequired`| Il consenso dell'utente è mancante o è stato revocato. |Chiamare `acquireToken` con parametri interattivi in modo che l'utente possa dare il consenso. |
| `UserPasswordExpired` | La password dell'utente è scaduta. | Chiamare `acquireToken` con il parametro interattivo in modo che l'utente possa reimpostare la password |
| `None` |  Ulteriori dettagli sono forniti. La condizione può essere risolta dall'interazione dell'utente durante il flusso di autenticazione interattivo. | Chiamata `acquireToken` con parametri interattivi |

### <a name="code-example"></a>Esempio di codice

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

L'elenco completo degli errori MSAL per iOS e macOS è elencato in [MSALError enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Tutti gli errori MSAL `MSALErrorDomain` generati vengono restituiti con dominio.

Per gli errori di sistema, MSAL restituisce l'originale `NSError` dall'API di sistema. Ad esempio, se l'acquisizione di token non riesce a `NSURLErrorDomain` causa `NSURLErrorNotConnectedToInternet` della mancanza di connettività di rete, MSAL restituisce un errore con il dominio e il codice.

Si consiglia di gestire almeno i seguenti due errori MSAL sul lato client:

- `MSALErrorInteractionRequired`: l'utente deve eseguire una richiesta interattiva. Esistono molte condizioni che possono causare questo errore, ad esempio una sessione di autenticazione scaduta o la necessità di requisiti di autenticazione aggiuntivi. Chiamare l'API di acquisizione di token interattiva MSAL per il ripristino. 

- `MSALErrorServerDeclinedScopes`: alcuni o tutti gli ambiti sono stati rifiutati. Decidere se continuare solo con gli ambiti concessi o interrompere il processo di accesso.

> [!NOTE]
> L'enumerazione `MSALInternalError` deve essere utilizzata solo per riferimento e debug. Non tentare di gestire automaticamente questi errori in fase di esecuzione. Se l'app rileva uno degli `MSALInternalError`errori che rientrano in , è possibile visualizzare un messaggio generico rivolto a un utente che spiega cosa è successo.

Ad esempio, `MSALInternalErrorBrokerResponseNotReceived` significa che l'utente non ha completato l'autenticazione e restituito manualmente all'app. In questo caso, l'app dovrebbe mostrare un messaggio di errore generico che spiega che l'autenticazione non è stata completata e suggerisce di provare a eseguire di nuovo l'autenticazione.

Il codice di esempio Objective-C seguente illustra le procedure consigliate per la gestione di alcune condizioni di errore comuni:The following Objective-C sample code demonstrates best practices for handling some common error conditions:

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>Accesso Condizionale e richieste di attestazioni

Quando si ottengono i token in modo invisibile all'utente, l'applicazione potrebbe ricevere errori quando un problema di richiesta di [attestazioni di accesso condizionale,](../azuread-dev/conditional-access-dev-guide.md) ad esempio i criteri di autenticazione a più fattori, è richiesto da un'API a cui si sta tentando di accedere.

Il modello per la gestione di questo errore consiste nell'acquisire in modo interattivo un token utilizzando MSAL. L'acquisizione di un token in modalità interattiva visualizza una richiesta all'utente e offre a quest'ultimo la possibilità di soddisfare i criteri di Accesso Condizionale richiesti.

Quando si chiama un'API che richiede l'Accesso Condizionale, in alcuni casi è possibile ricevere una richiesta di attestazioni nell'errore dall'API. Ad esempio, se il criterio di accesso condizionale deve avere un dispositivo gestito (Intune) l'errore sarà simile [a AADSTS53000: il dispositivo deve essere gestito per accedere a questa risorsa](reference-aadsts-error-codes.md) o qualcosa di simile. In questo caso, è possibile passare le attestazioni nella chiamata per l'acquisizione del token, in modo che all'utente venga richiesto di soddisfare i criteri appropriati.

### <a name="net"></a>.NET

Quando si chiama un'API che richiede l'Accesso Condizionale da MSAL.NET, l'applicazione dovrà gestire le eccezioni relative alla richiesta di attestazione. Verrà generata un'eccezione [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), in cui la proprietà [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) non sarà vuota.

Per gestire la richiesta di verifica attestazione, è necessario utilizzare il `.WithClaim()` metodo della `PublicClientApplicationBuilder` classe.

### <a name="javascript"></a>JavaScript

Quando si ottengono i `acquireTokenSilent`token in modo invisibile all'utente (utilizzando ) utilizzando MSAL.js, l'applicazione potrebbe ricevere errori quando un problema di richiesta di informazioni sull'accesso [condizionale,](../azuread-dev/conditional-access-dev-guide.md) ad esempio i criteri di autenticazione a più fattori, ovvero un'API a cui si sta tentando di accedere.

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

Quando si chiama un'API che richiede l'Accesso Condizionale, è possibile ricevere una richiesta di attestazioni nell'errore dall'API. In questo caso, è possibile passare le attestazioni restituite nell'errore al `claimsRequest` campo della `AuthenticationParameters.ts` classe per soddisfare i criteri appropriati. 

Per ulteriori informazioni, vedere [Richiesta di attestazioni aggiuntive.](active-directory-optional-claims.md)

### <a name="msal-for-ios-and-macos"></a>MSAL per iOS e MacOS

MSAL per iOS e macOS consente di richiedere attestazioni specifiche in scenari di acquisizione di token interattivi e silenziosi.

Per richiedere attestazioni `claimsRequest` `MSALSilentTokenParameters` personalizzate, specificare in o `MSALInteractiveTokenParameters`.

Per altre info, vedi [Richiedere attestazioni personalizzate tramite MSAL per iOS e macOS.](request-custom-claims.md)

## <a name="retrying-after-errors-and-exceptions"></a>Nuovi tentativi dopo errori ed eccezioni

È necessario implementare i criteri di ripetizione dei tentativi quando si chiama MSAL. MSAL effettua chiamate HTTP al servizio AAD e possono verificarsi errori occasionali, ad esempio la rete può arrestarsi o il server è sovraccarico.  

### <a name="http-error-codes-500-600"></a>Codici di errore HTTP 500 e 600

MSAL.NET implementa un semplice meccanismo per l'esecuzione di un nuovo tentativo per gli errori con codici di errore HTTP 500 e 600.

### <a name="http-429"></a>HTTP 429

Quando il server token di servizio (STS) è sovraccarico di troppe richieste, restituisce l'errore HTTP `Retry-After` 429 con un suggerimento su quanto tempo fino a quando è possibile riprovare nel campo di risposta.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) viene `System.Net.Http.Headers.HttpResponseHeaders` eseguito `namedHeaders`come proprietà . È possibile utilizzare informazioni aggiuntive dal codice di errore per migliorare l'affidabilità delle applicazioni. Nel caso descritto, è `RetryAfterproperty` possibile utilizzare `RetryConditionHeaderValue`i (di tipo ) e calcolare quando riprovare.

Di seguito è riportato un esempio per un'applicazione daemon che usa il flusso di credenziali client. È possibile adattarlo a uno qualsiasi dei metodi per l'acquisizione di un token.

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
