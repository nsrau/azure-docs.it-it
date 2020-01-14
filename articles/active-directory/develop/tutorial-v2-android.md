---
title: Concedere l'accesso agli utenti, disconnetterli e chiamare Microsoft Graph (Android) - Microsoft Identity Platform | Azure
description: Ottenere un token di accesso e chiamare Microsoft Graph o le API che richiedono token di accesso da Microsoft Identity Platform (Android)
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4c4c9bc025e8fd506b298ed676674899e318481
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689350"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Esercitazione: Concedere l'accesso agli utenti e chiamare Microsoft Graph da un'app Android 

>[!NOTE]
>Questa esercitazione illustra esempi semplificati di come usare MSAL per Android. Per semplicità, in questa esercitazione viene usata solo la modalità Account singolo. È anche possibile visualizzare il repository e clonare l'[app di esempio preconfigurata](https://github.com/Azure-Samples/ms-identity-android-java/) per esplorare scenari più complessi. Per altre informazioni sull'app di esempio, sulla configurazione e sulla registrazione, vedere la guida di [Avvio rapido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android). 

Questa esercitazione spiega come integrare l'app Android con Microsoft Identity Platform usando Microsoft Authentication Library per Android. Verrà illustrato come concedere l'accesso a un utente e disconnetterlo, come ottenere un token di accesso per chiamare l'API Microsoft Graph e come inviare una richiesta all'API Graph. 

> [!div class="checklist"]
> * Integrare l'app Android con Microsoft Identity Platform 
> * Accesso di un utente 
> * Reperimento di un token di accesso per chiamare l'API Microsoft Graph 
> * Chiamare l'API Microsoft Graph 
> * Disconnessione di un utente 

Al termine dell'esercitazione, l'applicazione accetterà accessi di account Microsoft personali (ad esempio outlook.com, live.com e di altro tipo) e di account aziendali o dell'istituto di istruzione di qualsiasi azienda o organizzazione che usi Azure Active Directory.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="how-this-tutorial-works"></a>Contenuto dell'esercitazione

