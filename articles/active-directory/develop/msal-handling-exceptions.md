---
title: Errori ed eccezioni (MSAL)
titleSuffix: Microsoft identity platform
description: Informazioni su come gestire gli errori e le eccezioni, l'accesso condizionale e le richieste di attestazione nelle applicazioni MSAL.
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
ms.date: 11/13/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5bfc5e6471d768b89a66610a2618bc1a44cf709d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145924"
---
# <a name="handle-msal-exceptions-and-errors"></a>Gestisci eccezioni ed errori di MSAL

Questo articolo fornisce una panoramica dei diversi tipi di errori e suggerimenti per la gestione degli errori di accesso comuni.

## <a name="msal-error-handling-basics"></a>Nozioni fondamentali sulla gestione degli errori MSAL

Le eccezioni in Microsoft Authentication Library (MSAL) sono destinate agli sviluppatori delle app per la risoluzione dei problemi, anziché per la visualizzazione agli utenti finali. I messaggi di eccezione non sono localizzati.

Durante l'elaborazione di eccezioni ed errori, è possibile usare il tipo di eccezione e il codice di errore per distinguere le eccezioni.  Per un elenco dei codici di errore, vedere [Codici di errore di autenticazione e autorizzazione](reference-aadsts-error-codes.md).

Durante l'acquisizione di token silenziosi o interattivi, è possibile che si verifichino errori durante l'esperienza di accesso, ad esempio errori relativi ai consensi, all'accesso condizionale (multi-factor authentication, alla gestione dei dispositivi, alle restrizioni basate sulla posizione), al rilascio e al riscatto dei token e Proprietà.

## <a name="msal-for-ios-and-macos-errors"></a>MSAL per errori iOS e macOS

L'elenco completo degli errori è elencato in [MSALError enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Tutti gli errori prodotti da MSAL vengono restituiti con `MSALErrorDomain` dominio.

Per gli errori di sistema, MSAL restituisce il `NSError` originale dall'API di sistema. Se, ad esempio, l'acquisizione di token ha esito negativo a causa della mancanza di connettività di rete, MSAL restituisce un errore con il dominio `NSURLErrorDomain` e il codice di `NSURLErrorNotConnectedToInternet`.

Si consiglia di gestire almeno i due errori MSAL seguenti sul lato client:

- `MSALErrorInteractionRequired`: l'utente deve eseguire una richiesta interattiva. Esistono molte condizioni che possono causare questo errore, ad esempio una sessione di autenticazione scaduta o la necessità di requisiti di autenticazione aggiuntivi. Chiamare l'API di acquisizione del token interattivo MSAL per il ripristino. 

- `MSALErrorServerDeclinedScopes`: alcuni o tutti gli ambiti sono stati rifiutati. Decidere se continuare solo con gli ambiti concessi oppure arrestare il processo di accesso.

> [!NOTE]
> L'enumerazione `MSALInternalError` deve essere utilizzata solo per riferimento e debug. Non tentare di gestire automaticamente questi errori in fase di esecuzione. Se l'app rileva uno degli errori che rientrano in `MSALInternalError`, è possibile che venga visualizzato un messaggio generico per l'utente che spiega cosa è successo.

Ad esempio, `MSALInternalErrorBrokerResponseNotReceived` significa che l'utente non ha completato l'autenticazione e ha restituito manualmente l'app. In questo caso, l'app dovrebbe mostrare un messaggio di errore generico che informa che l'autenticazione non è stata completata e suggerisce che tentano di eseguire di nuovo l'autenticazione.

Il codice di esempio Objective-C seguente illustra le procedure consigliate per la gestione di alcune condizioni di errore comuni:

