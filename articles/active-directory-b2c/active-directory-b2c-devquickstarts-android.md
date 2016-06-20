<properties
	pageTitle="Anteprima di Azure Active Directory B2C: Chiamare un'API Web da un'applicazione Android | Microsoft Azure"
	description="Questo articolo descrive come creare un'app ";elenco attività"; Android che chiama un'API Web Node.js usando token di connessione OAuth 2.0. Sia l'app Android che l'API Web usano Azure Active Directory B2C per gestire le identità utente e autenticare gli utenti."
	services="active-directory-b2c"
	documentationCenter="android"
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="brandwe"/>

# Anteprima di Azure AD B2C: Chiamare un'API Web da un'applicazione Android

Azure Active Directory (Azure AD) B2C permette di aggiungere funzionalità efficaci di gestione delle identità self-service alle app Android e alle API Web con pochi brevi passaggi. Questo articolo descrive come creare un'app Android "To-Do List" che chiama un'API Web Node.js usando token di connessione OAuth 2.0. Sia l'app Android che l'API Web usano Azure AD B2C per gestire le identità utente e autenticare gli utenti.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Per poter usare questa guida introduttiva, è necessario avere un'API Web protetta da Azure AD con B2C. Ne è stata compilata una da usare sia per .NET che per Node.js. Questa procedura dettagliata presuppone che l'esempio di API Web per Node.js sia configurato. Per altre informazioni, vedere l'[esercitazione sull'API Web di Azure AD B2C per Node.js](active-directory-b2c-devquickstarts-api-node.md).

Per i client Android che devono accedere a risorse protette, Azure AD fornisce Active Directory Authentication Library (ADAL). La funzione di ADAL è di permettere all'app di ottenere facilmente i token di accesso. Per far capire quanto è semplice, in questa guida verrà compilata un'applicazione Android To-Do List che:

- Ottiene i token di accesso che chiamano un'API To-Do List usando il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
- Ottiene gli elenchi attività degli utenti.
- Disconnette gli utenti.

> [AZURE.NOTE] Questo articolo non descrive come implementare la gestione dell'iscrizione, dell'accesso e del profilo con Azure AD B2C, ma illustra come chiamare le API Web dopo che l'utente ha effettuato l'autenticazione. Se non è già stato fatto, iniziare con l'[esercitazione introduttiva per la compilazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) per acquisire le nozioni di base su Azure AD B2C.

## Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire in questa guida.

## Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. In questo caso, sia l'app che l'API Web sono rappresentate da un singolo **ID applicazione** perché includono un'unica app per la logica. Per creare un'app, seguire [questa procedura](active-directory-b2c-app-registration.md): Assicurarsi di:

- Includere un'**app Web**/**API Web* nell'applicazione.
- Immettere `urn:ietf:wg:oauth:2.0:oob` come **URL di risposta**. Si tratta dell'URL predefinito per questo esempio di codice.
- Creare un **segreto applicazione** per l'applicazione e prenderne nota. Sarà necessario più avanti. Si noti che prima di usare questo valore è necessario [inserire un carattere di escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
- Copiare l'**ID applicazione** assegnato all'app. Sarà necessario più avanti.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Creare i criteri

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md) specifici. Questa app contiene tre esperienze di identità: iscrizione, accesso e accesso con Facebook. È necessario creare i criteri per ogni tipo, come descritto nell'[articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri assicurarsi di:

- Scegliere il **Nome visualizzato** e altri attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere **Nome visualizzato** e **ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **Nome** di ogni criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`. I nomi dei criteri saranno necessari in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo avere creato i tre criteri, è possibile passare alla compilazione dell'app.

Si noti che questo articolo non illustra come usare i criteri appena creati. Per informazioni sul funzionamento dei criteri in Azure AD B2C, iniziare dall'[esercitazione introduttiva per la compilazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Scaricare il codice

Il codice per questa esercitazione è [disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Per compilare l'esempio, è possibile [scaricare il progetto struttura come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **È necessario scaricare la struttura per completare questa esercitazione.** Data la complessità dell'implementazione di un'applicazione Android pienamente funzionale, la struttura contiene codice dell'esperienza utente che verrà eseguito al termine di questa esercitazione. Si tratta di una misura che permette agli sviluppatori di risparmiare tempo. Il codice dell'esperienza utente non è pertinente all'argomento relativo all'aggiunta di B2C a un'applicazione Android.

L'app completata è anche [disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Per compilare con Maven, è possibile usare `pom.xml` al livello principale.

  1. Seguire la procedura illustrata nella [sezione relativa ai prerequisiti per configurare Maven per Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Configurare un emulatore con SDK 21.
  3. Passare alla cartella radice in cui è stato clonato il repository.
  4. Eseguire il comando `mvn clean install`.
  5. Passare alla directory dell'esempio di Avvio rapido `cd samples\hello`.
  6. Eseguire il comando `mvn android:deploy android:run`.

Dovrebbe essere visualizzato l'avvio dell'app. Immettere le credenziali dell'utente test per provare.

Oltre al pacchetto Android Archive (AAR), verranno inviati anche i pacchetti Java Archive (JAR).

## Scaricare la libreria Active Directory Authentication Library (ADAL) per Android e aggiungerla all'area di lavoro

Sono disponibili alcune opzioni per usare questa libreria nel progetto Android:

* È possibile usare il codice sorgente per importare la libreria in Eclipse e collegarla all'applicazione.
* Se si usa Android Studio, è possibile usare il formato di pacchetto AAR e fare riferimento ai file binari.

### Opzione 1: Binari tramite Gradle (scelta consigliata)

È possibile ottenere i file binari dal repository centrale di Maven. È possibile includere il pacchetto AAR nel progetto in Android Studio, ad esempio in `build.gradle`, in questo modo:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### Opzione 2: AAR tramite Maven

Se si usa il plug-in `m2e` in Eclipse, è possibile specificare la dipendenza nel file `pom.xml`:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### Opzione 3: Codice sorgente tramite Git (ultima soluzione)

Per ottenere il codice sorgente dell'SDK tramite Git, immettere:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Usare la **convergenza** dei rami.

## Definire il file di configurazione

Usare la configurazione definita in precedenza nel portale B2C per configurare il progetto Android.

Aprire `helpes/Constants.java` e specificare i valori seguenti:

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
- `SCOPES`: ambiti passati al server da richiedere al server quando un utente esegue l'accesso. Per l'anteprima di B2C si passa `client_id`, che in futuro diventerà `read scopes`. Questo documento verrà aggiornato in seguito a questa modifica.
- `ADDITIONAL_SCOPES`: ambiti aggiuntivi che si vuole usare per l'applicazione. Verranno usati in futuro.
- `CLIENT_ID`: ID applicazione ottenuto dal portale.
- `REDIRECT_URL`: URL di reindirizzamento in cui verrà pubblicato il token.
- `EXTRA_QP`: qualsiasi elemento aggiuntivo che si vuole passare al server in formato codificato come URL.
- `FB_POLICY`: criteri che vengono richiamati. Questa è la parte più importante della procedura dettagliata.
- `EMAIL_SIGNIN_POLICY`: criteri che vengono richiamati. Questa è la parte più importante della procedura dettagliata.
- `EMAIL_SIGNUP_POLICY`: criteri che vengono richiamati. Questa è la parte più importante della procedura dettagliata.

## Aggiungere al progetto riferimenti alla libreria Active Directory Authentication Library (ADAL) per Android

> [AZURE.NOTE]	La libreria ADAL per Android usa un modello basato su intent per richiamare l'autenticazione. Gli intent si basano sull'app per svolgere il lavoro. L'intero esempio e tutta la libreria ADAL per Android illustrano come gestire gli intent e come passare le informazioni tra di essi.

Prima di tutto comunicare ad Android il layout dell'applicazione e gli oggetti intent che si vuole usare. Questi intent verranno illustrati in dettaglio più avanti in questa esercitazione.

Aggiornare il file `AndroidManifest.xml` del progetto per includere tutti gli intent:

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Come si può notare, si definiscono cinque attività che verranno usate tutte.

- `AuthenticationActivity`: proviene da ADAL e fornisce la visualizzazione Web per l'accesso.
- `LoginActivity`: visualizza i criteri di accesso e i pulsanti per ogni criterio.
- `SettingsActivity`: viene usata per modificare le impostazioni dell'app in fase di esecuzione.
- `AddTaskActivity`: viene usata per aggiungere all'API REST attività protette da Azure AD.
- `ToDoActivity`: attività principale che visualizza le attività.

## Creare l'attività di accesso

Creare un'attività principale denominata `LoginActivity`.

Creare un file denominato `LoginActivity.java`.

È necessario inizializzare l'attività e aggiungere alcuni pulsanti per il controllo dell'interfaccia utente. Se si ha esperienza con la scrittura di codice per Android, si tratta di un'operazione semplice.

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
A questo punto sono stati creati i pulsanti che chiamano l'intent `ToDoActivity` che chiama ADAL quando è necessario un token. usando l'attività come riferimento e parametro aggiuntivo. Questo parametro aggiuntivo è passato dal metodo `intent.putExtra()`. Per definire `"thePolicy"`, usare quanto specificato in `Constants.java`. In questo modo, viene comunicato all'intent qual è il criterio da richiamare durante l'autenticazione.

## Creare l'attività relativa alle impostazioni

Si tratta di un'attività che popola l'interfaccia utente delle impostazioni.

Creare un file denominato `SettingsActivity.java` per operazioni semplici di creazione, lettura, aggiornamento ed eliminazione.

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

	//private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_settings);

        loadSettings();
//		checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//		checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


	}

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

	private void saveSettings(String key, boolean value) {
		SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
				Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
		Editor prefsEditor = prefs.edit();
		prefsEditor.putBoolean(key, value);
		prefsEditor.commit();
	}
}
```

