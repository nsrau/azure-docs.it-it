---
title: Introduzione ad Android - Microsoft Identity Platform | Azure
description: Come un'app Android può ottenere un token di accesso e chiamare l'API Microsoft Graph o API che richiedono token di accesso da Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2914d6b1f4a6c94d7e3d4456c8255c1563a71b3e
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406645"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Accesso utenti e chiamata di Microsoft Graph da un'app Android

In questa esercitazione si apprenderà come integrare un'app Android in Microsoft Identity Platform. In particolare, l'app consentirà l'accesso di un utente, otterrà un token di accesso per chiamare l'API Microsoft Graph e creerà una richiesta all'API Microsoft Graph.  

Al termine della guida, l'applicazione accetterà accessi di account Microsoft personali (ad esempio outlook.com, live.com e di altro tipo) e di account aziendali o dell'istituto di istruzione di qualsiasi azienda o organizzazione che usa Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Contenuto dell'esercitazione

![Illustra come funziona l'app di esempio generata da questa esercitazione](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

L'app in questo esempio consentirà agli utenti di accedere e otterrà i dati per loro conto.  Questi dati saranno accessibili tramite un'API protetta (in questo caso, l'API Microsoft Graph) che richiede l'autorizzazione.

Più in particolare:

* L'app consentirà l'accesso all'utente tramite un browser o Microsoft Authenticator e il portale aziendale Intune.
* L'utente finale accetterà le autorizzazioni richieste dall'applicazione. 
* All'app verrà fornito un token di accesso per l'API Microsoft Graph.
* Il token di accesso verrà incluso nella richiesta HTTP all'API Web.
* Elaborare la risposta di Microsoft Graph.

Questo esempio usa Microsoft Authentication Library per Android (MSAL) per implementare l'autenticazione. MSAL rinnoverà automaticamente i token, distribuirà SSO tra le altre app nel dispositivo e gestirà gli account.

## <a name="prerequisites"></a>Prerequisiti

* Questa configurazione guidata usa Android Studio.
* È necessario Android 16 o versione successiva (si consiglia la versione 19 o successiva).

## <a name="library"></a>Libreria

Questa guida usa la libreria di autenticazione seguente:

|Libreria|DESCRIZIONE|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

## <a name="set-up-your-project"></a>Configurare il progetto

Questa esercitazione creerà un nuovo progetto. Se si preferisce scaricare invece l'esercitazione completata [scaricare il codice](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Creare un nuovo progetto

1. Aprire Android Studio e selezionare **Start a new Android Studio project** (Avvia un nuovo progetto di Android Studio).
    - Se Android Studio è già aperto, selezionare **File** > **New** (Nuovo) > **New Project** (Nuovo progetto).
2. Lasciare invariata l'opzione **Empty Activity** (Attività vuota) e selezionare **Next** (Avanti).
3. Assegnare un nome all'applicazione, impostare `Minimum API level` su **API 19 or newer** (API 19 o versione successiva) e fare clic su **Finish** (Fine).
5. In `app/build.gradle` impostare `targetedSdkVersion` su 27. 

## <a name="register-your-application"></a>Registrare l'applicazione

È possibile registrare l'applicazione in uno dei due modi descritti nelle due sezioni successive.

### <a name="register-your-app"></a>Registrare l'app

1. Passare al [portale di Azure](https://aka.ms/MobileAppReg) e selezionare `New registration`. 
2. Immettere un nome per l'app in **Nome** e selezionare `Register`. **Non impostare un URI di reindirizzamento in questa fase**. 
3. Nella sezione `Manage` passare a `Authentication` > `Add a platform` > `Android`
    - Immettere il nome del pacchetto del progetto. Se è stato scaricato il codice, il valore è `com.azuresamples.msalandroidapp`. 
    - Immettere l'hash della firma per il debug o lo sviluppo. Usare il comando KeyTool nel portale per generare l'hash della firma. 
4. Fare clic su `Configure` e archiviare la ***configurazione MSAL*** per usarla in seguito. 

## <a name="build-your-app"></a>Creare l'app

### <a name="configure-your-android-app"></a>Configurare l'app Android

1. Fare clic con il pulsante destro del mouse su **res** > **New** > **Folder** > **Raw Resources Folder** (ris. > Nuovo > Cartella > Cartella risorse non elaborate)
2. In **app** > **res** > **raw** (app > ris. > non elaborate) creare un nuovo file JSON denominato `auth_config.json` e incollare la ***configurazione MSAL***. Per altre informazioni, vedere [MSAL Configuration](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app) (Configurazione MSAL).
   <!-- Workaround for Docs conversion bug -->
3. In **app** > **manifests** > **AndroidManifest.xml** (app > manifesti > AndroidManifest.xml) aggiungere l'attività `BrowserTabActivity` seguente. Questa voce consente a Microsoft di eseguire il callback all'applicazione dopo il completamento dell'autenticazione:

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

    Tenere presente che l'hash della firma usato NON deve essere codificato in URL in **AndroidManifest.xml**. 

4. In **AndroidManifest.xml** aggiungere le autorizzazioni seguenti subito prima del tag `<application>`:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. In `BrowserTabActivity` sostituire il ***nome del pacchetto*** e l'***hash della firma*** con i valori registrati nel portale di Azure.

### <a name="create-the-apps-ui"></a>Creare l'interfaccia utente dell'app

1. Passare a **res** > **layout** e quindi aprire **activity_main.xml**.
2. Modificare il layout di attività da `android.support.constraint.ConstraintLayout` o altro a `LinearLayout`.
3. Aggiungere la proprietà `android:orientation="vertical"` al nodo `LinearLayout`.
4. Incollare il codice seguente nel nodo `LinearLayout`, sostituendo il contenuto corrente:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>Aggiungere MSAL al progetto

1. In Android Studio selezionare **Gradle Scripts** (Script Gradle) > **build.gradle (Module: app)**.
2. In **Dependencies** incollare il codice seguente:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Usare MSAL 

Nelle sezioni seguenti verranno apportate modifiche in `MainAcitivty.java`. Verranno illustrati i singoli passaggi necessari per aggiungere e usare MSAL nell'app.

#### <a name="required-imports"></a>Istruzioni import obbligatorie

Aggiungere le istruzioni import seguenti al progetto: 

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiating-msal"></a>Creazione di un'istanza di MSAL 

Nella classe `MainActivity` verrà creata un'istanza di MSAL con alcune configurazioni relative all'app, inclusi gli ambiti e l'API Web a cui si vuole accedere. 

Copiare le variabili seguenti in `MainActivity`:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Per creare l'istanza di MSAL, copiare il codice seguente nel metodo `onCreate(...)`:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

Il blocco di codice precedente tenta di completare automaticamente l'accesso per gli utenti quando aprono l'applicazione tramite `getAccounts(...)` e, in caso di esito positivo, `acquireTokenSilentAsync(...)`.  Nelle prossime sezioni si implementerà il gestore di callback qualora nessun account abbia effettuato l'accesso. 

#### <a name="use-msal-to-get-tokens"></a>Usare MSAL per ottenere token

A questo punto, è possibile implementare la logica di elaborazione dell'interfaccia utente dell'app e ottenere i token in modo interattivo tramite MSAL. 

MSAL espone due metodi principali per ottenere i token: `acquireTokenSilentAsync` e `acquireToken`.  

`acquireTokenSilentAsync` completa l'accesso per un utente e ottiene i token senza alcuna interazione dell'utente se è presente un account. In caso di esito positivo, MSAL eseguirà l'handoff dei token all'app, invece, in caso di esito negativo, genererà un'eccezione `MsalUiRequiredException`.  Se questa eccezione viene generata o se si vuole offrire all'utente un'esperienza di accesso interattivo (credenziali, MFA o altri criteri di accesso condizionale potrebbero essere necessari o meno), è possibile usare `acquireToken`.  

`acquireToken` mostrerà sempre l'interfaccia utente durante il tentativo di accesso dell'utente e di ottenere i token. Tuttavia, potrebbe usare i cookie di sessione nel browser o un account di Microsoft Authenticator per offrire un'esperienza SSO interattiva. 

Per iniziare, creare i tre metodi dell'interfaccia utente seguenti nella classe `MainActivity`:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Aggiungere quindi un metodo per ottenere l'attività corrente ed elaborare i callback invisibili all'utente e interattivi:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>Usare MSAL per la disconnessione

Verrà ora aggiunto il supporto per la disconnessione dell'app. 

È importante sottolineare che la disconnessione con MSAL rimuove tutte le informazioni note disponibili su un utente da questa applicazione, ma l'utente avrà ancora una sessione attiva nel dispositivo. Se l'utente tenta di accedere nuovamente potrebbero essere richieste interazioni, ma è possibile che non debba immettere nuovamente le credenziali dato che la sessione del dispositivo è attiva. 

Per aggiungere la disconnessione, copiare nell'app il metodo seguente che consente di scorrere tutti gli account e di rimuoverli:

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Chiamare l'API Microsoft Graph

Una volta ottenuto un token, è possibile inviare una richiesta all'API Microsoft Graph. Il token di accesso sarà all'interno di `AuthenticationResult` nel metodo `onSuccess(...)` del callback. Per costruire una richiesta autorizzata, l'app dovrà aggiungere il token di accesso all'intestazione HTTP:

| Chiave dell'intestazione    | value                 |
| ------------- | --------------------- |
| Authorization | Bearer <access-token> |

Per eseguire questa operazione nel codice, aggiungere i due metodi seguenti all'app per chiamare Graph e aggiornare l'interfaccia utente: 

```java
    /* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

Altre informazioni sull'[API Microsoft Graph](https://graph.microsoft.com).

#### <a name="multi-account-applications"></a>Applicazioni con più account

Questa app è stata sviluppata per uno scenario con un singolo account. MSAL supporta anche scenari con più account, ma richiede alcune operazioni aggiuntive per le app. È necessario creare un'interfaccia utente per consentire agli utenti di selezionare l'account da usare per ogni azione che richiede i token. In alternativa, l'app può implementare un'euristica per selezionare l'account da usare tramite il metodo `getAccounts(...)`. 

## <a name="test-your-app"></a>Test dell'app

### <a name="run-locally"></a>Esecuzione locale

Se il codice è stato costruito seguendo le indicazioni precedenti, provare a compilare e distribuire l'app in un dispositivo di test o un emulatore. Dovrebbe essere possibile accedere e ottenere i token per account di Azure AD o account Microsoft personali. Dopo l'accesso dell'utente, questa app visualizzerà i dati restituiti dall'endpoint `/me` di Microsoft Graph. 

Se si verificano problemi, è possibile segnalarli a Microsoft aprendo un problema in questo documento o nella libreria MSAL. 

### <a name="consent-to-your-app"></a>Fornire il consenso all'app

Al primo accesso di qualsiasi utente nell'app, verrà richiesto il consenso per le autorizzazioni richieste da Microsoft Identity Platform.  Anche se la maggior parte degli utenti sarà in grado di fornire il consenso, alcuni tenant di Azure AD hanno disabilitato il consenso dell'utente e sarà quindi necessario che gli amministratori forniscano il consenso per conto di tutti gli utenti.  Per supportare questo scenario, assicurarsi di registrare gli ambiti dell'app nel portale di Azure.

## <a name="help-and-support"></a>Guida e supporto

In caso di problemi con questa esercitazione o con Microsoft Identity Platform, vedere le [informazioni di Guida e supporto tecnico](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).
