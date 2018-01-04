---
title: Gestione degli errori le procedure consigliate per i client di Azure Active Directory Authentication Library (ADAL)
description: Fornisce informazioni e procedure consigliate per le applicazioni client ADAL di gestione degli errori.
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
ms.openlocfilehash: b6cf7bbb1ae41fcdf16601af87ec1b573866639a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Gestione degli errori le procedure consigliate per i client di Azure Active Directory Authentication Library (ADAL)

Questo articolo fornisce informazioni aggiuntive sul tipo di errori che gli sviluppatori possono verificarsi quando si usa ADAL per autenticare gli utenti. Quando si usa ADAL, sono disponibili vari casi in cui uno sviluppatore potrebbe essere necessario intervenire e gestire gli errori. Gestione degli errori corretta garantisce un'esperienza ottimale per l'utente finale e limita il numero di volte in cui che l'utente finale deve eseguire l'accesso.

In questo articolo, è esplorare i case specifici per ogni piattaforma supportata dal ADAL, come l'applicazione può gestire correttamente ogni case. Le indicazioni di errore viene suddivisa in due categorie più ampie, in base agli schemi di acquisizione del token forniti dalle API ADAL:

- **AcquireTokenSilent**: Client tenta di ottenere un token invisibile all'utente (non dell'interfaccia utente) e potrebbero non riuscire se ADAL ha esito negativo. 
- **AcquireToken**: Client può tentare di acquisizione invisibile all'utente, ma è anche possibile eseguire richieste interattive che richiedono l'accesso.

