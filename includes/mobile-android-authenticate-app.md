
1. In **Project Explorer** in Android Studio aprire il file ToDoActivity.java e aggiungere le istruzioni import seguenti.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
2. Aggiungere il metodo seguente alla classe **ToDoActivity** :

        // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
        public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;
 
        private void authenticate() {
            // Login using the Google provider.
            mClient.login("Google", "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
         
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            // When request completes
            if (resultCode == RESULT_OK) {
                // Check the request code matches the one we send in the login request
                if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                    MobileServiceActivityResult result = mClient.onActivityResult(data);
                    if (result.isLoggedIn()) {
                        // login succeeded
                        createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                        createTable();
                    } else {
                        // login failed, check the error message
                        String errorMessage = result.getErrorMessage();
                        createAndShowDialog(errorMessage, "Error");
                    }
                }
            }
        }

    In questo modo viene creato un nuovo metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Google. Una finestra di dialogo riporta l'ID dell'utente autenticato. Senza un'autenticazione positiva non è possibile procedere.

    > [!NOTE]
    > Se si usa un provider di identità diverso da Google, sostituire il valore passato al metodo **login** riportato in precedenza con uno dei seguenti: _MicrosoftAccount_, _Facebook_, _Twitter_ o _windowsazureactivedirectory_.

3. Nel metodo **onCreate** aggiungere la riga di codice seguente dopo il codice che crea un'istanza dell'oggetto `MobileServiceClient`.

        authenticate();

    Questa chiamata avvia il processo di autenticazione.
4. Spostare il codice rimanente dopo `authenticate();` nel metodo **onCreate** in un nuovo metodo **createTable**. Il layout è simile al seguente:

        private void createTable() {

            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);

            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

            // Load the items from Azure.
            refreshItemsFromTable();
        }

5. Aggiungere il frammento di _RedirectUrlActivity_ seguente ad _AndroidManifest.xml_ per assicurare che il reindirizzamento funzioni.
 
        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="{url_scheme_of_your_app}"
                    android:host="easyauth.callback"/>
            </intent-filter>
        </activity>

6.  Aggiungere redirectUriScheme a _build.gradle_ dell'applicazione Android.
 
        android {
            buildTypes {
                release {
                    // … …
                    manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
                }
                debug {
                    // … …
                    manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
                }
            }
        }

7. Aggiungere com.android.support:customtabs:23.0.1 alle dipendenze in build.gradle:

      dependencies {        // ...        compile 'com.android.support:customtabs:23.0.1'    }

8. Scegliere **Run app** (Esegui app) dal menu **Esegui** per avviare l'app e accedere con il provider di identità scelto.

Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query sul servizio back-end e aggiornare i dati.
