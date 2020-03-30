---
title: Guida alla migrazione da ADAL a MSAL per Android Azure
description: Informazioni su come eseguire la migrazione dell'app Android Azure Active Directory Authentication Library (ADAL) a Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084047"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Guida alla migrazione da ADAL a MSAL per Android

Questo articolo evidenzia le modifiche che è necessario apportare per eseguire la migrazione di un'app che usa Azure Active Directory Authentication Library (ADAL) per usare microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Punti salienti della differenza

ADAL funziona con l'endpoint di Azure Active Directory v1.0. Microsoft Authentication Library (MSAL) funziona con la piattaforma di identità Microsoft, precedentemente nota come endpoint di Azure Active Directory 2.0. La piattaforma di identità Microsoft è diversa da Azure Active Directory v1.0 in quanto:The Microsoft identity platform differs from Azure Active Directory v1.0 in that it:

Supporto:
  - Identità dell'organizzazione (Azure Active Directory)Organizational Identity (Azure Active Directory)
  - Identità non dell'organizzazione, ad esempio Outlook.com, Xbox Live e così via
  - (Solo B2C) Accesso federato con Google, Facebook, Twitter e Amazon

- Gli standard sono compatibili con:
  - OAuth v2.0
  - OpenID Connect (OIDC)

L'API pubblica MSAL introduce importanti modifiche, tra cui:

- Un nuovo modello per l'accesso ai token:A new model for accessing tokens:
  - ADAL fornisce l'accesso `AuthenticationContext`ai token tramite il , che rappresenta il server. MSAL fornisce l'accesso `PublicClientApplication`ai token tramite il , che rappresenta il client. Gli sviluppatori client non devono `PublicClientApplication` creare una nuova istanza per ogni autorità con cui devono interagire. È `PublicClientApplication` necessaria una sola configurazione.
  - Supporto per la richiesta di token di accesso utilizzando ambiti oltre agli identificatori di risorsa.
  - Supporto per il consenso incrementale. Gli sviluppatori possono richiedere ambiti quando l'utente accede a sempre più funzionalità nell'app, incluse quelle non incluse durante la registrazione dell'app.
  - Le autorità non vengono più convalidate in fase di esecuzione. Invece, lo sviluppatore dichiara un elenco di 'autorità note' durante lo sviluppo.
- Modifiche all'API token:Token API changes:
  - In ADAL, `AcquireToken()` prima effettua una richiesta invisibile all'utente. In caso contrario, effettua una richiesta interattiva. Questo comportamento ha portato alcuni `AcquireToken`sviluppatori a basarsi solo su , che ha causato all'utente viene richiesto in modo imprevisto le credenziali, a volte. MSAL richiede che gli sviluppatori siano intenzionali quando l'utente riceve un prompt dell'interfaccia utente.
    - `AcquireTokenSilent`genera sempre una richiesta invisibile all'utente che ha esito positivo o negativo.
    - `AcquireToken`restituisce sempre una richiesta che richiede all'utente tramite l'interfaccia utente.
- MSAL supporta l'accesso da un browser predefinito o da una visualizzazione Web incorporata:
  - Per impostazione predefinita, viene utilizzato il browser predefinito sul dispositivo. Ciò consente a MSAL di utilizzare lo stato di autenticazione (cookie) che potrebbe essere già presente per uno o più account connessi. Se non è presente alcuno stato di autenticazione, l'autenticazione durante l'autorizzazione tramite MSAL comporta la creazione dello stato di autenticazione (cookie) a vantaggio di altre applicazioni Web che verranno utilizzate nello stesso browser.
- Nuovo modello di eccezione:
  - Le eccezioni definiscono più chiaramente il tipo di errore che si è verificato e le operazioni che lo sviluppatore deve eseguire per risolverlo.
- MSAL supporta gli `AcquireToken` oggetti `AcquireTokenSilent` parametro per e le chiamate.
- MSAL supporta la configurazione dichiarativa per:
  - ID client, URI di reindirizzamento.
  - Browser incorporato e predefinito
  - Autorità
  - Impostazioni HTTP, ad esempio timeout di lettura e connessione

