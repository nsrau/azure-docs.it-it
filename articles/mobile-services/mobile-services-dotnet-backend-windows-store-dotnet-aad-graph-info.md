<properties
	pageTitle="Accesso alle informazioni di Azure Active Directory Graph (Windows Store) | Microsoft Azure"
	description="Informazioni su come accedere a dati di Azure Active Directory usando l'API Graph nell'applicazione per Windows Store."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="wesmc"/>

# Accesso alle informazioni di Azure Active Directory Graph



[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]

##Panoramica

Come l'altro provider di identità offerto con i Servizi mobili, anche il provider di Azure Active Directory (AAD) supporta un'API Graph avanzata, che può essere usata per l'accesso alla directory a livello di codice. Questa esercitazione consente di aggiornare l'app ToDoList in modo da personalizzare l'esperienza dell'app dell'utente autenticato restituendo informazioni aggiuntive sull'utente recuperate dalla directory tramite l'[API REST Graph].

Per altre informazioni sull'API Azure AD Graph, vedere il [blog del Team di Azure Active Directory Graph].


>[AZURE.NOTE]L'intento di questa esercitazione è di approfondire la conoscenza dell'autenticazione con Azure Active Directory. Si presuppone che sia stata completata l'esercitazione [Aggiungere l'autenticazione all'app] usando il provider di autenticazione di Azure Active Directory. Questa esercitazione continua ad aggiornare l'applicazione TodoItem usata nell'esercitazione [Aggiungere l'autenticazione all'app].




##Prerequisiti

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Aggiungere l'autenticazione all'app]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Esercitazione sull'API personalizzata]<br/>Dimostra come chiamare un'API personalizzata.



## <a name="generate-key"></a>Generare una chiave di accesso per la registrazione dell'app in AAD


Nel corso dell'esercitazione [Aggiungere l'autenticazione all'app] è stata creata una registrazione per l'applicazione integrata quando è stato completato il passaggio relativo alla [registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]. In questa sezione viene generata una chiave da usare nella lettura delle informazioni sulla directory con l'ID client dell'applicazione integrata.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Creare un'API personalizzata GetUserInfo

In questa sezione viene creata l'API personalizzata GetUserInfo che userà l'API di Azure AD Graph per recuperare informazioni aggiuntive sull'utente da AAD.

Se non sono mai state usate le API personalizzate con i Servizi mobili, consultare l'[esercitazione sull'API personalizzata] prima di completare questa sezione.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di back-end .NET e quindi scegliere **Gestisci pacchetti NuGet**.
2. Nella finestra di dialogo Gestione pacchetti NuGet, immettere **ADAL** nei criteri di ricerca per trovare e installare la **libreria di autenticazione di Active Directory** relativa al proprio servizio mobile. Questa esercitazione è stata testata di recente con la versione provvisoria 3.3.205061641-alpha del pacchetto ADAL.


3. In Visual Studio fare clic con il pulsante destro del mouse sulla cartella **Controller** per il progetto del servizio mobile e quindi fare clic su **Aggiungi** per aggiungere un nuovo **controller personalizzato dei Servizi mobili di Microsoft Azure** denominato `GetUserInfoController`. Il client chiamerà questa API per ottenere informazioni sull'utente da Active Directory.

4. Nel nuovo file GetUserInfoController.cs aggiungere le istruzioni `using` seguenti.

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.Threading.Tasks;
		using Newtonsoft.Json;
		using System.IO;

5. Nel nuovo file GetUserInfoController.cs aggiungere la classe `UserInfo` seguente da usare come contenitore delle informazioni che si desidera ottenere da AAD.

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
	                   "otherMails : " + string.Join(", ",otherMails);
	        }
	    }

6. Nel file GetUserInfoController.cs aggiungere le variabili membro seguenti alla classe `GetUserInfoController`.

        private const string AadInstance = "https://login.windows.net/{0}";
        private const string GraphResourceId = "https://graph.windows.net/";
        private const string APIVersion = "?api-version=2013-04-05";

        private string tenantdomain;
        private string clientid;
        private string clientkey;
        private string token = null;


7. Nel file GetUserInfoController.cs aggiungere il metodo `GetAADToken` seguente alla classe.

        private async Task<string> GetAADToken()
        {
            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;
            else
                Services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

    Questo metodo usa le impostazioni dell'app configurate nel servizio mobile sul [portale di gestione di Azure] per ottenere un token con cui accedere ad Active Directory.

7. Nel file GetUserInfoController.cs aggiungere il metodo `GetAADUser` seguente alla classe.

        private async Task<UserInfo> GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetAADUser() : No ServiceUser or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetAADUser() : Could not get AAD credientials for the logged in user.");
                return null;
            }

            if (token == null)
                await GetAADToken();

            if (token == null)
            {
                Services.Log.Error("GetAADUser() : No token.");
                return null;
            }

            // User the AAD Graph REST API to get the user's information
            string url = GraphResourceId + tenantdomain + "/users/" + clientAadCredentials.ObjectId + APIVersion;
            Services.Log.Info("GetAADUser() : Request URL : " + url);
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Method = "GET";
            request.ContentType = "application/json";
            request.Headers.Add("Authorization", token);
            UserInfo userinfo = null;
            try
            {
                WebResponse response = await request.GetResponseAsync();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string userjson = sr.ReadToEnd();
                userinfo = JsonConvert.DeserializeObject<UserInfo>(userjson);
                Services.Log.Info("GetAADUser user : " + userinfo.ToString());
            }
            catch(Exception e)
            {
                Services.Log.Error("GetAADUser exception : " + e.Message);
            }

            return userinfo;
        }

    Questo metodo ottiene l'ID oggetto di Active Directory per l'utente autorizzato, quindi usa l'API REST Graph per ottenere le informazioni sull'utente da Active Directory.


8. Nel file GetUserInfoController.cs sostituire il metodo `Get` con il metodo seguente. Questo metodo restituisce l'entità utente da Azure Active Directory tramite l'API REST Graph e richiede che un utente autorizzato esegua una chiamata all'API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public async Task<UserInfo> Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return await GetAADUser();
        }

9. Salvare le modifiche e compilare il servizio per verificare che non vi siano errori di sintassi.
10. Pubblicare il progetto del servizio mobile nell'account Azure.


## <a name="update-app"></a>Aggiornare l'app per l'uso di GetUserInfo

In questa sezione viene aggiornato il metodo `AuthenticateAsync` implementato nell'esercitazione [Aggiungere l'autenticazione all'app] per chiamare l'API personalizzata e restituire informazioni aggiuntive sull'utente da AAD.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]



## <a name="test-app"></a>Testare l'app

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Passaggi successivi

Nell'esercitazione successiva, [Controllo di accesso basato sui ruoli con AAD in Servizi mobili], si userà il controllo di accesso basato sui ruoli con Azure Active Directory (AAD) per verificare l'appartenenza al gruppo prima di consentire l'accesso.



<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images -->


<!-- URLs. -->
[Aggiungere l'autenticazione all'app]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[portale di gestione di Azure]: https://manage.windowsazure.com/
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Esercitazione sull'API personalizzata]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[blog del Team di Azure Active Directory Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Controllo di accesso basato sui ruoli con AAD in Servizi mobili]: mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac.md

<!---HONumber=September15_HO1-->