

Le istruzioni seguenti si applicano all'aggiornamento di un'applicazione client di Windows Store, ma è possibile testare l'app su una delle altre piattaforme supportate da Servizi mobili di Azure.


1. In Visual Studio aprire il file MainPage.xaml.cs e aggiungere l'istruzione `using` seguente all'inizio del file.
 
        using System.Net.Http;

2. Nel file MainPage.xaml.cs aggiungere la definizione di classe seguente allo spazio dei nomi per serializzare le informazioni dell'utente.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
            
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ", otherMails);
	        }
	    }


3. Nel file MainPage.xaml.cs aggiornare il metodo `AuthenticateAsync` affinché chiami l'API personalizzata per restituire le informazioni aggiuntive sull'utente da Azure Active Directory.

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    // Change 'MobileService' to the name of your MobileServiceClient instance.
                    // Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);

                    UserInfo userInfo = await App.MobileService
						.InvokeApiAsync<UserInfo>("getuserinfo", HttpMethod.Get, null);

                    message = string.Format("User info for the logged in user...\n\n{0}",userInfo.ToString());
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }

                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


4. Salvare le modifiche e compilare il servizio per verificare che non vi siano errori di sintassi.

<!---HONumber=July15_HO4-->