Objective-C
```ObjC
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

Swift
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

## <a name="msal-for-python-error-handling"></a>MSAL per la gestione degli errori Python

In MSAL per Python, la maggior parte degli errori viene comunicata come valore restituito dalla chiamata API. L'errore è rappresentato da un dizionario che contiene la risposta JSON della piattaforma di identità Microsoft.

* Una risposta con esito positivo contiene la chiave `"access_token"`. Il formato della risposta è definito dal protocollo OAuth2. Per ulteriori informazioni, vedere [5,1 risposta riuscita](https://tools.ietf.org/html/rfc6749#section-5.1)
* Una risposta di errore contiene `"error"` e in genere `"error_description"`. Il formato della risposta è definito dal protocollo OAuth2. Per ulteriori informazioni, vedere [5,2 errore di risposta](https://tools.ietf.org/html/rfc6749#section-5.2)

Quando viene restituito un errore, la chiave `"error_description"` contiene un messaggio leggibile; che a sua volta contiene in genere un codice di errore della piattaforma Microsoft Identity. Per informazioni dettagliate sui diversi codici di errore, vedere [codici di errore di autenticazione e autorizzazione](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

In MSAL per Python le eccezioni sono rare perché la maggior parte degli errori viene gestita restituendo un valore di errore. L'eccezione `ValueError` viene generata solo quando si verifica un problema con il modo in cui si tenta di usare la libreria, ad esempio quando i parametri API non sono validi.

## <a name="net-exceptions"></a>Eccezioni .NET

Durante l'elaborazione delle eccezioni, è possibile usare il tipo di eccezione e il membro `ErrorCode` per distinguere le eccezioni. `ErrorCode` valori sono costanti di tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

È anche possibile esaminare i campi [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)e [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Se viene generata l'eccezione [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) , provare a eseguire i [codici di errore di autenticazione e autorizzazione](reference-aadsts-error-codes.md) per verificare se il codice è elencato.

### <a name="common-exceptions"></a>Eccezioni comuni

Ecco le eccezioni comuni che potrebbero essere generate e alcune possibili mitigazioni:  

| Eccezione | Codice di errore | Mitigazione|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione con ID ' {appId}' con nome ' {appName}'. Inviare una richiesta di autorizzazione interattiva per questo utente e questa risorsa.| È prima necessario ottenere il consenso dell'utente. Se non si usa .NET Core, che non dispone di un'interfaccia utente Web, chiamare (solo una volta) `AcquireTokeninteractive`. Se si usa .NET Core o non si vuole eseguire una `AcquireTokenInteractive`, l'utente può passare a un URL per concedere il consenso: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. per chiamare `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: l'utente è obbligato a usare multi-factor authentication.| Non esiste alcuna mitigazione. Se l'autenticazione a più fattori è configurata per il tenant e Azure Active Directory (AAD) decide di applicarla, è necessario eseguire il fallback a un flusso interattivo, ad esempio `AcquireTokenInteractive` o `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: il tipo di concessione non è supportato sugli endpoint */Common* o */consumers* . Usare */organizations* o l'endpoint specifico del tenant. È stato usato */common*.| Come spiegato nel messaggio di Azure AD, l'autorità deve disporre di un tenant oppure di */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: il corpo della richiesta deve contenere il parametro seguente: `client_secret or client_assertion`.| Questa eccezione può essere generata se l'applicazione non è stata registrata come applicazione client pubblica in Azure AD. Nella portale di Azure modificare il manifesto dell'applicazione e impostare `allowPublicClient` su `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: non è stato possibile identificare l'utente connesso| La libreria non è stata in grado di eseguire query sull'utente che ha eseguito l'accesso a Windows corrente o se l'utente non è un join AD o AAD (gli utenti aggiunti al posto di lavoro non sono supportati). Attenuazione 1: in UWP verificare che l'applicazione disponga delle funzionalità seguenti: autenticazione aziendale, reti private (client e server), informazioni sull'account utente. Attenuazione 2: implementare una logica personalizzata per recuperare il nome utente (ad esempio, john@contoso.com) e usare il modulo `AcquireTokenByIntegratedWindowsAuth` che accetta il nome utente.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Questo metodo si basa su un protocollo esposto da Active Directory (AD). Se un utente è stato creato in Azure Active Directory senza il supporto di AD (utente "gestito"), questo metodo avrà esito negativo. Gli utenti creati in AD e supportati da AAD (utenti "federati") possono trarre vantaggio da questo metodo di autenticazione non interattiva. Mitigazione: usare l'autenticazione interattiva.|

### `MsalUiRequiredException`

Uno dei codici di stato comuni restituiti da MSAL.NET quando si chiama `AcquireTokenSilent()` è `MsalError.InvalidGrantError`. Questo codice di stato indica che l'applicazione deve chiamare nuovamente la libreria di autenticazione, ma in modalità interattiva (AcquireTokenInteractive o AcquireTokenByDeviceCodeFlow per le applicazioni client pubbliche ed eseguire una richiesta di verifica nelle app Web). Questo perché è necessaria un'interazione utente aggiuntiva prima di poter emettere il token di autenticazione.

Nella maggior parte dei casi, quando `AcquireTokenSilent` ha esito negativo, è perché la cache del token non ha token corrispondenti alla richiesta. I token di accesso scadono tra 1 ora e `AcquireTokenSilent` tenterà di recuperarne uno nuovo in base a un token di aggiornamento (in termini di OAuth2, questo è il flusso del token di aggiornamento). Questo flusso può anche avere esito negativo per diversi motivi, ad esempio se un amministratore tenant Configura criteri di accesso più rigorosi. 