![Illustra come funziona l'app di esempio generata da questa esercitazione](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

L'app in questa esercitazione consentirà agli utenti di accedere e recuperare i dati per loro conto. Questi dati saranno accessibili tramite un'API protetta (API Microsoft Graph) che richiede l'autorizzazione ed è protetta da Microsoft Identity Platform.

Più in particolare:

* L'app consentirà l'accesso all'utente tramite un browser o Microsoft Authenticator e il portale aziendale Intune.
* L'utente finale accetterà le autorizzazioni richieste dall'applicazione.
* All'app verrà fornito un token di accesso per l'API Microsoft Graph.
* Il token di accesso verrà incluso nella richiesta HTTP all'API Web.
* Elaborare la risposta di Microsoft Graph.

Questo esempio usa Microsoft Authentication Library per Android (MSAL) per implementare l'autenticazione: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL rinnoverà automaticamente i token, distribuirà il Single Sign-On (SSO) tra le altre app nel dispositivo e gestirà gli account.

### <a name="prerequisites"></a>Prerequisites

* Per questa esercitazione è necessario Android Studio versione 3.5 o successiva

## <a name="create-a-project"></a>Creazione di un progetto
Se non si dispone già di un'applicazione Android, seguire questa procedura per configurare un nuovo progetto. 

1. Aprire Android Studio e selezionare **Start a new Android Studio project** (Avvia un nuovo progetto di Android Studio).
2. Selezionare **Basic Activity** (Attività di base) e quindi **Next** (Avanti).
3. Assegnare un nome all'applicazione.
4. Salvare il nome del pacchetto. Dovrà essere immesso più tardi nel portale di Azure.
5. Modificare il linguaggio da **Kotlin** a **Java**.
6. Impostare **Minimum API level** (Livello API minimo) su **API 19** o su un valore superiore e fare clic su **Finish** (Fine).
7. Nella visualizzazione del progetto selezionare **Project** (Progetto) nell'elenco a discesa per visualizzare i file di progetto di origine e non di origine, aprire **app/build.gradle** e impostare `targetSdkVersion` su `28`.

## <a name="integrate-with-microsoft-authentication-library"></a>Integrazione con Microsoft Authentication Library 

### <a name="register-your-application"></a>Registrare l'applicazione

1. Accedere al [portale di Azure](https://aka.ms/MobileAppReg).
2. Aprire il pannello [Registrazioni per l'app](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) e fare clic su **+ Nuova registrazione**.
3. In **Nome** immettere un nome per l'app e quindi, **senza** impostare un URI di reindirizzamento, fare clic su **Registra**.
4. Nella sezione **Gestione** del riquadro visualizzato selezionare **Autenticazione** >  **+ Aggiungi una piattaforma**  > **Android**. Per visualizzare questa sezione potrebbe essere necessario selezionare l'opzione per passare alla nuova esperienza nella parte superiore del pannello.
5. Immettere il nome del pacchetto del progetto. Se è stato scaricato il codice, il valore è `com.azuresamples.msalandroidapp`.
6. Nella sezione **Hash della firma**  della pagina **Configura l'app Android**  fare clic su **Generazione di un hash della firma per lo sviluppo** e copiare il comando KeyTool da usare per la piattaforma.

   > [!Note]
   > KeyTool.exe viene installato come parte di Java Development Kit (JDK). Per eseguire il comando KeyTool, è necessario installare anche lo strumento OpenSSL.

7. Immettere il valore di **Hash della firma** generato da KeyTool.
8. Fare clic su `Configure` e salvare la **configurazione MSAL** visualizzata nella pagina **Configurazione di Android** in modo da poterla immettere in seguito durante la configurazione dell'app.  Fare clic su **Done**.

### <a name="configure-your-application"></a>Configurare l'applicazione 

1. Nel riquadro dei progetti di Android Studio passare a **app\src\main\res**.
2. Fare clic con il pulsante destro del mouse su **res** e scegliere **Nuovo** > **Directory**. Immettere `raw` come nome della nuova directory e fare clic su **OK**.
3. In **app** > **src** > **main** > **res** > **raw** creare un nuovo file JSON denominato `auth_configbn_single_account.json` e incollare la configurazione MSAL salvata in precedenza. 

    Sotto l'URI di reindirizzamento incollare: 
    ```json
      "account_mode" : "SINGLE",
    ```
    Il file di configurazione dovrebbe essere simile a questo esempio: 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
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
    
   >[!NOTE]
   >Questa esercitazione illustra solo come configurare un'app nella modalità Account singolo. Per altre informazioni sulla [modalità Account singolo e sulla modalità Più account ](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) e su come [configurare l'app](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration), vedere la documentazione
   
4. In **app** > **src** > **main** > **AndroidManifest.xml** aggiungere l'attività `BrowserTabActivity` sotto il corpo dell'applicazione. Questa voce consente a Microsoft di eseguire il callback all'applicazione dopo il completamento dell'autenticazione:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Sostituire il valore `android:host=` con il nome del pacchetto registrato nel portale di Azure.
    Sostituire il valore `android:path=` con l'hash della chiave registrato nel portale di Azure. L'hash della firma **non** deve essere codificato in URL. Verificare che sia presente un `/` iniziale all'inizio dell'hash della firma. 
    >[!NOTE]
    >Il "Nome del pacchetto" con cui verrà sostituito il valore di `android:host` dovrà avere un aspetto simile al seguente: "com.azuresamples.msalandroidapp". L'"hash della firma" con cui verrà sostituito il valore di `android:path` dovrà avere un aspetto simile al seguente: "/1wIqXSqBj7w+h11ZifsnqwgyKrY=". Questi valori saranno anche disponibili nel pannello Autenticazione della registrazione app. Si noti che l'URI di reindirizzamento avrà un aspetto simile al seguente: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Mentre l'hash della firma è codificato in URL alla fine di questo valore, l'hash della firma **non** deve essere codificato in URL nel valore di `android:path`. 

## <a name="use-msal"></a>Usare MSAL 

### <a name="add-msal-to-your-project"></a>Aggiungere MSAL al progetto

1. Nella finestra del progetto di Android Studio passare a **app** > **src** > **build.gradle** e aggiungere quanto segue: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.0.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    ```
    [Altre informazioni su Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Istruzioni import obbligatorie 

Aggiungere quanto segue sopra **app** > **src** > **main**> **java** > **com.example(yourapp)**  > **MainActivity.java** 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Creare un'istanza di PublicClientApplication
#### <a name="initialize-variables"></a>Inizializzare le variabili 
```java
private final static String[] SCOPES = {"User.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
All'interno della classe `MainActivity`, fare riferimento al metodo onCreate() seguente per creare un'istanza di MSAL usando `SingleAccountPublicClientApplication`.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

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
}
```

### <a name="initializeui"></a>initializeUI
Rimanere in ascolto dei pulsanti e chiamare i metodi o registrare gli errori. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> La disconnessione con MSAL rimuove tutte le informazioni note su un utente da questa applicazione, ma l'utente avrà ancora una sessione attiva nel dispositivo. Se l'utente tenta di accedere nuovamente, è possibile che veda l'interfaccia utente per l'accesso, ma che non debba immettere nuovamente le credenziali dato che la sessione del dispositivo è ancora attiva. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Callback usato per le richieste interattive.

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Callback usato per le richieste non interattive 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Chiamare l'API Microsoft Graph 

Il codice seguente illustra come chiamare GraphAPI usando Graph SDK. 

### <a name="callgraphapi"></a>callGraphAPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Aggiungere l'interfaccia utente
### <a name="activity"></a>Attività 
Se si vuole modellare l'interfaccia utente in base a questa esercitazione, i metodi seguenti forniscono una guida all'aggiornamento del testo e all'ascolto dei pulsanti.

#### <a name="updateui"></a>updateUI
Abilita/Disabilita i pulsanti in base allo stato di accesso e imposta il testo.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Metodo per aggiornare il testo nell'interfaccia utente per riflettere la disconnessione. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout 

File `activity_main.xml` di esempio per visualizzare i pulsanti e le caselle di testo. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Test dell'app

### <a name="run-locally"></a>Esecuzione locale

Compilare e distribuire l'app in un dispositivo di test o un emulatore. Dovrebbe essere possibile accedere e ottenere i token per account di Azure AD o gli account Microsoft personali.

Dopo l'accesso, l'app visualizzerà i dati restituiti dall'endpoint `/me` di Microsoft Graph.

### <a name="consent"></a>Consenso

Al primo accesso di qualsiasi utente nell'app, verrà richiesto il consenso per le autorizzazioni richieste da Microsoft Identity Platform. Per alcuni tenant di Azure AD è stato disabilitato il consenso dell'utente e sarà quindi necessario che gli amministratori forniscano il consenso per conto di tutti gli utenti. Per supportare questo scenario, sarà necessario creare un tenant personalizzato o ricevere il consenso dell'amministratore. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare l'oggetto app creato nel passaggio [Registrare l'applicazione](#register-your-application).

## <a name="get-help"></a>Ottenere aiuto

In caso di problemi con questa esercitazione o con Microsoft Identity Platform, vedere [Opzioni di supporto tecnico e assistenza per gli sviluppatori](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

Contribuire al miglioramento di Microsoft Identity Platform. Completare un breve sondaggio di due domande per condividere la propria opinione.

> [!div class="nextstepaction"]
> [Sondaggio su Microsoft Identity Platform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
