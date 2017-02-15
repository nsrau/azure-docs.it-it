
1. In **Project Explorer** in Android Studio aprire il file ToDoActivity.java e aggiungere le istruzioni import seguenti.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
2. Aggiungere il metodo seguente alla classe **ToDoActivity** :

        private void authenticate() {
            // Login using the Google provider.

            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();    
                }
            });       
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
5. Scegliere **Run app** (Esegui app) dal menu **Esegui** per avviare l'app e accedere con il provider di identità scelto.

Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query sul servizio back-end e aggiornare i dati.


<!--HONumber=Dec16_HO2-->


