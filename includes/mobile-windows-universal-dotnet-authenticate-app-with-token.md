
1. Nel file di progetto MainPage.xaml.cs aggiungere le istruzioni **using** seguenti:

		using System.Linq;		
		using Windows.Security.Credentials;

2. Sostituire il metodo **AuthenticateAsync** con il codice seguente:

        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;

            // This sample uses the Facebook provider.
            var provider = "Facebook";

            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }

            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;

                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;

                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.

                success = true;
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);

                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider,
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);

                    success = true;
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
            message = string.Format("You are now logged in - {0}", user.UserId);
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();

            return success;
        }

	In questa versione di **AuthenticateAsync** l'app tenta di usare le credenziali archiviate in **PasswordVault** per accedere al servizio mobile. Un accesso normale viene eseguito anche quando non sono disponibili credenziali archiviate.

	>[AZURE.NOTE]Un token memorizzato nella cache potrebbe essere scaduto, ma la scadenza del token può avvenire anche dopo l'autenticazione mentre l'app è in uso. Per informazioni su come determinare se un token è scaduto, vedere [Cercare i token di autenticazione scaduti](http://aka.ms/jww5vp). Per una soluzione relativa alla gestione degli errori di autorizzazione relativi ai token scaduti, vedere il post sulla [memorizzazione nella cache e la gestione dei token scaduti nell'SDK per il codice gestito di Servizi mobili di Azure ](hive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx).

3. Riavviare l'app due volte.

	Si noti che al primo avvio viene di nuovo richiesto l'accesso con il provider. Al secondo riavvio, invece, verranno usate le credenziali memorizzate nella cache e l'accesso sarà ignorato.

<!---HONumber=AcomDC_1203_2015-->