## Creare l'attività di aggiunta di azioni

È possibile usare questa attività per aggiungere un'azione all'endpoint API REST.

Creare un file denominato `AddTaskActivity.java` e scrivere il codice seguente.

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## Creare l'attività relativa all'elenco attività

È l'attività più importante. È possibile usarla per ottenere un token da Azure AD per un criterio e quindi usare il token per chiamare il server API REST delle attività.

Creare un file denominato `ToDoActivity.java` e scrivere il codice seguente. Le chiamate verranno illustrate più avanti.

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 Come è possibile osservare, questo codice si basa su metodi non ancora scritti che includono `updateLoggedInUser()`, `clearSessionCookie()` e `getTasks()`. e che verranno scritti più avanti in questa guida. È possibile ignorare gli errori in Android Studio per adesso.

Descrizione dei parametri:

  - `SCOPES`: è obbligatorio e indica gli ambiti per i quali si vuole richiedere l'accesso. Per l'anteprima di B2C, corrisponde a `client_id`, ma in futuro verrà modificato.
  - `POLICY`: criterio da usare per autenticare l'utente.
  - `CLIENT_ID`: è obbligatorio e proviene dal portale di Azure AD.
  - `redirectUri`: può essere configurato come nome del pacchetto. Non è obbligatorio specificarlo per la chiamata ad `acquireToken`.
  - `getUserInfo()`: consente di verificare se un utente è già presente nella cache. Il parametro descrive anche come chiedere un utente se l'utente non viene trovato o se il token di accesso dell'utente non è valido. Questo metodo verrà scritto più avanti in questa guida.
  - `PromptBehavior.always`: facilita la richiesta delle credenziali per ignorare la cache e i cookie.
  - `Callback`: viene chiamato dopo lo scambio di un codice di autorizzazione con un token. Avrà un oggetto `AuthenticationResult`, che contiene il token di accesso, la data di scadenza e le informazioni sul token ID.

> [AZURE.NOTE]	L'app del portale aziendale di Microsoft Intune fornisce il componente broker che può essere installato nel dispositivo dell'utente. L'app consente l'accesso Single Sign-On (SSO) in tutte le applicazioni presenti nel dispositivo. Gli sviluppatori dovranno prepararsi a consentirne l'uso di Intune. La libreria ADAL per Android userà l'account broker se ne è stato creato uno nell'autenticatore. Per usare il broker, lo sviluppatore deve registrare un `redirectUri` speciale per il broker da usare. `redirectUri` è nel formato msauth://packagename/Base64UrlencodedSignature. È possibile ottenere `redirectUri` per l'app usando lo script `brokerRedirectPrint.ps1` oppure usando la chiamata API `mContext.getBrokerRedirectUri()`. La firma è relativa ai certificati di firma da Google Play Store.

 È possibile ignorare l'utente broker usando:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Per ridurre la complessità di questa esercitazione introduttiva su B2C, è stato dato il consenso per ignorare il broker nell'esempio.

Ora creare alcuni metodi di supporto per ottenere il token durante le chiamate di autenticazione all'API dell'attività.