L'interazione è finalizzata all'esecuzione di un'azione da parte dell'utente. Alcune di queste condizioni sono semplici per la risoluzione degli utenti (ad esempio, accettano le condizioni per l'utilizzo con un solo clic) e alcune non possono essere risolte con la configurazione corrente (ad esempio, il computer in questione deve connettersi a una specifica rete aziendale). Alcuni consentono all'utente di configurare l'autenticazione a più fattori o di installare Microsoft Authenticator nel dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>Enumerazione `MsalUiRequiredException` classificazione

MSAL espone un campo `Classification`, che è possibile leggere per offrire un'esperienza utente migliore, ad esempio per indicare all'utente che la password è scaduta o che è necessario fornire il consenso per l'uso di alcune risorse. I valori supportati fanno parte dell'enumerazione `UiRequiredExceptionClassification`:

| Classificazione    | Significato           | Gestione consigliata |
|-------------------|-------------------|----------------------|
| BasicAction | La condizione può essere risolta dall'interazione dell'utente durante il flusso di autenticazione interattiva. | Chiamare AcquireTokenInteractively (). |
| AdditionalAction | La condizione può essere risolta da un'interazione correttiva aggiuntiva con il sistema, all'esterno del flusso di autenticazione interattivo. | Chiamare AcquireTokenInteractively () per visualizzare un messaggio in cui viene illustrata l'azione correttiva. L'applicazione chiamante può scegliere di nascondere i flussi che richiedono additional_action se è improbabile che l'utente completi l'azione correttiva. |
| MessageOnly      | Non è possibile risolvere la condizione in questo momento. Se si avvia il flusso di autenticazione interattiva, viene visualizzato un messaggio che descrive la condizione. | Chiamare AcquireTokenInteractively () per visualizzare un messaggio in cui viene illustrata la condizione. AcquireTokenInteractively () restituirà l'errore UserCanceled dopo che l'utente legge il messaggio e chiude la finestra. L'applicazione chiamante può scegliere di nascondere i flussi che generano message_only se l'utente non può trarre vantaggio dal messaggio.|
| ConsentRequired  | Il consenso dell'utente è mancante o è stato revocato. | Chiamare AcquireTokenInteractively () per consentire all'utente di concedere il consenso. |
| UserPasswordExpired | La password dell'utente è scaduta. | Chiamare AcquireTokenInteractively () in modo che l'utente possa reimpostare la password. |
| PromptNeverFailed| È stata chiamata l'autenticazione interattiva con il prompt dei parametri = Never, forzando MSAL a basarsi sui cookie del browser e non visualizzare il browser. Questa operazione non è riuscita. | Chiamare AcquireTokenInteractively () senza prompt. None |
| AcquireTokenSilentFailed | MSAL SDK non dispone di informazioni sufficienti per recuperare un token dalla cache. Questo può essere dovuto al fatto che non sono presenti token nella cache o che non è stato trovato alcun account. Il messaggio di errore contiene maggiori dettagli.  | Chiamare AcquireTokenInteractively (). |
| nessuno    | Non vengono forniti altri dettagli. La condizione può essere risolta dall'interazione dell'utente durante il flusso di autenticazione interattiva. | Chiamare AcquireTokenInteractively (). |

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

### <a name="error-object"></a>Oggetto Error

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
* **AuthError. Message:**  Uguale a errorMessage.
* **AuthError. stack:** Analisi dello stack per gli errori generati. Consente di risalire al punto di origine dell'errore.

### <a name="error-types"></a>Tipi di errore

Sono disponibili i tipi di errore seguenti:

- `AuthError`: classe di errore di base per la libreria MSAL. js, usata anche per gli errori imprevisti.

- `ClientAuthError`: classe Error, che indica un problema con l'autenticazione client. La maggior parte degli errori che provengono dalla libreria saranno ClientAuthError. Questi errori derivano da elementi come la chiamata a un metodo di accesso quando l'account di accesso è già in corso, l'utente annulla l'accesso e così via.

- `ClientConfigurationError`: classe Error, estende `ClientAuthError` generata prima che vengano eseguite richieste quando i parametri di configurazione dell'utente specificati non sono validi o sono mancanti.

- `ServerError`: classe Error, che rappresenta le stringhe di errore inviate dal server di autenticazione. Può trattarsi di errori come parametri o formati della richiesta non validi oppure di eventuali altri errori che impediscono al server di eseguire l'autenticazione o l'autorizzazione dell'utente.

- `InteractionRequiredAuthError`: classe Error, estende `ServerError` per rappresentare gli errori del server, che richiedono una chiamata interattiva. Questo errore viene generato da `acquireTokenSilent` se l'utente è necessario per interagire con il server per fornire le credenziali o il consenso per l'autenticazione/autorizzazione. I codici di errore includono `"interaction_required"`, `"login_required"`e `"consent_required"`.

Per la gestione degli errori nei flussi di autenticazione con metodi di reindirizzamento (`loginRedirect`, `acquireTokenRedirect`), è necessario registrare il callback, che viene chiamato con esito positivo o negativo dopo il Reindirizzamento usando `handleRedirectCallback()` metodo come indicato di seguito:

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

### <a name="interaction-required-errors"></a>Interazione richiesta, errori

Quando si tenta di usare un metodo non interattivo per acquisire un token, ad esempio `acquireTokenSilent`, viene restituito un errore, ma MSAL non è riuscito a eseguirlo in modo invisibile all'utente.

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

## <a name="conditional-access-and-claims-challenges"></a>Accesso Condizionale e richieste di attestazioni

Quando si ottengono i token in modo invisibile all'utente, l'applicazione può ricevere errori quando una richiesta di [attestazione dell'accesso condizionale](conditional-access-dev-guide.md) , ad esempio i criteri di autenticazione a più fattori, è richiesta da un'API a cui si sta

Il modello per la gestione di questo errore consiste nell'acquisire in modo interattivo un token usando MSAL. L'acquisizione di un token in modalità interattiva visualizza una richiesta all'utente e offre a quest'ultimo la possibilità di soddisfare i criteri di Accesso Condizionale richiesti.

Quando si chiama un'API che richiede l'Accesso Condizionale, in alcuni casi è possibile ricevere una richiesta di attestazioni nell'errore dall'API. Ad esempio, se i criteri di accesso condizionale hanno un dispositivo gestito (Intune), l'errore sarà simile a [AADSTS53000: il dispositivo deve essere gestito per accedere a questa risorsa](reference-aadsts-error-codes.md) o qualcosa di simile. In questo caso, è possibile passare le attestazioni nella chiamata per l'acquisizione del token, in modo che all'utente venga richiesto di soddisfare i criteri appropriati.

### <a name="net"></a>.NET

Quando si chiama un'API che richiede l'Accesso Condizionale da MSAL.NET, l'applicazione dovrà gestire le eccezioni relative alla richiesta di attestazione. Verrà generata un'eccezione [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), in cui la proprietà [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) non sarà vuota.

Per gestire la richiesta di attestazione, è necessario usare il metodo `.WithClaim()` della classe `PublicClientApplicationBuilder`.

### <a name="javascript"></a>JavaScript

Quando si ottengono i token in modo invisibile all'utente (usando `acquireTokenSilent`) con MSAL. js, l'applicazione può ricevere errori quando una richiesta di [attestazione dell'accesso condizionale](conditional-access-dev-guide.md) , ad esempio l'autenticazione a più fattori, è richiesta da un'API a cui si sta provando ad accedere.

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

Quando si chiama un'API che richiede l'Accesso Condizionale, è possibile ricevere una richiesta di attestazioni nell'errore dall'API. In questo caso, è possibile passare le attestazioni restituite nell'errore al campo `claimsRequest` della classe `AuthenticationParameters.ts` per soddisfare i criteri appropriati. 

Per altri dettagli, vedere [richiesta di attestazioni aggiuntive](active-directory-optional-claims.md) .

### <a name="msal-for-ios-and-macos"></a>MSAL per iOS e MacOS

MSAL per iOS e macOS ti permette di richiedere attestazioni specifiche negli scenari di acquisizione di token interattivi e invisibile all'utente.

Per richiedere attestazioni personalizzate, specificare `claimsRequest` in `MSALSilentTokenParameters` o `MSALInteractiveTokenParameters`.

Per altre informazioni, vedere [richiedere attestazioni personalizzate con MSAL per iOS e MacOS](request-custom-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Nuovi tentativi dopo errori ed eccezioni

Si prevede di implementare i criteri di ripetizione dei tentativi quando si chiama MSAL. MSAL esegue chiamate HTTP al servizio AAD e possono verificarsi errori occasionali, ad esempio la rete può diventare inattiva o il server è sovraccarico.  

### <a name="http-error-codes-500-600"></a>Codici di errore HTTP 500 e 600

MSAL.NET implementa un semplice meccanismo per l'esecuzione di un nuovo tentativo per gli errori con codici di errore HTTP 500 e 600.

### <a name="http-429"></a>HTTP 429

Quando il server del token del servizio (STS) viene sottoposto a overload con troppe richieste, restituisce l'errore HTTP 429 con un suggerimento sul tempo fino a quando non è possibile riprovare nel campo della risposta `Retry-After`.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) surfaces `System.Net.Http.Headers.HttpResponseHeaders` come `namedHeaders`della proprietà. È possibile utilizzare informazioni aggiuntive del codice di errore per migliorare l'affidabilità delle applicazioni. Nel caso descritto, è possibile usare il `RetryAfterproperty` (di tipo `RetryConditionHeaderValue`) e calcolare quando riprovare.

Di seguito è riportato un esempio di applicazione daemon con il flusso di credenziali client. È possibile adattare questa operazione a uno dei metodi per l'acquisizione di un token.

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
