---
title: Avvio rapido per l'uso di Microsoft Identity Platform con un'app Android | Azure
description: Informazioni su come le applicazioni Android possono chiamare un'API che richiede token di accesso generati dall'endpoint di Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e11e47952f70ce0cd212ca93eff1c38f2b3993a8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678059"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Guida introduttiva: Accesso utenti e chiamata dell'API Microsoft Graph da un'app di Android

In questa guida di avvio rapido si usa un esempio di codice per illustrare in che modo un'applicazione Android può accedere ad account personali, aziendali o dell'istituto di istruzione, ottenere un token di accesso e chiamare l'API Microsoft Graph.

![Screenshot dell'app di esempio](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Prerequisiti**
> * Android Studio 
> * Android 16+ (obbligatorio)

## <a name="step-1-get-the-sample-app"></a>Passaggio 1: Ottenere l'app di esempio

[Clonare il codice](https://github.com/Azure-Samples/ms-identity-android-java.git).

## <a name="step-2-register-your-application"></a>Passaggio 2: Registrare l'applicazione

Per registrare un oggetto applicazione e aggiungere manualmente le informazioni di registrazione dell'oggetto applicazione al progetto di esempio, seguire questa procedura:

1. Accedere al [portale di Azure](https://aka.ms/MobileAppReg).
1. Aprire il pannello [Registrazioni per l'app](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) e fare clic su **+ Nuova registrazione**.
1. In **Nome** immettere un nome per la registrazione dell'app e quindi, senza impostare un URI di reindirizzamento, fare clic su **Registra**.
1. Nella sezione **Gestione** selezionare **Autenticazione** >  **+ Aggiungi una piattaforma**  > **Android**. Per visualizzare questa schermata, potrebbe essere necessario selezionare l'opzione **Prova la nuova esperienza** nella parte superiore del pannello.
1. Immettere il valore `com.azuresamples.msalandroidapp` in **Nome pacchetto** per il progetto.
1. Nella sezione **Hash della firma** della pagina **Configura l'app Android** fare clic su **Generazione di un hash della firma per lo sviluppo** e copiare il comando KeyTool da usare per la piattaforma usata per sviluppare l'app Android.

   > [!Note]
   > KeyTool.exe viene installato come parte di Java Development Kit (JDK). Per eseguire il comando KeyTool, è necessario installare anche lo strumento OpenSSL.  Nel percorso sono necessari KeyTool e la directory OpenSSL\bin.

1. In una finestra del terminale eseguire il comando KeyTool copiato dal portale.
1. Immettere l'hash della firma generato nel portale in **Hash della firma**.
1. Fare clic su `Configure` e creare una copia della **configurazione MSAL**, che verrà copiata e incollata in un file di configurazione nel passaggio successivo. Fare clic su **Done**.

## <a name="step-3-add-your-app-registration"></a>Passaggio 3: Aggiungere la registrazione per l'app

1. Aprire il progetto di esempio in Android Studio.
1. In **app** > **res** > **raw** aprire **auth_config_multiple_account.json**.  Incollare il contenuto della configurazione MSAL. In questo modo verranno aggiunti i valori di ID client, ID tenant e redirect_uri dal portale. Il risultato sarà simile al seguente, ma con i valori inseriti per ID client, ID tenant e redirect_uri:

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
      "account_mode" : "MULTIPLE",
      "broker_redirect_uri_registered": true,
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
    ```

1. Aprire **app** > **res** > **raw**, aprire **auth_config_single_account.json** e incollare il contenuto della configurazione MSAL. Il risultato sarà simile al file **auth_config_multiple_account.json** indicato sopra.
1. In **app** > **manifests** > **AndroidManifest.xml** individuare l'attività `BrowserTabActivity`. Questa voce consente a Microsoft di eseguire il callback all'applicazione dopo il completamento dell'autenticazione:

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. Sostituire il nome del pacchetto con quello registrato nel portale di Azure per il valore `android:host=`.  In questo caso, sarà `com.azuresamples.msalandroidapp`.

    > [!IMPORTANT]
    > Il valore **android:path** **deve** includere un carattere "/" iniziale, altrimenti sotto il valore sarà presente una riga rossa e l'app di esempio non funzionerà.
     
1. Sostituire il valore `android:path=` con l'hash della chiave ottenuto eseguendo KeyTool in precedenza e che è stato immesso nel portale di Azure. L'hash della firma non deve essere codificato in URL.

## <a name="step-4-run-the-sample-app"></a>Passaggio 4: Eseguire l'app di esempio

Selezionare l'emulatore o il dispositivo dall'elenco a discesa dei **dispositivi disponibili** di Android Studio ed eseguire l'app.

L'app di esempio viene avviata nella schermata **Single Account Mode** (Modalità per account singolo). Per impostazione predefinita, viene fornito un ambito predefinito, **user.read**, che viene usato durante la lettura dei dati del profilo durante la chiamata all'API Microsoft Graph. L'URL per la chiamata all'API Microsoft Graph viene fornito per impostazione predefinita. Se si desidera, è possibile modificarli entrambi.

![App di esempio MSAL che illustra l'utilizzo di account singoli o multipli](./media/quickstart-v2-android/quickstart-sample-app.png)

Usare il menu dell'app per passare dalla modalità per account singolo a quella per account multipli e viceversa.

Nella modalità per account singolo accedere con un account aziendale o personale:

1. Selezionare **Get graph data interactively** (Ottieni i dati del grafo in modo interattivo) per richiedere all'utente le credenziali. L'output della chiamata all'API Microsoft Graph verrà visualizzato nella parte inferiore della schermata.
2. Dopo l'accesso selezionare **Get graph data silently** (Ottieni i dati del grafo in modo automatico) per effettuare una chiamata all'API Microsoft Graph senza richiedere di nuovo le credenziali utente. L'output della chiamata all'API Microsoft Graph verrà visualizzato nella parte inferiore della schermata.

Nella modalità per account multipli è possibile ripetere gli stessi passaggi.  È anche possibile rimuovere l'account di accesso e di conseguenza rimuovere i token memorizzati nella cache per tale account.

## <a name="how-the-sample-works"></a>Funzionamento dell'esempio

Il codice è organizzato in frammenti in cui viene illustrato come scrivere un'app MSAL per account singolo e account multipli. I file di codice sono organizzati come indicato di seguito:

| File  | Dimostra  |
|---------|---------|
| MainActivity | Gestisce l'interfaccia utente |
| MSGraphRequestWrapper  | Chiama l'API Microsoft Graph usando il token fornito da MSAL |
| MultipleAccountModeFragment  | Inizializza un'applicazione per account multipli, carica un account utente e ottiene un token per chiamare l'API Microsoft Graph |
| SingleAccountModeFragment | Inizializza un'applicazione per account singolo, carica un account utente e ottiene un token per chiamare l'API Microsoft Graph |
| res/auth_config_multiple_account.json  | File di configurazione per account multipli |
| res/auth_config_single_account.json  | File di configurazione per l'account singolo |
| Gradle Scripts/build.grade (Module:app) | Posizione in cui aggiungere le dipendenze della libreria MSAL |

I file verranno ora esaminati in modo più dettagliato e verrà richiamato il codice specifico di MSAL in ognuno di essi.

### <a name="add-msal-to-the-app"></a>Aggiungere MSAL all'app

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) è la libreria usata per concedere l'accesso agli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform. Gradle 3.0+ installa la libreria quando si aggiunge quanto segue in **Gradle Scripts** > **build.gradle (Module: app)** in **Dependencies**:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

È possibile verificarlo nel progetto di esempio in build.gradle (Module: app):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
    ...
}
```

Indica a Gradle di scaricare e compilare MSAL da Maven Central.

### <a name="msal-imports"></a>Importazioni MSAL

Le importazioni pertinenti per la libreria MSAL sono `com.microsoft.identity.client.*`.  Viene ad esempio visualizzato `import com.microsoft.identity.client.PublicClientApplication;`, ovvero lo spazio dei nomi per la classe `PublicClientApplication`, che rappresenta l'applicazione client pubblica.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Questo file illustra come creare un'app MSAL per account singolo e chiamare un'API Microsoft Graph.

Le app per account singolo vengono usate solo da un unico utente.  Ad esempio, è possibile avere un solo account con cui si accede all'app di mapping.

#### <a name="single-account-msal-initialization"></a>Inizializzazione di MSAL per account singolo

In `onCreateView()` viene creato un account singolo `PublicClientApplication` usando le informazioni di configurazione archiviate nel file `auth_config_single_account.json`.  Ecco come inizializzare la libreria MSAL per l'uso in un'app MSAL per account singolo:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Accesso di un utente

Il codice per l'accesso di un utente è `initializeUI()` nel gestore di clic `signInButton`.

Chiamare `signIn()` prima di provare ad acquisire i token. `signIn()` si comporta come se venisse chiamato `acquireToken()` e consente di ottenere un prompt interattivo per l'accesso dell'utente.

L'accesso di un utente è un'operazione asincrona. Viene passato un callback che chiama l'API Microsoft Graph e aggiorna l'interfaccia utente dopo che l'utente ha eseguito l'accesso:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Disconnessione di un utente

Il codice per la disconnessione di un utente è `initializeUI()` nel gestore di clic `signOutButton`.  La disconnessione di un utente è un'operazione asincrona. La disconnessione dell'utente implica anche la cancellazione della cache dei token per l'account. Viene creato un callback per aggiornare l'interfaccia utente dopo che l'account utente è stato disconnesso:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Ottenere un token in modo interattivo o automatico

Per fare in modo che all'utente venga presentato il minor numero di richieste, il token viene in genere ottenuto automaticamente. Se si verifica un errore, provare a ottenere il token in modo interattivo. La prima volta che l'app chiama `signIn()`, funge in realtà da chiamata a `acquireToken()`, che richiederà all'utente l'immissione delle credenziali.

Ecco alcune situazioni in cui all'utente finale potrebbe essere richiesto di selezionare il proprio account, immettere le credenziali o fornire il consenso per le autorizzazioni richieste dall'app:

* La prima volta che l'utente accede all'applicazione
* Se un utente reimposta la password, dovrà immettere le credenziali
* Se il consenso viene revocato
* Se l'app richiede esplicitamente il consenso
* Quando l'applicazione richiede l'accesso a una risorsa per la prima volta
* Quando è necessario eseguire l'autenticazione a più fattori o soddisfare altri criteri di accesso condizionale

Il codice per ottenere un token in modo interattivo, ovvero visualizzando un'interfaccia utente per richiedere l'input dell'utente, si trova in `initializeUI()` nel gestore di clic `callGraphApiInteractiveButton`:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Se l'utente ha già eseguito l'accesso, `acquireTokenSilentAsync()` consente alle app di richiedere i token in modo automatico, come illustrato in `initializeUI()`, nel gestore di clic `callGraphApiSilentButton`:

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Caricare un account

Il codice per caricare un account si trova in `loadAccount()`.  Il caricamento dell'account dell'utente è un'operazione asincrona, di conseguenza i callback da gestire quando l'account viene caricato, cambia o si verifica un errore vengono passati a MSAL.  Il codice seguente gestisce anche `onAccountChanged()`, che si verifica quando un account viene rimosso, l'utente passa a un altro account e così via.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Chiamare Microsoft Graph

Quando un utente ha eseguito l'accesso, la chiamata a Microsoft Graph viene effettuata tramite una richiesta HTTP da `callGraphAPI()`. Questa funzione è un wrapper che semplifica l'esempio eseguendo alcune attività, ad esempio il recupero del token di accesso da `authenticationResult` e la creazione di un pacchetto della chiamata a MSGraphRequestWrapper, nonché visualizzando i risultati della chiamata.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Questo è il file di configurazione per un'app MSAL che usa un account singolo.

Per una spiegazione di questi campi, vedere [Informazioni sul file di configurazione di Android Microsoft Authentication Library (MSAL)](msal-configuration.md).

Si noti la presenza di `"account_mode" : "SINGLE"`, che configura l'app per l'uso di un account singolo.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Questo file illustra come creare un'app MSAL per account multipli e chiamare un'API Microsoft Graph. 

Un esempio di app per account multipli è costituito da un'app di posta elettronica che consente di usare più account utente, ad esempio un account aziendale e un account personale.

#### <a name="multiple-account-msal-initialization"></a>Inizializzazione di MSAL per account multipli

In `onCreateView()` viene creato un oggetto app per account multiplo (`IMultipleAccountPublicClientApplication`) usando le informazioni di configurazione archiviate nel file `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

L'oggetto `MultipleAccountPublicClientApplication` creato viene archiviato in una variabile di membro di classe in modo che possa essere usato per interagire con la libreria MSAL allo scopo di acquisire i token e caricare e rimuovere l'account utente.

#### <a name="load-an-account"></a>Caricare un account

Le app per account multipli chiamano in genere `GetAccounts()` per selezionare l'account da usare per le operazioni MSAL. Il codice per caricare un account si trova in `loadAccount()`.  Il caricamento dell'account dell'utente è un'operazione asincrona. Un callback gestisce quindi le situazioni in cui l'account viene caricato, cambia o si verifica un errore.

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Ottenere un token in modo interattivo o automatico

Ecco alcune situazioni in cui all'utente finale potrebbe essere richiesto di selezionare il proprio account, immettere le credenziali o fornire il consenso per le autorizzazioni richieste dall'app:

* La prima volta che gli utenti accedono all'applicazione
* Se un utente reimposta la password, dovrà immettere le credenziali 
* Se il consenso viene revocato 
* Se l'app richiede esplicitamente il consenso 
* Quando l'applicazione richiede l'accesso a una risorsa per la prima volta
* Quando è necessario eseguire l'autenticazione a più fattori o soddisfare altri criteri di accesso condizionale

Le app per account multipli devono in genere acquisire token in modo interattivo, ovvero visualizzando un'interfaccia utente per richiedere l'input dell'utente, con una chiamata a `acquireToken()`.  Il codice per ottenere un token in modo interattivo si trova in `initializeUI()`, nel gestore di clic `callGraphApiInteractiveButton`:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Le app non dovrebbero richiedere agli utenti di eseguire l'accesso ogni volta che richiedono un token. Se l'utente ha già eseguito l'accesso, `acquireTokenSilentAsync()` consente alle app di richiedere i token senza richiedere l'input dell'utente, come illustrato in `initializeUI()`, nel gestore di clic `callGraphApiSilentButton`:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
accountList.get(accountListSpinner.getSelectedItemPosition()),
AUTHORITY,
getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Rimuovere un account

Il codice per rimuovere un account e tutti i token memorizzati nella cache per l'account si trova in `initializeUI()`, nel gestore del pulsante Rimuovi account. Prima di poter rimuovere un account, è necessario un oggetto account, ottenuto da funzioni MSAL come `getAccounts()` e `acquireToken()`. Dal momento che la rimozione di un account è un'operazione asincrona, viene fornito il callback `onRemoved` per aggiornare l'interfaccia utente.

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Questo è il file di configurazione per un'app MSAL che usa account multipli.

Per una spiegazione di questi campi, vedere [Informazioni sul file di configurazione di Android Microsoft Authentication Library (MSAL)](msal-configuration.md).

A differenza del file di configurazione [auth_config_single_account.json](#auth_config_single_accountjson), questo file contiene `"account_mode" : "MULTIPLE"` invece di `"account_mode" : "SINGLE"` perché si tratta di un'app per account multipli.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Informazioni sulla procedura per creare l'applicazione usata in questa guida introduttiva

Provare l'esercitazione Android per una guida dettagliata completa sulla creazione di applicazioni e di nuove funzionalità, tra cui una spiegazione completa di questa guida introduttiva.

> [!div class="nextstepaction"]
> [Esercitazione Android per le chiamate API Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL per wiki della libreria Android

Leggere altre informazioni sulla libreria MSAL per Android:

> [!div class="nextstepaction"]
> [MSAL per wiki della libreria Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Contribuire al miglioramento di Microsoft Identity Platform. Completare un breve sondaggio di due domande per condividere la propria opinione.

> [!div class="nextstepaction"]
> [Sondaggio su Microsoft Identity Platform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
