---
title: Errori ed eccezioni (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come gestire gli errori e le eccezioni, l'accesso condizionale e le richieste di attestazione nelle applicazioni MSAL Android.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: f40c2bb0f529f9e0683c67bea884443458707f4f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206595"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Gestire eccezioni ed errori in MSAL per Android

Le eccezioni in Microsoft Authentication Library (MSAL) sono progettate per aiutare gli sviluppatori di app a risolvere i problemi relativi all'applicazione. I messaggi di eccezione non sono localizzati.

Durante l'elaborazione di eccezioni ed errori, è possibile usare il tipo di eccezione e il codice di errore per distinguere le eccezioni.  Per un elenco dei codici di errore, vedere [Codici di errore di autenticazione e autorizzazione](reference-aadsts-error-codes.md).

Durante l'esperienza di accesso, è possibile che si verifichino errori relativi a consenso, accesso condizionale (autenticazione a più fattori, gestione dei dispositivi, restrizioni basate sulla posizione), rilascio e riscatto di token e proprietà utente.


|Classe Error | Stringa di origine/errore| Come gestire |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: Il token di aggiornamento usato per riscattare il token di accesso non è valido, è scaduto o è stato revocato. Questa eccezione può essere dovuta a una modifica della password. </li><li>`NO_TOKENS_FOUND`: Il token di accesso non esiste e non è possibile trovare alcun token di aggiornamento per riscattare il token di accesso.</li> <li>Procedura dettagliata obbligatoria<ul><li>MFA</li><li>Attestazioni mancanti</li></ul></li><li>Bloccato dall'accesso condizionale (ad esempio, è richiesta l'installazione del [broker di autenticazione](./msal-android-single-sign-on.md) )</li><li>`NO_ACCOUNT_FOUND`: Nessun account disponibile nella cache per l'autenticazione invisibile all'utente.</li></ul> |Chiamare `acquireToken()` per richiedere all'utente di immettere il nome utente e la password e possibilmente il consenso ed eseguire l'autenticazione a più fattori.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: L'utente o il server non ha accettato tutti gli ambiti. Il server potrebbe rifiutare un ambito se l'ambito richiesto non è supportato, non riconosciuto o non supportato per un determinato account. </li></ul>| Lo sviluppatore deve decidere se continuare l'autenticazione con gli ambiti concessi o terminare il processo di autenticazione. Opzione per inviare nuovamente la richiesta del token di acquisizione solo per gli ambiti concessi e fornire suggerimenti per i quali sono state concesse le autorizzazioni passando `silentParametersForGrantedScopes` e chiamando `acquireTokenSilent` . |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: In questa richiesta manca un parametro obbligatorio, include un parametro non valido, include un parametro più di una volta o non è valido. </li><li>`SERVICE_NOT_AVAILABLE`: Rappresenta i codici di errore 500/503/506 a causa del mancato servizio. </li><li>`UNAUTHORIZED_REQUEST`: Il client non è autorizzato a richiedere un codice di autorizzazione.</li><li>`ACCESS_DENIED`: Il proprietario della risorsa o il server di autorizzazione ha negato la richiesta.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` convalida non riuscita</li><li>`UNKNOWN_ERROR`: La richiesta al server non è riuscita, ma non `error_description` è stato restituito alcun errore dal servizio.</li><ul>| Questa classe di eccezione rappresenta errori durante la comunicazione con il servizio, può provenire dagli endpoint di autorizzazione o token. MSAL legge l'errore e error_description dalla risposta del server. In genere, questi errori vengono risolti correggendo le configurazioni dell'app nel codice o nel portale di registrazione delle app. Raramente un'interruzione del servizio può attivare questo avviso, che può essere risolto solo attendendo il ripristino del servizio.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: Sono state trovate più voci della cache e l'SDK non è in grado di identificare il token di accesso o di aggiornamento corretto dalla cache. Questa eccezione in genere indica un bug nell'SDK per l'archiviazione dei token o che l'autorità non viene fornita nella richiesta invisibile all'utente e vengono trovati più token corrispondenti. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: Nessuna rete attiva disponibile sul dispositivo. </li><li>`JSON_PARSE_FAILURE`: L'SDK non è riuscito ad analizzare il formato JSON.</li><li>`IO_ERROR`: `IOException` si è verificato un errore di rete o di dispositivo. </li><li>`MALFORMED_URL`: Il formato dell'URL non è corretto. Probabilmente causato dalla creazione della richiesta di autenticazione, dell'autorità o dell'URI di reindirizzamento. </li><li>`UNSUPPORTED_ENCODING`: La codifica non è supportata dal dispositivo. </li><li>`NO_SUCH_ALGORITHM`: L'algoritmo utilizzato per generare la richiesta [PKCE](https://tools.ietf.org/html/rfc7636) non è supportato. </li><li>`INVALID_JWT`: `JWT` il valore restituito dal server non è valido o è vuoto o non valido. </li><li>`STATE_MISMATCH`: Lo stato della risposta di autorizzazione non corrisponde allo stato nella richiesta di autorizzazione. Per le richieste di autorizzazione, l'SDK verificherà lo stato restituito dal reindirizzamento e quello inviato nella richiesta. </li><li>`UNSUPPORTED_URL`: URL non supportato. Impossibile eseguire la convalida dell'autorità ADFS. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: L'autorità non è supportata per la convalida dell'autorità. L'SDK supporta le autorità B2C, ma non supporta la convalida dell'autorità B2C. Sarà supportato solo l'host noto. </li><li>`CHROME_NOT_INSTALLED`: Chrome non è installato nel dispositivo. L'SDK usa la scheda personalizzata Chrome per le richieste di autorizzazione, se disponibile, e verrà eseguito il fallback al browser Chrome. </li><li>`USER_MISMATCH`: L'utente specificato nella richiesta di acquisizione del token non corrisponde a quello restituito dal server.</li></ul>|Questa classe di eccezione rappresenta errori generali locali della libreria. Queste eccezioni possono essere gestite correggendo la richiesta.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: Il flusso interattivo avviato dall'utente e prima della ricezione dei token ha annullato la richiesta. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: È necessario specificare Authority per `acquireTokenSilent` .</li></ul>|Questi errori possono essere attenuati dallo sviluppatore che corregge gli argomenti e garantendo l'attività di autenticazione interattiva, callback di completamento, ambiti e un account con un ID valido.|


## <a name="catching-errors"></a>Rilevamento degli errori

Il frammento di codice seguente mostra un esempio di rilevamento degli errori in caso di chiamate invisibili `acquireToken` .

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [registrazione degli errori](./msal-logging.md?tabs=android)