Nello stesso file `ToDoActivity.java` scrivere il codice seguente.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Aggiungere anche i metodi per impostare e ottenere `AuthenticationResult`, contenente il token, in `Constants` globale. Anche se `ToDoActivity.java` usa `sResult` nei flussi, è necessario aggiungere questi metodi. In caso contrario, le altre attività non avranno accesso al token per svolgere il lavoro, ad esempio aggiungere un'azione in `AddTaskActivity.java`.

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## Creare un metodo per restituire un identificatore utente

La libreria ADAL per Android rappresenta l'utente sotto forma di oggetto `UserIdentifier`. che gestisce l'utente. È possibile usare l'oggetto per determinare se lo stesso utente viene usato nelle chiamate. Usando queste informazioni, è possibile basarsi sulla cache, invece di effettuare una nuova chiamata al server. Per semplificare l'operazione, è stato creato il metodo `getUserInfo()` che restituisce `UserIdentifier`. È possibile usarlo con `acquireToken()`. È stato creato anche un metodo `getUniqueId()` che restituisce l'ID di `UserIdentifier` nella cache.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### Scrivere metodi di supporto

Scrivere ora alcuni metodi di supporto per cancellare i cookie e fornire `AuthenticationCallback`. Questi metodi vengono usati esclusivamente nell'esempio per verificare di essere nello stato originario quando si chiama l'attività `ToDo`.

Nello stesso file denominato `ToDoActivity.java` scrivere il codice seguente.

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## Chiamare l'API delle attività

Ora che l'attività è pronta per ottenere i token, è possibile scrivere l'API per accedere al server delle azioni.

`getTasks` visualizza una matrice che rappresenta le attività nel server

Iniziare con `getTasks`.

Nello stesso file denominato `ToDoActivity.java` scrivere il codice seguente.

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

Scrivere anche un metodo che inizializzerà le tabelle alla prima esecuzione.

Nello stesso file denominato `ToDoActivity.java` scrivere il codice seguente.

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

È possibile notare che questo codice richiede altri metodi per funzionare. Verranno scritti più avanti.

### Creare un generatore dell'URL dell'endpoint

È necessario generare l'URL dell'endpoint a cui verrà effettuata la connessione. Eseguire questa operazione nello stesso file di classe.

**Nello stesso file** denominato `ToDoActivity.java` scrivere il codice seguente.

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


Si noti che il token di accesso viene aggiunto alla richiesta nel codice illustrato nella sezione successiva.

## Scrivere i metodi per l'esperienza utente

Per il funzionamento dell'app, Android richiede la gestione di alcuni callback. Sono `createAndShowDialog` e `onResume()`. Se si ha esperienza con la scrittura di codice per Android, si tratta di un'operazione semplice.

Nello stesso file denominato `ToDoActivity.java` scrivere il codice seguente.

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

Gestire quindi i callback della finestra di dialogo.

Nello stesso file denominato `ToDoActivity.java` scrivere il codice seguente.

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

Verrà avviata la compilazione di un file `ToDoActivity.java`. e anche dell'intero progetto.

## Eseguire l'app di esempio

Infine, compilare ed eseguire l'applicazione in Android Studio o Eclipse. Eseguire l'iscrizione o l'accesso all'app. Creare le attività per l'utente connesso. Disconnettersi ed eseguire di nuovo l'accesso con un account utente diverso e quindi creare le attività per tale utente.

Si noti che le attività sono archiviate per utente nell'API, perché l'API estrae l'identità dell'utente dal token di accesso che riceve.

Come riferimento, l'esempio completo [è disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). È anche possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## Informazioni importanti


### Crittografia

ADAL esegue la crittografia dei token e li archivia in `SharedPreferences` per impostazione predefinita. È possibile esaminare la classe `StorageHelper` per visualizzare i dettagli. Android ha introdotto **AndroidKeyStore 4.3 (API18)** per l'archiviazione protetta delle chiavi private. ADAL lo usa per API18 e versioni successive. Per usare ADAL per versioni precedenti dell'SDK, è necessario specificare una chiave privata in `AuthenticationSettings.INSTANCE.setSecretKey`.

### Cookie di sessione nella visualizzazione Web

La visualizzazione Web di Android non cancella i cookie di sessione dopo la chiusura dell'app. È possibile gestire questo aspetto con il codice di esempio seguente.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[Altre informazioni sui cookie](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

<!---HONumber=AcomDC_0608_2016-->