## <a name="your-app-registration-and-migration-to-msal"></a>Registrazione e migrazione dell'app a MSAL

Non è necessario modificare la registrazione dell'app esistente per usare MSAL. Se si desidera sfruttare il consenso incrementale/progressivo, potrebbe essere necessario esaminare la registrazione per identificare gli ambiti specifici che si desidera richiedere in modo incrementale. Di seguito sono necessarie ulteriori informazioni sugli ambiti e sul consenso incrementale.

Nella registrazione dell'app nel portale verrà visualizzata una scheda **Autorizzazioni API.** In questo modo viene fornito un elenco delle API e delle autorizzazioni (ambiti) a cui l'app è attualmente configurata per richiedere l'accesso. Viene inoltre visualizzato un elenco dei nomi di ambito associati a ogni autorizzazione API.

### <a name="user-consent"></a>Consenso dell'utente

Con ADAL e l'endpoint AAD v1, il consenso dell'utente alle risorse di cui è proprietario è stato concesso al primo utilizzo. Con MSAL e la piattaforma di identità Microsoft, il consenso può essere richiesto in modo incrementale. Il consenso incrementale è utile per le autorizzazioni che un utente può considerare privilegi elevati o può altrimenti mettere in dubbio se non viene fornito con una spiegazione chiara del motivo per cui è richiesta l'autorizzazione. In ADAL, tali autorizzazioni potrebbero aver portato l'utente ad abbandonare l'accesso all'app.

> [!TIP]
> Ti consigliamo l'uso del consenso incrementale negli scenari in cui devi fornire un contesto aggiuntivo all'utente sul motivo per cui la tua app necessita di un'autorizzazione.

### <a name="admin-consent"></a>Consenso dell'amministratore

Gli amministratori dell'organizzazione possono concedere le autorizzazioni necessarie per l'applicazione per conto di tutti i membri dell'organizzazione. Alcune organizzazioni consentono solo agli amministratori di acconsentire alle applicazioni. Il consenso dell'amministratore richiede l'inclusione di tutte le autorizzazioni e gli ambiti dell'API usati dall'applicazione nella registrazione dell'app.

> [!TIP]
> Anche se puoi richiedere un ambito usando MSAL per un elemento non incluso nella registrazione dell'app, ti consigliamo di aggiornare la registrazione dell'app per includere tutte le risorse e gli ambiti a cui un utente potrebbe mai concedere l'autorizzazione.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migrazione da ID risorsa a ambitiMigrating from resource IDs to scopes

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Autenticare e richiedere l'autorizzazione per tutte le autorizzazioni al primo utilizzoAuthenticate and request authorization for all permissions on first use

Se attualmente si utilizza ADAL e non è necessario utilizzare il consenso incrementale, il `acquireToken` modo più `AcquireTokenParameter` semplice per iniziare a utilizzare MSAL consiste nel effettuare una richiesta utilizzando il nuovo oggetto e impostare il valore dell'ID risorsa.

> [!CAUTION]
> Non è possibile impostare sia gli ambiti che un ID risorsa. Se si tenta di impostare entrambi, verrà creato un `IllegalArgumentException`file .

 Ciò comporterà lo stesso comportamento v1 utilizzato. Tutte le autorizzazioni richieste nella registrazione dell'app vengono richieste all'utente durante la prima interazione.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Autentica e richiedi le autorizzazioni solo se necessario

Per sfruttare i vantaggi del consenso incrementale, crea un elenco di autorizzazioni (ambiti) che l'app usa dalla registrazione dell'app e organizzale in due elenchi in base a:

- Ambiti da richiedere durante la prima interazione dell'utente con l'app durante l'accesso.
- Le autorizzazioni associate a una funzionalità importante dell'app che dovrai spiegare anche all'utente.

Dopo aver organizzato gli ambiti, organizzare ogni elenco in base alla risorsa (API) per cui si vuole richiedere un token. Così come tutti gli altri ambiti che si desidera che l'utente a autorizzare contemporaneamente.

L'oggetto parameters utilizzato per effettuare la richiesta a MSAL supporta:

