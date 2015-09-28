<properties
	pageTitle="Anteprima Azure AD B2C: chiamata di un’API Web da un’applicazione | Microsoft Azure"
	description="Questo articolo illustra come creare un’app Android To-Do List che chiama un’API Web node.js web tramite token di connessione OAuth 2.0. Sia l’app Android che l’API Web utilizzano Azure AD B2C per gestire le identità degli utenti e autenticare gli utenti."
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
	ms.date="09/16/2015"
	ms.author="brandwe"/>

# Anteprima di Azure AD B2C: Chiamata di un'API Web da un'applicazione Android

Azure AD B2C consente di aggiungere potenti funzionalità di gestione delle identità self-service alle app Android e alle API Web con pochi brevi passaggi. Questo articolo descrive come creare un'app Android "To-Do List" che chiama un’API Web node.js web tramite token di connessione OAuth 2.0. Sia l'app Android che l'API Web usano Azure AD B2C per gestire le identità utente e autenticare gli utenti.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]Questa Guida rapida richiede come prerequisito che un'API Web sia protetta da Azure AD con B2C per poter funzionare completamente. È stata creata una da utilizzare sia per .Net che per node. js. Questa procedura dettagliata presuppone che l’API Web Node. js di esempio sia configurata. Consultare l’[esercitazione API Web Azure AD B2Cper Node.js](active-directory-b2c-devquickstarts-api-node.md).

> [AZURE.NOTE]Questo articolo non descrive come implementare l’accesso, l’iscrizione e la gestione del profilo con Azure AD B2C, ma illustra la chiamata delle API Web dopo che l'utente ha già effettuato l'autenticazione. Se non è già stato fatto, iniziare con l'[esercitazione introduttiva per un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) per acquisire le nozioni di base su Azure AD B2C.

Per i client Android che devono accedere a risorse protette, Azure AD fornisce Active Directory Authentication Library (ADAL). La funzione di ADAL è di permettere all'app di ottenere facilmente i token di accesso. Per far capire quanto è semplice, verrà compilata un'applicazione Android To-Do List che:

-	Ottiene i token di accesso per la chiamata all'API To-Do List con il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Ottiene l'elenco attività (To-Do List) dell'utente.
-	Disconnette gli utenti.



### Passaggio 1: Ottenere una directory di Azure AD B2C

Prima di poter utilizzare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, passare a [creare una directory B2C](active-directory-b2c-get-started.md) prima di continuare.

### Passaggio 2:Creare un'applicazione

A questo punto, è necessario creare un'app nella directory B2C, che fornisce ad Azure AD alcune informazioni necessarie per comunicare in modo sicuro con l'app. Sia l'app Web che l'API Web saranno rappresentate da un singolo **ID applicazione** in questo caso, poiché includono un'app per la logica. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md): Assicurarsi di

- Includere un'**app Web/API Web** nell'applicazione
- Specificare `http://localhost:3000/auth/openid/return` come **URL di risposta**. Si tratta dell'URL predefinito per questo esempio di codice.
- Creare un **Segreto applicazione** per l'applicazione e prenderne nota, perché verrà richiesto a breve.
- Copiare l'**ID applicazione** assegnato all'app, perché anche questo verrà richiesto a breve.

### Passaggio 3: Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [**criteri**](active-directory-b2c-reference-policies.md) specifici. Questa app contiene tre esperienze di identità: iscrizione, accesso e accesso con Facebook. Sarà necessario creare i criteri per ciascun tipo, come descritto nell'articolo [riferimento ai criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere il **Nome visualizzato** e alcuni altri attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere il **Nome visualizzato** e l'**ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **Nome** di ciascun criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`. Sarà necessario usare i nomi dei criteri a breve. 

Dopo aver creato i tre criteri, è possibile passare alla creazione dell'app.

Si noti che questo articolo non illustra come usare i criteri appena creati. Per altre informazioni sul funzionamento dei criteri in Azure AD B2C, iniziare con l'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

### Passaggio 4: Scaricare il codice

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Per creare l'esempio passo passo, è possibile [scaricare un progetto scheletro come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip) o clonare lo scheletro:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**Il download dello scheletro è necessario per completare questa esercitazione.** Data la complessità dell'implementazione di un'applicazione pienamente funzionante su Android, lo **scheletro** prevede un codice UX che sarà eseguito dopo aver completato l'esercitazione seguente. Si tratta di una misura che consente di risparmiare tempo per gli sviluppatori. Il codice UX non è pertinente all'argomento di aggiunta B2C a un'applicazione Android.

L'app completata è anche [disponibile come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) o nel ramo `complete` dello stesso repository.


