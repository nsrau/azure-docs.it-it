1.  In Package Explorer di Eclipse aprire il file ToDoActivity.java e aggiungere le istruzioni import seguenti.

        import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Aggiungere il metodo seguente alla classe **ToDoActivity**:

        private void authenticate() {

            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google,
                    new UserAuthenticationCallback() {

                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {

                            if (exception == null) {
                                createAndShowDialog(String.format(
                                                "You are now logged in - %1$2s",
                                                user.getUserId()), "Success");
                                createTable();
                            } else {
                                createAndShowDialog("You must log in. Login Required", "Error");
                            }
                        }
                    });
        }

    In questo modo viene creato un nuovo metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Google. Viene visualizzata una finestra di dialogo che riporta l'ID dell'utente autenticato. Senza un'autenticazione positiva non è possibile procedere.

    <div class="dev-callout"><b>Nota</b>
	<p>Se si utilizza un provider di identit&agrave; diverso da Google, sostituire il valore passato al metodo di <strong>accesso</strong> riportato in precedenza con uno dei seguenti: <em>MicrosoftAccount</em>, <em>Facebook</em>, <em>Twitter</em> o <em>windowsazureactivedirectory</em>.</p>
</div>

3.  Nel metodo **onCreate** aggiungere la riga di codice seguente dopo il codice che crea l'istanza dell'oggetto `MobileServiceClient`.

        authenticate();

    Questa chiamata avvia il processo di autenticazione.

4.  Spostare il codice rimanente dopo `authenticate();` nel metodo **onCreate** in un nuovo metodo **createTable**, simile al seguente:

        private void createTable() {

            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);

            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }

5.  Nel menu **Run** fare clic su **Run** per avviare l'app e accedere con il provider di identità scelto.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.


