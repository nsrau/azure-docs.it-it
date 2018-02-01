---
title: Procedure consigliate di gestione degli errori per i client di Azure Active Directory Authentication Library (ADAL)
description: Fornisce linee guida e procedure consigliate per la gestione degli errori per le applicazioni client ADAL.
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mtillman
ms.author: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.custom: 
ms.openlocfilehash: 275ab65569a1861f046c8ee77914e0859d41d5f7
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Procedure consigliate di gestione degli errori per i client di Azure Active Directory Authentication Library (ADAL)

Questo articolo fornisce indicazioni sul tipo di errori che gli sviluppatori possono riscontrare durante l'uso di ADAL per autenticare gli utenti. Quando si usa ADAL, in vari casi uno sviluppatore potrebbe avere la necessità di analizzare e gestire situazioni di errore. Una gestione degli errori corretta garantisce un'esperienza ottimale per l'utente finale e limita il numero di volte in cui che l'utente finale deve eseguire l'accesso.

In questo articolo vengono esplorati i casi specifici per ogni piattaforma supportata da ADAL e il modo in cui un'applicazione può gestire ogni caso correttamente. Queste linee guida sugli errori sono suddivise in due categorie principali, in base ai modelli di acquisizione dei token forniti dalle API ADAL:

- **AcquireTokenSilent**: il client tenta di ottenere un token in modo invisibile all'utente (senza interfaccia utente) e può verificarsi un errore in caso di esito negativo di ADAL. 
- **AcquireToken**: il client può tentare l'acquisizione invisibile all'utente, ma anche eseguire richieste interattive che richiedono l'accesso.

> [!TIP]
> È consigliabile registrare tutti gli errori e tutte le eccezioni quando si usa ADAL e Azure AD. I log non sono utili solo per comprendere lo stato di integrità generale dell'applicazione, ma sono anche importanti durante il debug di problemi più estesi. Anche se l'applicazione potrebbe recuperare da determinati errori, questi potrebbero essere indicativi di problemi di progettazione di più ampio spettro che richiedono modifiche al codice per la risoluzione. 
> 
> Per l'implementazione delle condizioni di errore trattate in questo documento, è consigliabile registrare il codice di errore e la descrizione per i motivi illustrati in precedenza. Vedere [Informazioni di riferimento su errori e registrazione](#error-and-logging-reference) per esempi di codice di registrazione. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent tenta di ottenere un token con la garanzia che l'utente finale non visualizzi un'interfaccia utente. Esistono vari casi in cui l'acquisizione invisibile all'utente può avere esito negativo e deve essere gestita tramite richieste interattive o da un gestore predefinito. Nelle sezioni che seguono verranno fornite informazioni specifiche su quando e come impiegare ogni caso.

Esiste un set di errori generati dal sistema operativo, che potrebbe richiedere una gestione degli errori specifica per l'applicazione. Per altre informazioni, vedere la sezione "Errori del sistema operativo" in [Informazioni di riferimento su errori e registrazione](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenari applicativi

