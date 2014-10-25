<properties linkid="develop-mobile-tutorials-dotnet-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc"></tags>

# Accesso alle informazioni di Azure Active Directory Graph

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/" title="Windows Store C#" class="current">Windows Store C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title=".NET backend" class="current">Back-end .NET</a> |
    <a href="/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="JavaScript backend">Back-end JavaScript</a>
</div>

Come l'altro provider di identità offerto con i Servizi mobili, anche il provider di Azure Active Directory (AAD) supporta una [libreria client Graph][libreria client Graph] avanzata, che può essere usata per l'accesso alla directory a livello di programmazione. In questa esercitazione si aggiornerà l'app ToDoList in modo da personalizzare l'esperienza autenticata dell'utente in base alle informazioni aggiuntive recuperate dalla directory con la [libreria client Graph][libreria client Graph].

> [WACOM.NOTE] L'intento di questa esercitazione è di approfondire la conoscenza dell'autenticazione con Azure Active Directory. Si presuppone che sia stata completata l'esercitazione [Introduzione all'autenticazione][Introduzione all'autenticazione] usando il provider di autenticazione di Azure Active Directory. Questa esercitazione continua ad aggiornare l'applicazione ToDoItem usata nell'esercitazione [Introduzione all'autenticazione][Introduzione all'autenticazione].

In questa esercitazione viene descritta la procedura seguente:

1.  [Generare una chiave di accesso per la registrazione dell'app in AAD][Generare una chiave di accesso per la registrazione dell'app in AAD]
2.  [Creare un'API personalizzata GetUserInfo][Creare un'API personalizzata GetUserInfo]
3.  [Aggiornare l'app da usare per la chiamata all'API personalizzata][Aggiornare l'app da usare per la chiamata all'API personalizzata]
4.  [Test dell'app][Test dell'app]

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Aggiunge un requisito di accesso all'app di esempio TodoList.

-   [Esercitazione sull'API personalizzata][Esercitazione sull'API personalizzata]
    Dimostra come chiamare un'API personalizzata.

## <a name="generate-key"></a>Generare una chiave di accesso per la registrazione dell'app in AAD

Nel corso dell'esercitazione [Introduzione all'autenticazione][Introduzione all'autenticazione] è stata creata una registrazione per l'applicazione integrata quando è stato completato il passaggio [Registrazione delle app per l'utilizzo delle credenziali di accesso di un account Azure Active Directory][Registrazione delle app per l'utilizzo delle credenziali di accesso di un account Azure Active Directory]. In questa sezione viene generata una chiave da usare nella lettura delle informazioni sulla directory con l'ID client dell'applicazione integrata.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="create-api"></a>Creare un'API personalizzata GetUserInfo

In questa sezione viene creata l'API personalizzata GetUserInfo che userà la [libreria client Graph][libreria client Graph] per recuperare informazioni aggiuntive sull'utente da AAD.

Se non sono mai state usate le API personalizzate con i Servizi mobili, consultare l'[esercitazione sull'API personalizzata][Esercitazione sull'API personalizzata] prima di completare questa sezione.

1.  In Visual Studio, fare clic con il pulsante destro del mouse sul progetto di back-end e quindi scegliere **Gestisci pacchetti NuGet**.
2.  Nella finestra di dialogo Gestione pacchetti NuGet, immettere **ADAL** nei criteri di ricerca per trovare e installare la **libreria di autenticazione di Active Directory** relativa al proprio servizio mobile.
3.  Nella finestra di dialogo Gestione pacchetti NuGet, installare anche la **libreria client di Microsoft Azure Active Directory Graph** relativa al proprio servizio mobile.

4.  In Visual Studio, fare clic con il pulsante destro del mouse sulla cartella **Controller** per il progetto del servizio mobile e quindi fare clic su **Aggiungi** per aggiungere un nuovo **controller personalizzato dei Servizi mobili di Microsoft Azure** denominato `GetUserInfoController`. Il client chiamerà questa API per ottenere informazioni sull'utente da Active Directory.

5.  Nel nuovo file GetUserInfoController.cs aggiungere le istruzioni `using` seguenti.

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6.  Nel file GetUserInfoController.cs aggiungere il metodo `GetAADToken` seguente alla classe.

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = authContext.AcquireToken(GraphResourceId, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    Questo metodo usa le impostazioni dell'app configurate nel servizio mobile sul [portale di gestione di Azure][portale di gestione di Azure] per ottenere un token con cui accedere ad Active Directory.

7.  Nel file GetUserInfoController.cs aggiungere il metodo `GetAADUser` seguente alla classe.

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    Questo metodo ottiene l'ID oggetto di Active Directory per l'utente autorizzato, quindi usa la libreria client graph per ottenere le informazioni sull'utente da Active Directory.

8.  Nel file GetUserInfoController.cs sostituire il metodo `Get` con il metodo seguente. Questo metodo restituisce l'oggetto `User` della libreria client graph e richiede che un utente autorizzato chiami l'API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9.  Salvare le modifiche e compilare il servizio per verificare che non vi siano errori di sintassi.
10. Pubblicare il progetto del servizio mobile nell'account Azure.

## <a name="update-app"></a>Aggiornare l'app per l'uso di GetUserInfo

In questa sezione verrà aggiornato il metodo `AuthenticateAsync` implementato nell'esercitazione [Introduzione all'autenticazione][Introduzione all'autenticazione] per chiamare l'API personalizzata e restituire informazioni aggiuntive sull'utente da AAD.

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app][mobile-services-aad-graph-info-update-app]]

## <a name="test-app"></a>Test dell'app

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app][mobile-services-aad-graph-info-test-app]]

## <a name="next-steps"></a>Passaggi successivi

Nell'esercitazione successiva, [Controllo di accesso basato sui ruoli con AAD in Servizi mobili][Controllo di accesso basato sui ruoli con AAD in Servizi mobili], si userà il controllo di accesso basato sui ruoli con Azure Active Directory (AAD) per verificare l'appartenenza al gruppo prima di consentire l'accesso.

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [Windows Store C\#]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/ "Windows Store C#"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/ ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/ "JavaScript backend"
  [libreria client Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [Generare una chiave di accesso per la registrazione dell'app in AAD]: #generate-key
  [Creare un'API personalizzata GetUserInfo]: #create-api
  [Aggiornare l'app da usare per la chiamata all'API personalizzata]: #update-app
  [Test dell'app]: #test-app
  [Esercitazione sull'API personalizzata]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
  [Registrazione delle app per l'utilizzo delle credenziali di accesso di un account Azure Active Directory]: /it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
  [mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
  [Controllo di accesso basato sui ruoli con AAD in Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