- `Scope`: elenco di ambiti per i quali si desidera richiedere l'autorizzazione e ricevere un token di accesso.
- `ExtraScopesToConsent`: un elenco aggiuntivo di ambiti per cui si desidera richiedere l'autorizzazione mentre si richiede un token di accesso per un'altra risorsa. Questo elenco di ambiti consente di ridurre al minimo il numero di volte in cui è necessario richiedere l'autorizzazione dell'utente. Il che significa meno autorizzazione dell'utente o richieste di consenso.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Eseguire la migrazione da AuthenticationContext a PublicClientApplicationsMigrate from AuthenticationContext to PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Costruzione di PublicClientApplicationConstructing PublicClientApplication

Quando si utilizza MSAL, `PublicClientApplication`si crea un'istanza di . Questo oggetto modella l'identità dell'app e viene usato per effettuare richieste a una o più autorità. Con questo oggetto si configureranno l'identità del client, l'URI di reindirizzamento, l'autorità predefinita, se utilizzare il browser del dispositivo rispetto alla visualizzazione Web incorporata, il livello di registrazione e altro ancora.

È possibile configurare in modo dichiarativo questo oggetto con JSON, che viene fornito come file o archivio come risorsa all'interno dell'APK.

Anche se questo oggetto non è un singleton, internamente utilizza condiviso `Executors` sia per le richieste interattive che silenziose.

### <a name="business-to-business"></a>Business to Business

In ADAL, ogni organizzazione da cui si richiedono i `AuthenticationContext`token di accesso richiede un'istanza separata di . In MSAL, questo non è più un requisito. È possibile specificare l'autorità da cui si desidera richiedere un token come parte della richiesta invisibile all'utente o interattiva.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Eseguire la migrazione dalla convalida dell'autorità alle autorità noteMigrate from authority validation to known authorities

MSAL non dispone di un flag per abilitare o disabilitare la convalida dell'autorità. La convalida dell'autorità è una funzionalità di ADAL e nelle prime versioni di MSAL, che impedisce al codice di richiedere token a un'autorità potenzialmente dannosa. MSAL ora recupera un elenco di autorità note a Microsoft e si unisce tale elenco con le autorità specificate nella configurazione.

> [!TIP]
> Se si è un utente di Azure Business Business to Consumer (B2C), non è più necessario disabilitare la convalida dell'autorità. Includere invece ognuno dei criteri B2C di Azure AD supportati come autorità nella configurazione MSAL.

Se si tenta di utilizzare un'autorità non nota a Microsoft e non inclusa nella configurazione, si otterrà un `UnknownAuthorityException`file .

### <a name="logging"></a>Registrazione
È ora possibile configurare in modo dichiarativo la registrazione come parte della configurazione, in questo modo:You can now declaratively configure logging as part of your configuration, like this:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Eseguire la migrazione da UserInfo a AccountMigrate from UserInfo to Account

In ADAL, `AuthenticationResult` fornisce `UserInfo` un oggetto utilizzato per recuperare informazioni sull'account autenticato. Il termine "utente", che significava un agente umano o software, è stato applicato in un modo che ha reso difficile comunicare che alcune applicazioni supportano un singolo utente (se un agente umano o software) che dispone di più account.

Si consideri un conto bancario. È possibile avere più di un conto in più di un istituto finanziario. Quando si apre un account, all'utente vengono rilasciate credenziali, ad esempio una carta atm & PIN, che vengono utilizzate per accedere al saldo, ai pagamenti delle fatture e così via, per ogni account. Tali credenziali possono essere utilizzate solo presso l'istituto finanziario che le ha rilasciate.

Per analogia, come gli account di un istituto finanziario, gli account nella piattaforma di identità Microsoft sono accessibili tramite credenziali. Tali credenziali sono registrate o rilasciate da Microsoft. O da Microsoft per conto di un'organizzazione.

Se la piattaforma di identità Microsoft differisce da un istituto finanziario, in questa analogia è che la piattaforma di identità Microsoft fornisce un framework che consente a un utente di utilizzare un account, e le credenziali associate, per accedere alle risorse che appartengono a più individui e organizzazioni. Questo è come essere in grado di utilizzare una carta emessa da una banca, presso un altro istituto finanziario. Questo funziona perché tutte le organizzazioni in questione utilizzano la piattaforma di identità Microsoft, che consente di utilizzare un account tra più organizzazioni. Ad esempio:

Sam lavora per Contoso.com ma gestisce le macchine virtuali di Azure che appartengono a Fabrikam.com. Affinché Sam gestisca le macchine virtuali di Fabrikam, deve essere autorizzato ad accedervi. Questo accesso può essere concesso aggiungendo l'account di Sam a Fabrikam.com e concedendo al proprio account un ruolo che gli consenta di lavorare con le macchine virtuali. Questa operazione verrà eseguita con il portale di Azure.This would be done with the Azure portal.

L'aggiunta dell'account Contoso.com di Sam come membro di Fabrikam.com comporterebbe la creazione di un nuovo record in Azure Active Directory per Sam di Fabrikam.com. Il record di Sam in Azure Active Directory è noto come oggetto utente. In questo caso, l'oggetto utente punterebbe all'oggetto utente di Sam in Contoso.com. L'oggetto utente Fabrikam di Sam è la rappresentazione locale di Sam e viene utilizzato per archiviare informazioni sull'account associato a Sam nel contesto di Fabrikam.com. In Contoso.com, il titolo di Sam è Senior DevOps Consultant. In Fabrikam, il titolo di Sam è Contractor-Virtual Machines. In Contoso.com, Sam non è responsabile, né autorizzato, per gestire le macchine virtuali. In Fabrikam.com, questa è la sua unica funzione lavorativa. Eppure Sam ha ancora solo un set di credenziali per tenere traccia di, che sono le credenziali rilasciate da Contoso.com.

Una volta `acquireToken` effettuata una chiamata riuscita, `IAccount` verrà visualizzato un `acquireTokenSilent` riferimento a un oggetto che può essere utilizzato nelle richieste successive.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Se si dispone di un'app che accede alle attestazioni relative a un account `IAccount` da `IMultiTenantAccount`ognuno dei tenant in cui è rappresentato l'account, è possibile eseguire il cast di oggetti in . Questa interfaccia fornisce `ITenantProfiles`una mappa di , con chiave in base all'ID tenant, che consente di accedere alle attestazioni che appartengono all'account in ognuno dei tenant da cui è stato richiesto un token, relativo all'account corrente.

Le attestazioni alla `IAccount` radice `IMultiTenantAccount` del e contengono sempre le attestazioni dal tenant di casa. Se non è ancora stata effettuata una richiesta per un token all'interno del tenant principale, questa raccolta sarà vuota.

## <a name="other-changes"></a>Altre modifiche

### <a name="use-the-new-authenticationcallback"></a>Utilizzare il nuovo AuthenticationCallbackUse the new AuthenticationCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>Eseguire la migrazione alle nuove eccezioniMigrate to the new exceptions

In ADAL è presente un tipo `AuthenticationException`di eccezione, , `ADALError` che include un metodo per il recupero del valore enum.
In MSAL, esiste una gerarchia di eccezioni e ognuna ha il proprio set di codici di errore specifici associati.

Elenco delle eccezioni MSAL

|Eccezione  | Descrizione  |
|---------|---------|
| `MsalException`     | Eccezione controllata predefinita generata da MSAL.  |
| `MsalClientException`     | Generato se l'errore è sul lato client. |
| `MsalArgumentException`     | Generata se uno o più argomenti di input non sono validi. |
| `MsalClientException`     | Generato se l'errore è sul lato client. |
| `MsalServiceException`     | Generato se l'errore è lato server. |
| `MsalUserCancelException`     | Generato se l'utente ha annullato il flusso di autenticazione.  |
| `MsalUiRequiredException`     | Generato se il token non può essere aggiornato automaticamente.  |
| `MsalDeclinedScopeException`     | Generato se uno o più ambiti richiesti sono stati rifiutati dal server.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | Generato se la risorsa ha attivato il criterio di protezione MAMCA. |

### <a name="adalerror-to-msalexception-errorcode"></a>Da ADALError a MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>Registrazione ADAL nella registrazione MSAL

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