- Applicazioni [client native](active-directory-dev-glossary.md#native-client) (iOS, Android, desktop .NET o Xamarin)
- Applicazioni [client Web](active-directory-dev-glossary.md#web-client) che chiamano una [risorsa](active-directory-dev-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Casi di errore e azioni implementabili

Fondamentalmente, esistono due casi di errori AcquireTokenSilent:

| Caso | Descrizione |
|------|-------------|
| **Caso 1**: l'errore è risolvibile con un accesso interattivo | Per gli errori causati dalla mancanza di token validi, è necessaria una richiesta interattiva. In particolare, per la risoluzione di ricerche nella cache e token di aggiornamento non validi/scaduti è necessaria una chiamata di AcquireToken.<br><br>In questi casi, occorre chiedere all'utente finale di eseguire l'accesso. L'applicazione può scegliere di effettuare una richiesta interattiva immediatamente dopo l'interazione con l'utente finale (ad esempio, l'uso di un pulsante di accesso) o in seguito. La scelta dipende dal comportamento desiderato dell'applicazione.<br><br>Vedere il codice nella sezione seguente per questo caso specifico e gli errori per diagnosticarlo.|
| **Caso 2**: l'errore non è risolvibile con un accesso interattivo | Per la rete e gli errori temporanei o altri malfunzionamenti, l'esecuzione di una richiesta AcquireToken interattiva non risolve il problema. Le richieste di accesso interattivo non necessarie possono anche risultare frustranti per gli utenti finali. ADAL esegue automaticamente solo un ulteriore tentativo per la maggior parte degli errori AcquireTokenSilent.<br><br>L'applicazione client può anche eseguire un altro tentativo in seguito, ma quando e come eseguirlo dipende dal comportamento dell'applicazione e dall'esperienza desiderata per gli utenti finali. Ad esempio, l'applicazione può eseguire un nuovo tentativo di chiamata di AcquireTokenSilent dopo alcuni minuti oppure in risposta a un'azione dell'utente finale. Un tentativo immediato causerebbe la limitazione dell'applicazione ed è sconsigliato.<br><br>Un tentativo successivo che genera lo stesso errore non significa che il client deve eseguire una richiesta interattiva tramite AcquireToken, perché non risolverebbe l'errore.<br><br>Vedere il codice nella sezione seguente per questo caso specifico e gli errori per diagnosticarlo. |

### <a name="net"></a>.NET

Le linee guida seguenti offrono esempi per la gestione degli errori in combinazione con i metodi ADAL: 

- acquireTokenSilentAsync(…)
- acquireTokenSilentSync(…) 
- [deprecato] acquireTokenSilent(…)
- [deprecato] acquireTokenByRefreshToken(…) 

Il codice verrebbe implementato come segue:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request.  
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

Le linee guida seguenti offrono esempi per la gestione degli errori in combinazione con i metodi ADAL: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- [deprecato] acquireTokenSilent(…)

Il codice verrebbe implementato come segue:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

Le linee guida seguenti offrono esempi per la gestione degli errori in combinazione con i metodi ADAL: 

- acquireTokenSilentWithResource(…)

Il codice verrebbe implementato come segue:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application.  

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken è il metodo ADAL predefinito usato per ottenere i token. Nei casi in cui è richiesta l'identità dell'utente, AcquireToken tenta prima di tutto di ottenere un token in modo invisibile all'utente, quindi visualizza l'interfaccia utente se necessario (a meno che non venga passato PromptBehavior.Never). Nei casi in cui è richiesta l'identità dell'applicazione, AcquireToken tenta di ottenere un token, ma non visualizza l'interfaccia utente perché non esiste alcun utente finale.  

Per la gestione degli errori AcquireToken, la gestione degli errori dipende dalla piattaforma e dallo scenario che l'applicazione sta tentando di realizzare.  

Anche il sistema operativo può generare un set di errori e ciò richiede una gestione degli errori dipendente dall'applicazione specifica. Per altre informazioni, vedere "Errori del sistema operativo" in [Informazioni di riferimento su errori e registrazione](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenari applicativi

- Applicazioni client native (iOS, Android, desktop .NET o Xamarin)
- Applicazioni Web che chiamano un'API di risorse (.NET)
- Applicazioni a pagina singola (JavaScript)
- Applicazioni da servizio a servizio (.NET, Java)
  - Tutti gli scenari, tra cui per-conto-di
  - Scenari specifici per-conto-di

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Casi di errore e azioni implementabili: applicazioni client native

Se si sta creando un'applicazione client nativa, esistono alcuni casi di gestione degli errori da prendere in considerazione che riguardano i problemi di rete, gli errori temporanei e altri errori specifici della piattaforma. Nella maggior parte dei casi, un'applicazione non deve eseguire nuovi tentativi immediati, ma attendere invece l'interazione con l'utente finale che richiede un accesso.  

Esistono alcuni casi speciali in cui un singolo tentativo potrebbe risolvere il problema. Ad esempio, quando un utente deve abilitare i dati in un dispositivo o ha completato il download del broker di Azure AD dopo l'errore iniziale. 

In caso di errore, un'applicazione può presentare un'interfaccia utente per consentire all'utente finale di eseguire una forma di interazione che richiede un nuovo tentativo. Ad esempio, in caso di problemi con il dispositivo a causa di un errore offline, un pulsante "Ritenta l'accesso", che richiede un nuovo tentativo di AcquireToken anziché ritentare immediatamente l'operazione in errore. 

La gestione degli errori nelle applicazioni native può essere definita da due casi:

|  |  |
|------|-------------|
| **Caso 1**:<br>Errore irreversibile (la maggior parte dei casi) | 1. Non eseguire immediatamente un nuovo tentativo. Presentare l'interfaccia utente all'utente finale in base all'errore specifico che richiama un nuovo tentativo ("Ritenta l'accesso", "Scarica applicazione broker Azure AD" e così via). |
| **Caso 2**:<br>Errore non irreversibile | 1. Eseguire un singolo tentativo perché l'utente finale potrebbe trovarsi in uno stato che consente l'esito positivo.<br><br>2. Se il tentativo ha esito negativo, presentare l'interfaccia utente all'utente finale in base all'errore specifico che richiama un nuovo tentativo ("Ritenta l'accesso", "Scarica applicazione broker Azure AD" e così via). |

> [!IMPORTANT]
> Se un account utente viene passato ad ADAL in una chiamata invisibile all'utente con esito negativo, la richiesta interattiva successiva consente all'utente finale di eseguire l'accesso con un account diverso. Dopo una corretta chiamata di AcquireToken con un account utente, l'applicazione deve verificare che l'utente connesso corrisponda all'oggetto utente locale dell'applicazione. Una mancata corrispondenza non genera un'eccezione (tranne che in Objective C), ma deve essere presa in considerazione nei casi in cui un utente è noto in locale prima delle richieste di autenticazione (ad esempio una chiamata invisibile all'utente non riuscita).
>

#### <a name="net"></a>.NET

Le linee guida seguenti offrono esempi per la gestione degli errori in combinazione con tutti i metodi ADAL AcquireToken(…) non invisibili all'utente, *tranne*: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion,…)   

Il codice verrebbe implementato come segue:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> Per ADAL .NET esiste un altro aspetto da considerare, perché supporta PromptBehavior.Never, che ha un comportamento simile ad AcquireTokenSilent.
>

Le linee guida seguenti offrono esempi per la gestione degli errori in combinazione con i metodi ADAL: 

- acquireToken(…, PromptBehavior.Never)

Il codice verrebbe implementato come segue:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Le linee guida seguenti offrono esempi per la gestione degli errori in combinazione con tutti i metodi ADAL AcquireToken(…) non invisibili all'utente. 

Il codice verrebbe implementato come segue:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Le linee guida seguenti offrono esempi per la gestione degli errori in combinazione con tutti i metodi ADAL AcquireToken(…) non invisibili all'utente. 

Il codice verrebbe implementato come segue:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Casi di errore e azioni implementabili: applicazioni Web che chiamano una risorsa API (.NET)

Se si sta creando un'app Web .NET che ottiene un token usando codice di autorizzazione per una risorsa, l'unico codice richiesto è un gestore predefinito per il caso generico. 

Le linee guida seguenti offrono esempi per la gestione degli errori in combinazione con i metodi ADAL: 

- AcquireTokenByAuthorizationCodeAsync(…)

Il codice verrebbe implementato come segue:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Casi di errore e azioni implementabili: applicazioni a pagina singola (adal.js)

Se si sta creando un'applicazione a pagina singola con adal.js e AcquireToken, il codice di gestione degli errori è simile a quello di una tipica chiamata invisibile all'utente.  In particolare in adal.js, AcquireToken non visualizza mai un'interfaccia utente. 

Per una chiamata AcquireToken non riuscita esistono i casi seguenti:

|  |  |
|------|-------------|
| **Caso 1**:<br>Risolvibile con una richiesta interattiva | 1. In caso di esito negativo di login(), non eseguire immediatamente un nuovo tentativo. Ripetere solo dopo la richiesta di un nuovo tentativo con un'azione dell'utente.|
| **Caso 2**:<br>Non risolvibile con una richiesta interattiva. L'errore non è irreversibile. | 1. Eseguire un singolo tentativo perché l'utente finale potrebbe trovarsi in uno stato che consente l'esito positivo.<br><br>2. Se il tentativo ha esito negativo, presentare all'utente finale un'azione basata sull'errore specifico che può richiamare un nuovo tentativo ("Ritenta l'accesso"). |
| **Caso 3**:<br>Non risolvibile con una richiesta interattiva. L'errore è irreversibile. | 1. Non eseguire immediatamente un nuovo tentativo. Presentare all'utente finale un'azione basata sull'errore specifico che può richiamare un nuovo tentativo ("Ritenta l'accesso"). |

Il codice verrebbe implementato come segue:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Casi di errore e azioni implementabili: applicazioni da servizio a servizio (solo .NET)

Se si sta creando un'applicazione da servizio a servizio che usa AcquireToken, esistono alcuni errori chiave che devono essere gestiti dal codice. L'unica soluzione è restituire l'errore all'app chiamante (per i casi per-conto-di) o applicare una strategia di ripetizione dei tentativi. 

#### <a name="all-scenarios"></a>Tutti gli scenari

Per *tutti* gli scenari di applicazioni da servizio a servizio, incluso lo scenario per-conto-di:

- Non eseguire immediatamente un nuovo tentativo. ADAL esegue un solo ulteriore tentativo per determinate richieste non riuscite. 
- Continuare con nuovi tentativi solo quando richiesto dall'azione di un utente o un'app. Ad esempio, un'applicazione daemon che opera in base a un intervallo prestabilito deve attendere l'intervallo successivo per ripetere il tentativo.

Le linee guida seguenti offrono esempi per la gestione degli errori in combinazione con i metodi ADAL: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion, …)

Il codice verrebbe implementato come segue:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>Scenari per-conto-di

Per gli scenari di applicazioni da servizi a servizio *per-conto-di*.

Le linee guida seguenti offrono esempi per la gestione degli errori in combinazione con i metodi ADAL: 

- AcquireTokenAsync(…, UserAssertion, …)

Il codice verrebbe implementato come segue:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

È disponibile un [esempio completo](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) che illustra questo scenario.

## <a name="error-and-logging-reference"></a>Informazioni di riferimento su errori e registrazione

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Errori della libreria ADAL

Per esplorare errori ADAL specifici, la fonte di informazioni di riferimento ideale è il codice sorgente nel [repository azure-activedirectory-library-for-dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58).

#### <a name="guidance-for-error-logging-code"></a>Linee guida per il codice di registrazione degli errori

Il processo di registrazione per ADAL .NET cambia a seconda della piattaforma in uso. Vedere la [documentazione sulla registrazione](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet#diagnostics) per esempi di codice per l'abilitazione della registrazione.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Errori della libreria ADAL

Per esplorare errori ADAL specifici, la fonte di informazioni di riferimento ideale è il codice sorgente nel [repository azure-activedirectory-library-for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33).

#### <a name="operating-system-errors"></a>Errori del sistema operativo

Gli errori del sistema operativo Android vengono esposti tramite AuthenticationException in ADAL, sono identificabili come "SERVER_INVALID_REQUEST" e possono essere ulteriormente dettagliati tramite le descrizioni degli errori. I due messaggi principali che un'app può scegliere di visualizzare nell'interfaccia utente sono:

- Errori SSL 
  - [End user is using Chrome 53](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/SSL-Certificate-Validation-Issue) (L'utente finale usa Chrome 53)
  - [Cert Chain has a cert marked as extra download (user needs to contact IT Admin)](https://vkbexternal.partners.extranet.microsoft.com/VKBWebService/ViewContent.aspx?scid=KB;EN-US;3203929) (Nella catena di certificati un certificato è contrassegnato come download aggiuntivo e l'utente deve contattare l'amministratore IT)
  - L'autorità di certificazione radice non è considerata attendibile dal dispositivo. Contattare l'amministratore IT. 
- Errori correlati alla rete 
  - [Problema di rete potenzialmente correlato alla convalida del certificato SSL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/SSL-Certificate-Validation-Issue), può eseguire un singolo tentativo

#### <a name="guidance-for-error-logging-code"></a>Linee guida per il codice di registrazione degli errori

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode.     
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>Errori della libreria ADAL

Per esplorare errori ADAL specifici, la fonte di informazioni di riferimento ideale è il codice sorgente nel [repository azure-activedirectory-library-for-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295).

#### <a name="operating-system-errors"></a>Errori del sistema operativo

Gli errori di iOS possono verificarsi durante l'accesso quando gli utenti usano visualizzazioni Web e in base al tipo di autenticazione. Questa situazione può essere causata da condizioni come errori SSL, timeout o errori di rete:

- Per la condivisione dei diritti, gli account di accesso non sono persistenti e la cache sembra vuota. È possibile risolvere il problema aggiungendo la riga di codice seguente al keychain:`[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Per il set di errori NsUrlDomain, l'azione cambia in base alla logica dell'app. Vedere la [documentazione di riferimento su NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) per informazioni sulle istanze specifiche che possono essere gestite.
- Vedere [ADAL Obj-C Common Issues](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) (Problemi comuni di Obj-C ADAL) per un elenco degli errori comuni gestito dal team di Objective-C ADAL.

#### <a name="guidance-for-error-logging-code"></a>Linee guida per il codice di registrazione degli errori

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Linee guida per il codice di registrazione degli errori - JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```
## <a name="related-content"></a>Contenuti correlati

* [Guida per sviluppatori di Azure AD][AAD-Dev-Guide]
* [Azure AD Authentication Libraries][AAD-Auth-Libraries] (Librerie di autenticazione di Azure AD)
* [Scenari di autenticazione per Azure AD][AAD-Auth-Scenarios]
* [Integrazione di applicazioni con Azure Active Directory][AAD-Integrating-Apps]

Usare la sezione dei commenti di seguito per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

[![Pulsante Accedi][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->
[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