> [!TIP]
> È consigliabile accedere a tutti gli errori e le eccezioni quando si usa ADAL e Azure AD. Log non solo sono utili per comprendere l'integrità generale dell'applicazione, ma sono anche importanti durante il debug di problemi più ampi. Mentre l'applicazione potrebbe risolvere alcuni errori, essi possono Accennare al più ampi problemi di progettazione che richiedono modifiche al codice per risolvere. 
> 
> Quando le condizioni di errore di implementazione trattate in questo documento, è necessario registrare il codice di errore e la descrizione per i motivi illustrati in precedenza. Vedere il [errore e riferimento di registrazione](#error-and-logging-reference) per esempi di codice di registrazione. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent tenta di ottenere un token con la garanzia che l'utente finale non rileva un'interfaccia utente (UI). Esistono vari casi in cui non riesca acquisizione invisibile all'utente e deve essere gestito tramite richieste interattive o da un gestore predefinito. Approfondiremo le specifiche dei quando e come utilizzare ogni case nelle sezioni che seguono.

È un set di errori generati dal sistema operativo, che potrebbe richiedere specifiche dell'applicazione di gestione degli errori. Per ulteriori informazioni, vedere la sezione relativa agli errori "Sistema operativo" in [errore e riferimento di registrazione](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenari applicativi

- [Native client](active-directory-dev-glossary.md#native-client) applicazioni (iOS, Android, Desktop .NET o Xamarin)
- [Client Web](active-directory-dev-glossary.md#web-client) le applicazioni che chiamano un [risorse](active-directory-dev-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Casi di errore e i passaggi utilizzabili

Fondamentalmente, esistono due casi di errori AcquireTokenSilent:

| Caso | DESCRIZIONE |
|------|-------------|
| **Caso 1**: errore sia risolvibile con un accesso interattivo | Per gli errori causati dalla mancanza di un token valido, è necessaria una richiesta interattiva. In particolare, la ricerca nella cache e un token di aggiornamento non valido/scaduto richiedono una chiamata di AcquireToken per risolvere.<br><br>In questi casi, l'utente finale deve essere richiesto di accedere. L'applicazione è possibile scegliere di effettuare una richiesta interattiva immediatamente dopo l'interazione dell'utente finale (ad esempio, l'utilizzo di un pulsante di accesso) o versione successiva. La scelta dipende dal comportamento desiderato dell'applicazione.<br><br>Vedere il codice nella sezione seguente per questo caso specifico e gli errori diagnosticarlo.|
| **Caso 2**: errore non è risolvibile con un accesso interattivo | Per la rete e gli errori temporanei/temporaneo o altri errori, l'esecuzione di una richiesta AcquireToken interattiva non risolvere il problema. Non necessari interactive sign-in richiede inoltre possono risultare frustrante per gli utenti finali. ADAL tenta automaticamente di un singolo nuovo tentativo per la maggior parte degli errori per gli errori AcquireTokenSilent.<br><br>L'applicazione client può inoltre tentare un nuovo tentativo in seguito, ma quando e come eseguire questa operazione dipende il comportamento dell'applicazione e l'esperienza dell'utente finale desiderata. Ad esempio, l'applicazione può eseguire un nuovo tentativo AcquireTokenSilent dopo alcuni minuti, o in risposta a un'azione dell'utente finale. Un tentativo immediato comporterà l'applicazione viene limitato e non deve essere eseguito.<br><br>Un errore con lo stesso errore di ripetizione successivi non significa che il client deve eseguire una richiesta interattiva tramite AcquireToken, poiché non viene risolto l'errore.<br><br>Vedere il codice nella sezione seguente per questo caso specifico e gli errori diagnosticarlo. |

### <a name="net"></a>.NET

Le linee guida seguenti vengono forniti esempi per la gestione degli errori in combinazione con metodi ADAL: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- acquireTokenSilent(...) [obsoleto]
- acquireTokenByRefreshToken(...) [obsoleto] 

Il codice potrebbe essere implementato come segue:

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

Le linee guida seguenti vengono forniti esempi per la gestione degli errori in combinazione con metodi ADAL: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- acquireTokenSilent(...) [obsoleto]

Il codice potrebbe essere implementato come segue:

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

Le linee guida seguenti vengono forniti esempi per la gestione degli errori in combinazione con metodi ADAL: 

- acquireTokenSilentWithResource(...)

Il codice potrebbe essere implementato come segue:

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

AcquireToken è il metodo ADAL predefinito utilizzato per ottenere i token. Nei casi in cui identità utente è obbligatorio, AcquireToken tenta di ottenere un token automaticamente prima, quindi visualizza un'interfaccia utente, se necessario (a meno che non viene passato PromptBehavior.Never). Nei casi in cui è richiesta l'identità dell'applicazione, AcquireToken tenta di ottenere un token, ma non visualizzare l'interfaccia utente perché non esiste alcun utente finale.  

Gestione degli errori AcquireToken la gestione degli errori dipende dalla piattaforma e scenario, l'applicazione sta tentando di raggiungere.  

Il sistema operativo è anche possibile generare un set di errori, che richiedono la dipende l'applicazione specifica di gestione degli errori. Per ulteriori informazioni, vedere "Errori di sistema operativo" in [errore e riferimento di registrazione](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scenari applicativi

- Applicazioni client native (iOS, Android, Desktop .NET o Xamarin)
- Applicazioni Web che chiamano una risorsa API (.NET)
- Applicazioni a pagina singola (JavaScript)
- Applicazioni di servizio-servizio (.NET, Java)
  - Tutti gli scenari, tra cui on-behalf-of
  - On-Behalf-of scenari specifici

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Casi di errore e i passaggi utilizzabili: le applicazioni client Native

Se si sta creando un'applicazione client nativa, esistono alcuni casi di gestione degli errori da prendere in considerazione che riguardano i problemi di rete, errori temporanei e altri errori specifici della piattaforma. Nella maggior parte dei casi, un'applicazione non deve eseguire tentativi immediati, ma anziché attendere l'interazione dell'utente finale che richiede un accesso aggiuntivo.  

Esistono alcuni casi speciali in cui un singolo tentativo potrebbe risolvere il problema. Ad esempio, quando un utente è necessario abilitare i dati in un dispositivo o completato il broker di Azure AD Scarica dopo l'errore iniziale. 

In caso di errore, un'applicazione può presentare un'interfaccia utente per consentire all'utente finale eseguire l'interazione di un tipo che richiede un nuovo tentativo. Ad esempio, se il dispositivo non riuscito per un errore non in linea, un pulsante "Tenta di accedere di nuovo", che richiede un AcquireToken ripetere anziché ritentare immediatamente l'errore. 

Gestione degli errori in applicazioni native possono essere definiti in due casi:

|  |  |
|------|-------------|
| **Caso 1**:<br>Errore irreversibile (la maggior parte dei casi) | 1. Non tentare di tentativi immediati. Presentare all'utente finale che dell'interfaccia utente in base all'errore specifico che richiama un nuovo tentativo ("Tenta nuovamente di accesso", "Applicazione di Service broker Scarica Azure AD" e così via). |
| **Caso 2**:<br>Errore non irreversibile | 1. Eseguire un singolo tentativo perché l'utente finale potrebbe aver immesso uno stato che restituisce un esito positivo.<br><br>2. Se il tentativo ha esito negativo, presentare all'utente finale che dell'interfaccia utente in base all'errore specifico che richiama un nuovo tentativo ("Tenta nuovamente di accesso", "App del broker Scarica Azure AD" e così via). |

> [!IMPORTANT]
> Se un account utente viene passato ad ADAL in una chiamata invisibile all'utente e ha esito negativo, la richiesta successiva interattiva consente all'utente finale di accedere utilizzando un account diverso. Dopo una corretta AcquireToken utilizzando un account utente, l'applicazione deve verificare che l'utente connesso corrisponda all'oggetto utente locale dell'applicazione. Una mancata corrispondenza non genera un'eccezione (tranne che in Objective C), ma devono essere considerate nei casi in cui un utente è noto in locale prima le richieste di autenticazione (ad esempio una chiamata invisibile all'utente non riuscita).
>

#### <a name="net"></a>.NET

Le linee guida seguenti vengono forniti esempi per l'insieme a tutti AcquireToken(...) non invisibile all'utente di gestione degli errori Metodi di ADAL, *tranne*: 

- AcquireTokenAsync (..., IClientAssertionCertification,...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync(...,UserAssertion,...)   

Il codice potrebbe essere implementato come segue:

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
> ADAL .NET è un fattore aggiuntivo in quanto supporta PromptBehavior.Never, che ha un comportamento simile AcquireTokenSilent.
>

Le linee guida seguenti vengono forniti esempi per la gestione degli errori in combinazione con metodi ADAL: 

- acquireToken(..., PromptBehavior.Never)

Il codice potrebbe essere implementato come segue:

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

Le linee guida seguenti vengono forniti esempi per l'insieme a tutti AcquireToken(...) non invisibile all'utente di gestione degli errori Metodi di ADAL. 

Il codice potrebbe essere implementato come segue:

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

Le linee guida seguenti vengono forniti esempi per l'insieme a tutti AcquireToken(...) non invisibile all'utente di gestione degli errori Metodi di ADAL. 

Il codice potrebbe essere implementato come segue:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Casi di errore e i passaggi utilizzabili: le applicazioni Web che chiama una risorsa API (.NET)

Se si sta creando un'app web .NET che chiama Ottiene un token utilizzando un codice di autorizzazione per una risorsa, l'unico codice richiesto è un gestore predefinito per il case generico. 

Le linee guida seguenti vengono forniti esempi per la gestione degli errori in combinazione con metodi ADAL: 

- AcquireTokenByAuthorizationCodeAsync(...)

Il codice potrebbe essere implementato come segue:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Casi di errore e i passaggi utilizzabili: Single Page Applications (adal.js)

Se si sta creando un'applicazione a pagina singola con adal.js AcquireToken, il codice di gestione degli errori è simile a quello di una tipica chiamata invisibile all'utente.  In particolare in adal.js, AcquireToken evitare di visualizzare un'interfaccia utente. 

Un AcquireToken non riuscita con i casi seguenti:

|  |  |
|------|-------------|
| **Caso 1**:<br>Risolvibile con una richiesta interattiva | 1. Se login () non riesce, non eseguire tentativi immediati. Ripetere solo dopo l'azione dell'utente viene richiesto un nuovo tentativo.|
| **Caso 2**:<br>Non Resolvable con una richiesta interattiva. Errore è irreversibile. | 1. Eseguire un singolo tentativo l'utente finale principale sia immesso uno stato che restituisce un esito positivo.<br><br>2. Se il tentativo ha esito negativo, presentare all'utente finale con un'azione in base all'errore specifico che può richiamare un nuovo tentativo ("tentativo di accedere di nuovo"). |
| **Caso 3**:<br>Non Resolvable con una richiesta interattiva. Errore non riproducibile. | 1. Non tentare di tentativi immediati. Presentare all'utente finale con un'azione in base all'errore specifico che può richiamare un nuovo tentativo ("tentativo di accedere di nuovo"). |

Il codice potrebbe essere implementato come segue:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Casi di errore e i passaggi utilizzabili: applicazioni di servizio-servizio (solo .NET)

Se si sta creando un'applicazione di servizio-servizio che utilizza AcquireToken, sono presenti alcuni errori di chiave che necessario gestire il codice. L'unica soluzione all'errore è restituito l'errore al chiamante app (per i casi di on-behalf-of) o applicare una strategia di ripetizione. 

#### <a name="all-scenarios"></a>Tutti gli scenari

Per *tutti* scenari di applicazione di servizio, tra cui on-behalf-of:

- Non tentare un tentativo immediato. Tentativi ADAL un singolo ripetere per alcune richieste non riuscite. 
- Continuare solo nuovo tentativo dopo un'azione utente o un'app richiede un nuovo tentativo. Ad esempio, un'applicazione daemon che alcuni intervalli di set di lavoro deve attendere il successivo intervallo di tentativi.

Le linee guida seguenti vengono forniti esempi per la gestione degli errori in combinazione con metodi ADAL: 

- AcquireTokenAsync (..., IClientAssertionCertification,...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync (..., UserAssertion,...)

Il codice potrebbe essere implementato come segue:

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

#### <a name="on-behalf-of-scenarios"></a>Scenari di on-behalf-of

Per *on-behalf-of* scenari di applicazione di servizio.

Le linee guida seguenti vengono forniti esempi per la gestione degli errori in combinazione con metodi ADAL: 

- AcquireTokenAsync (..., UserAssertion,...)

Il codice potrebbe essere implementato come segue:

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

È stato compilato un [esempio completo](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) che illustra questo scenario.

## <a name="error-and-logging-reference"></a>Riferimento a errori e registrazione

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Errori di libreria ADAL

Per esplorare ADAL errori specifici, il codice sorgente di [repository di azure-activedirectory-libreria-per-dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) riferimento errore migliore.

#### <a name="guidance-for-error-logging-code"></a>Linee guida per il codice di registrazione errore

La registrazione di ADAL .NET di modifiche a seconda della piattaforma si sta lavorando. Consultare la [registrazione documentazione](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet#diagnostics) per il codice necessario abilitare la registrazione.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Errori di libreria ADAL

Per esplorare ADAL errori specifici, il codice sorgente di [repository di azure-activedirectory-libreria-for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) riferimento errore migliore.

#### <a name="operating-system-errors"></a>Errori del sistema operativo

Errori del sistema operativo Android sono esposte tramite AuthenticationException in ADAL, identificati come "SERVER_INVALID_REQUEST" e possono essere ulteriormente granulari tramite le descrizioni degli errori. I due messaggi principali che un'app è possibile scegliere di visualizzare l'interfaccia utente sono:

- Errore SSL 
  - [Utente finale Usa 53 Chrome](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/SSL-Certificate-Validation-Issue)
  - [Catena di certificazione è un certificato contrassegnato come aggiuntivo scaricare (utente dovrà contattare l'amministratore IT)](https://vkbexternal.partners.extranet.microsoft.com/VKBWebService/ViewContent.aspx?scid=KB;EN-US;3203929)
  - Autorità di certificazione radice non è considerato attendibile dal dispositivo. Contattare l'amministratore IT. 
- Gli errori correlati di rete 
  - [Problema potenzialmente correlato per la convalida del certificato SSL di rete](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/SSL-Certificate-Validation-Issue), può tentare di ripetere singolo

#### <a name="guidance-for-error-logging-code"></a>Linee guida per il codice di registrazione errore

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

#### <a name="adal-library-errors"></a>Errori di libreria ADAL

Per esplorare ADAL errori specifici, il codice sorgente di [azure-activedirectory-libreria-per-objc repository](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) riferimento errore migliore.

#### <a name="operating-system-errors"></a>Errori del sistema operativo

errori di iOS possono verificarsi durante l'accesso quando gli utenti utilizzano visualizzazioni web e del tipo di autenticazione. Questo può essere causato da condizioni, ad esempio errori SSL, timeout o errori di rete:

- Per la condivisione di titoli, gli account di accesso non sono persistenti e la cache è vuota. È possibile risolvere aggiungendo la riga di codice seguente a keychain:`[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Per il set di NsUrlDomain di errori, l'azione di modifica in base alla logica di app. Vedere il [la documentazione di riferimento NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) per istanze specifiche che possono essere gestite.
- Vedere [problemi comuni di ADAL Obj-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) per un elenco di errori comuni di gestito dal team ADAL Objective-C.

#### <a name="guidance-for-error-logging-code"></a>Linee guida per il codice di registrazione errore

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
* [Librerie di autenticazione di Azure AD][AAD-Auth-Libraries]
* [Scenari di autenticazione di Azure AD][AAD-Auth-Scenarios]
* [Integrazione di applicazioni con Azure Active Directory][AAD-Integrating-Apps]

Utilizzare la sezione commenti di seguito, per fornire commenti e suggerimenti e consentono di ridefinire e definire il contenuto.

<!--Reference style links -->
[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[! [Accedi pulsante] [AAD-Accedi]] [AAD-Accedi] [AAD-Accedi]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