Per compilare con Maven, è possibile usare pom.xml al livello principale.


  * Seguire i passaggi nella [sezione dei prerequisiti per la configurazione di Maven per Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  * Emulatore di configurazione con SDK 21.
  * Passare alla cartella radice in cui è stato clonato il repository.
  * Eseguire il comando: mvn clean install
  * Passare alla directory dell'esempio di Avvio rapido: cd samples\\hello
  * Eseguire il comando: mvn android:deploy android:run
  * Dovrebbe essere visualizzato l'avvio dell'app.
  * Immettere le credenziali dell'utente test per provare.

Oltre al pacchetto AAR, verranno inviati anche i pacchetti JAR.

### Passaggio 5: Scaricare ADAL per Android e aggiungerlo all'area di lavoro di Eclipse

Per semplificare l'operazione, sono disponibili più opzioni per usare questa libreria nel progetto Android:

* È possibile usare il codice sorgente per importare la libreria in Eclipse e collegarla all'applicazione.
* Se si usa Android Studio, è possibile usare il formato di pacchetto *AAR* e fare riferimento ai file binari.

####Opzione 1: Codice sorgente tramite Git

Per ottenere il codice sorgente dell'SDK tramite Git, digitare semplicemente:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"

####Opzione 2: File binari tramite Gradle

È possibile ottenere i file binari dal repository centrale di Maven. Il pacchetto AAR può essere incluso come segue nel progetto in Android Studio:

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
    compile('com.microsoft.aad:adal:2.0-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0-alpha
}
```

####Opzione 3: AAR tramite Maven

Se si usa il plug-in m2e in Eclipse, è possibile specificare la dipendenza nel file pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0-alpha</version>
    <type>aar</type>
</dependency>
```


####Opzione 5: Pacchetto JAR nella cartella libs
È possibile ottenere il file JAR dal repository Maven e inserirlo nella cartella *libs* del progetto. È necessario copiare anche le risorse necessarie per il progetto, perché non sono incluse nei pacchetti JAR.


### Passaggio 6: Aggiungere i riferimenti ad ADAL per Android al progetto


2. Aggiungere un riferimento al progetto e specificarlo come una libreria Android. In caso di dubbi su come eseguire questa operazione, [fare clic qui per altre informazioni](http://developer.android.com/tools/projects/projects-eclipse.html).

3. Aggiungere la dipendenza del progetto per il debug alle impostazioni del progetto.

4. Aggiornare il file AndroidManifest.xml del progetto includendo:

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

### Passaggio 7: Aggiungere il codice per la chiamata ADAL per Android

Creare un'attività principale e chiamarla `ToDoActivity`.

È necessario inizializzare l'attività e aggiungere alcuni pulsanti che consentano di controllare l'interfaccia utente. Anche questa è piuttosto semplice e molto familiare il codice Android è stato scritto prima:

```
public class ToDoActivity extends Activity {

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

    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();
```

Successivamente, nella stessa classe (non chiudere ancora la parentesi quadra) è possibile aggiungere i pulsanti per l'interfaccia utente. Si noti che sono stati aggiunti i pulsanti per l’accesso con Facebook e con l’indirizzo di posta elettronica. Saranno utilizzati i criteri definiti nel `constants.java` file:

```
        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

    ```
Create an instance of AuthenticationContext at your main Activity:

    ```Java
 		 mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false, InMemoryCacheStore.getInstance());
            mAuthContext.getCache().removeAll();

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
    ```
  * NOTE: mContext is a field in your activity
 
 Let's also define our Result method `onActivityResult` that will be called when we have a result from the callback we'll write later:
 
 ```
 @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
 ```

Next, let's define our callback:

    ```Java
    mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY, Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
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

                            if (result != null && !result.getAccessToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
        }
        Toast.makeText(getApplicationContext(), TAG + "done", Toast.LENGTH_SHORT).show();
    }
        ```
        
 You may notice that this relies on methods we haven't written yet, such as `updateLoggedInUser()` and `getTasks()`. We'll write those below.	You can safely ignore the errors in Android Studio for now.

Explanation of the parameters:

  * ***SCOPES*** is required and is the scopes you are trying to reqeust access for. For the B2C preview this is the same as the Clientid but will change in the future.
  * ***POLICY*** is the policy for while you wish to authenticate the user. 
  * ***CLIENT_ID*** is required and comes from the AzureAD Portal.
  * You can setup redirectUri as your packagename. It is not required to be provided for the acquireToken call.
  * ***USER_HINT*** is the way we look up if the user is already in the cache and prompt the user if they are not found or the access token is invalid.
  * ***PROMPT*** helps to ask for credentials to skip cache and cookie.
  * ***Callback*** will be called after authorization code is exchanged for a token.

  The Callback will have an object of AuthenticationResult which has accesstoken, date expired, and idtoken info.

> [AZURE.NOTE]	Microsoft Intune's Company portal app provides the broker component and may be installed on the user's device. Developers should be prepared to allow for it's use as it provides SSO across all applications on the device. ADAL for Android will use the broker account if there is one user account created in the Authenticator. To use the broker, the developer needs to register a special redirectUri for broker usage. RedirectUri is in the format of msauth://packagename/Base64UrlencodedSignature. You can get your redirecturi for your app using the script `brokerRedirectPrint.ps1` or use API call `mContext.getBrokerRedirectUri()`. The signature is related to your signing certificates from the Google Play store.

 A Developer can skip the broker user with:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.WARNING] In order to reduce the complexity of this B2C Quickstart, we have opted in our sample to skip the broker. However, this is not recommended.

Next, let's create some helper methods that will get the token alone during our authentication calls to the Task API:

```
private void getToken(final AuthenticationCallback callback) {

        // one of the acquireToken overloads
        mAuthContext.acquireToken(onnstants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY,
                Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                "nux=1&" + Constants.EXTRA_QP, callback);
    }

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {
        Constants.CURRENT_RESULT = newToken;
    }
    
```

Finally, Your app manifest should have permissions to use AccountManager accounts: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


### Step 8. Call the Task API

Now that we have our Activity wired up and ready to go to do the heavy lifting of grabbing tokens, let's write our API to access the task server.

Our `getTasks` provides an array that represents the tasks in our server 
Our `addTask` and `deleteTask` do the subsequent action and return TRUE or FALSE if it was successful.

Let's write our `getTask` first:

```
private void getTasks() { if (Constants.CURRENT\_RESULT == null || Constants.CURRENT\_RESULT.getAccessToken().isEmpty()) return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(Constants.CURRENT_RESULT.getAccessToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }
 ```
 
 Scrivere inoltre un metodo che consenta di inizializzare le nostre tabelle alla prima esecuzione:
 
 ``` private void initAppTables() { try { // Get the Mobile Service Table instance to use // mToDoTable = mClient.getTable(WorkItem.class); // mToDoTable.TABvLES\_URL = "/api/"; //mTextNewToDo = (EditText)findViewById(R.id.listViewToDo);

            // Create an adapter to bind the items with the view
            //mAdapter = new WorkItemAdapter(ToDoActivity.this, R.layout.listViewToDo);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 You'll see that this code requires some additional methods to do it's work. Let's write those now.
 
 ### Create endpoint URL generator
 
 We need to generate the endpoint URL that we'll be connecting to. Let's do that in the same class file:
 
 
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


Note that we add the access token to the request in the following code:

### Step 9: Let's write some UX methods

Android requires us to handle some callbacks in order to operate the app. These are `createAndShowDialog` and `onResume()`. This is pretty simple and very familiar if you've written Android code before. 

Let's write those now:

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

And now manage our dialog callbacks:


```
/**
     *Crea una finestra di dialogo e la visualizza * * @param exception Eccezione da visualizzare nella finestra di dialogo * @param title Titolo della finestra di dialogo */ private void createAndShowDialog(Exception exception, String title) { createAndShowDialog(exception.toString(), title); }

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
    


### Passaggio 10: Eseguire l'app di esempio

Infine, compilare ed eseguire l'applicazione in Android Studio o Eclipse. Effettuare l'iscrizione o l'accesso all'app e creare le attività per l'utente connesso. Disconnettersi ed eseguire di nuovo l'accesso con un account utente diverso e creare le attività per l’utente in questione.

Osservare il modo in cui le attività sono archiviate per utente nell'API, poiché l'API estrae l'identità dell'utente dai token di accesso ricevuto.

Come riferimento, l'esempio completato [è disponibile in un file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


<!--

### Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

### Important Information


#### Encryption

ADAL encrypts the tokens and store in SharedPreferences by default. You can look at the StorageHelper class to see the details. Android introduced AndroidKeyStore for 4.3(API18) secure storage of private keys. ADAL uses that for API18 and above. If you want to use ADAL for lower SDK versions, you need to provide secret key at AuthenticationSettings.INSTANCE.setSecretKey

#### Session cookies in Webview

Android webview does not clear session cookies after app is closed. You can handle this with sample code below:
```java
CookieSyncManager.createInstance(getApplicationContext()); CookieManager cookieManager = CookieManager.getInstance(); cookieManager.removeSessionCookie(); CookieSyncManager.getInstance().sync();
```Ulteriori informazioni sui cookie: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=Sept15_HO3-->