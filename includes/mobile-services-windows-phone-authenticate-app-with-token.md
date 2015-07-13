
Nell'esempio precedente è stato illustrato un accesso standard, che richiede al client di contattare sia il provider di identità sia il servizio mobile ogni volta che l'app viene avviata. Non solo questo metodo è inefficiente, ma si potrebbero riscontrare problemi relativi all'uso qualora molti clienti provassero ad avviare l'app contemporaneamente. Un miglior approccio consiste nel memorizzare nella cache il token di autorizzazione restituito dai Servizi mobili e provare a usare questo prima di usare un accesso basato su provider.

>[AZURE.NOTE]È possibile memorizzare nella cache il token rilasciato dai Servizi mobili indipendentemente dal fatto che si usi l'autenticazione gestita dal client o gestita dal servizio. In questa esercitazione viene usata l'autenticazione gestita dal servizio.

1. Nel file di progetto MainPage.xaml.cs aggiungere le istruzioni **using** seguenti:

		using System.IO.IsolatedStorage;
		using System.Security.Cryptography;		

2. Sostituire il metodo **AuthenticateAsync** con il codice seguente:

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            string message;
            // This sample uses the Facebook provider.
            var provider = "Facebook";

            // Provide some additional app-specific security for the encryption.
            byte [] entropy = { 1, 8, 3, 6, 5 };

            // Authorization credential.
            MobileServiceUser user = null;

            // Isolated storage for the app.
            IsolatedStorageSettings settings =
                IsolatedStorageSettings.ApplicationSettings;

            while (user == null)
            {
                // Try to get an existing encrypted credential from isolated storage.                    
                if (settings.Contains(provider))
                {
                    // Get the encrypted byte array, decrypt and deserialize the user.
                    var encryptedUser = settings[provider] as byte[];
                    var userBytes = ProtectedData.Unprotect(encryptedUser, entropy);
                    user = JsonConvert.DeserializeObject<MobileServiceUser>(
                        System.Text.Encoding.Unicode.GetString(userBytes, 0, userBytes.Length));
                }
                if (user != null)
                {
                    // Set the user from the stored credentials.
                    App.MobileService.CurrentUser = user;

                    try
                    {
                        // Try to return an item now to determine if the cached credential has expired.
                        await App.MobileService.GetTable<TodoItem>().Take(1).ToListAsync();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        if (ex.Response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
                        {
                            // Remove the credential with the expired token.
                            settings.Remove(provider);
                            user = null;
                            continue;
                        }
                    }
                }
                else
                {
                    try
                    {
                        // Login with the identity provider.
                        user = await App.MobileService
                            .LoginAsync(provider);

                        // Serialize the user into an array of bytes and encrypt with DPAPI.
                        var userBytes = System.Text.Encoding.Unicode
                            .GetBytes(JsonConvert.SerializeObject(user));
                        byte[] encryptedUser = ProtectedData.Protect(userBytes, entropy);

                        // Store the encrypted user credentials in local settings.
                        settings.Add(provider, encryptedUser);
                        settings.Save();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        message = "You must log in. Login Required";
                    }
                }
                message = string.Format("You are now logged in - {0}", user.UserId);
                MessageBox.Show(message);
            }
        }

	In questa versione di **AuthenticateAsync** l'app tenta di usare le credenziali archiviate in formato crittografato nella risorsa di archiviazione locale per accedere al servizio mobile. Viene inviata una semplice query per verificare che il token archiviato non sia scaduto. Quando viene restituito 401, viene effettuato un normale tentativo di accesso basato su provider. Un accesso normale viene eseguito anche quando non sono disponibili credenziali archiviate.

	>[AZURE.NOTE]Questa app verifica la presenza di token scaduti durante l'accesso, ma la scadenza del token può avvenire dopo l'autenticazione mentre l'app è in uso. Per una soluzione relativa alla gestione degli errori di autorizzazione relativi ai token scaduti, vedere il post sulla [memorizzazione nella cache e la gestione dei token scaduti nell'SDK per il codice gestito di Servizi mobili di Azure ](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx).
	
3. Riavviare l'app due volte.

	Si noti che al primo avvio viene di nuovo richiesto l'accesso con il provider. Al secondo riavvio, invece, verranno usate le credenziali memorizzate nella cache e l'accesso sarà ignorato.

<!---HONumber=62-->