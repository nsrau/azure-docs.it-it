<properties
	pageTitle="Controllo degli accessi in base al ruolo in Servizi mobili e Azure Active Directory (Windows Store) | Microsoft Azure"
	description="Informazioni su come controllare gli accessi in base al ruolo di Azure Active Directory nell'applicazione per Windows Store."
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

# Controllo degli accessi in base al ruolo in Servizi mobili e Azure Active Directory

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##Panoramica

Il controllo degli accessi in base al ruolo consiste nell'assegnazione di autorizzazioni ai ruoli degli utenti. Definisce in modo chiaro i limiti relativi alle azioni che alcune classi di utenti possono o meno eseguire. Questa esercitazione illustra come aggiungere un controllo degli accessi in base al ruolo di base a Servizi mobili di Azure.

Questa esercitazione illustra il controllo degli accessi in base al ruolo, verificando l'appartenenza di ciascun utente a un gruppo Vendite definito in Azure Active Directory (AAD). Il controllo degli accessi viene eseguito con il back-end del servizio mobile .NET usando l'[API REST Graph] per Azure Active Directory. Solo gli utenti che appartengono al gruppo Vendite possono eseguire query sui dati.


>[AZURE.NOTE]L'intento di questa esercitazione è approfondire la conoscenza dell'autenticazione per includere procedure di autorizzazione. Si presuppone che sia stata completata l'esercitazione [Aggiungere l'autenticazione all'app] usando il provider di autenticazione di Azure Active Directory. Questa esercitazione continua ad aggiornare l'applicazione TodoItem usata nell'esercitazione [Aggiungere l'autenticazione all'app].

##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013 in esecuzione su Windows 8.1.
* Completamento dell'esercitazione [Aggiungere l'autenticazione all'app] con il provider di autenticazione di Azure Active Directory.




##Generare una chiave per l'applicazione integrata


Nel corso dell'esercitazione [Aggiungere l'autenticazione all'app] è stata creata una registrazione per l'applicazione integrata quando è stato completato il passaggio relativo alla [registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]. In questa sezione viene generata una chiave da usare nella lettura delle informazioni sulla directory con l'ID client dell'applicazione integrata.

Se è stata eseguita in precedenza l'esercitazione relativa all'[accesso alle informazioni di Azure Active Directory Graph], questo passaggio è già stato completato ed è possibile ignorare questa sezione.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Creare un gruppo Vendite con l'appartenenza

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]



##Creare un attributo di autorizzazione personalizzato nel servizio mobile 

In questa sezione viene creato un nuovo attributo di autorizzazione personalizzato che è possibile usare per eseguire i controlli di accesso sulle operazioni del servizio mobile. L'attributo cerca un gruppo Active Directory basato sul nome del ruolo specificato. Quindi, esegue i controlli di accesso in base all'appartenenza al gruppo.

1. In Visual Studio, fare clic con il pulsante destro del mouse sul progetto di back-end .NET e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nella finestra di dialogo Gestione pacchetti NuGet, immettere **ADAL** nei criteri di ricerca per trovare e installare la **libreria di autenticazione di Active Directory** relativa al proprio servizio mobile. Questa esercitazione è stata testata di recente con la versione provvisoria 3.3.205061641-alpha del pacchetto ADAL.

3. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di servizio mobile e fare clic su **Aggiungi**, quindi **Nuova cartella**. Denominare la nuova cartella **Utilità**.

4. In Visual Studio fare clic con il pulsante destro del mouse sulla nuova cartella **Utilità** e aggiungere un nuovo file della classe denominato **AuthorizeAadRole.cs**.

    ![][0]

5. Nel file AuthorizeAadRole.cs aggiungere le istruzioni `using` seguenti all'inizio del file.

		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using System.Web.Http.Controllers;
		using System.Web.Http.Filters;
		using Newtonsoft.Json;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.IO;

6. In AuthorizeAadRole.cs aggiungere il seguente tipo enumerato allo spazio dei nomi Utilità. In questo esempio viene trattato solo il ruolo **Vendite**. Gli altri sono solo esempi di gruppi che è possibile usare.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

7. In AuthorizeAadRole.cs aggiungere la definizione della classe `AuthorizeAadRole` seguente allo spazio dei nomi Utilità.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;
	        private ApiServices services = null;

	        // Constants used with ADAL and the Graph REST API for AAD
	        private const string AadInstance = "https://login.windows.net/{0}";
	        private const string GraphResourceId = "https://graph.windows.net/";
	        private const string APIVersion = "?api-version=2013-04-05";

	        // App settings pulled from the Mobile Service
	        private string tenantdomain;
	        private string clientid;
	        private string clientkey;
	        private Dictionary<int, string> groupIds = new Dictionary<int, string>();

	        private string token = null;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

	        // private class used to serialize the Graph REST API web response
	        private class MembershipResponse
	        {
	            public bool value;
	        }

            public AadRoles Role { get; private set; }

            // Generate a local dictionary for the role group ids configured as
            // Mobile Service app settings
            private void InitGroupIds()
            {
            }

            // Use ADAL and the authentication app settings from the Mobile Service to
            // get an AAD access token
            private string GetAADToken()
            {
            }

            // Given an AAD user id, check membership against the group associated with the role.
            private bool CheckMembership(string memberId)
            {
            }

            // Called when the user is attempting authorization
            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }



8. In AuthorizeAadRole.cs aggiornare il metodo `InitGroupIds` nella classe `AuthorizeAadRole` come indicato di seguito. Questo metodo crea un mapping di dizionario di ID di gruppo per ogni ruolo.

        private void InitGroupIds()
        {
            string groupId;

            if (services == null)
                return;

            if (!groupIds.ContainsKey((int)AadRoles.Sales))
            {
                if (services.Settings.TryGetValue("AAD_SALES_GROUP_ID", out groupId))
                {
                    groupIds.Add((int)AadRoles.Sales, groupId);
                }
                else
                    services.Log.Error("AAD_SALES_GROUP_ID app setting not found.");
            }
        }


9. In AuthorizeAadRole.cs aggiornare il metodo `GetAADToken` nella classe `AuthorizeAadRole`. Questo metodo usa le impostazioni dell'app archiviate nel servizio mobile per ottenere un token di accesso ad Azure Active Directory da ADAL.

    >[AZURE.NOTE]In ADAL per .NET è inclusa per impostazione predefinita una cache dei token in memoria che consente di ridurre il traffico di rete in eccesso in Active Directory. È tuttavia possibile scrivere un'implementazione della cache o disattivare la cache completamente. Per altre informazioni, visitare il sito Web di [ADAL per .NET].

        // Use ADAL and the authentication app settings from the Mobile Service to get an AAD access token
        private async Task<string> GetAADToken()
        {
            // Try to get the required AAD authentication app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);

            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);
            if (result != null)
                token = result.AccessToken;
            else
                services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

10. In AuthorizeAadRole.cs aggiornare il metodo `CheckMembership` nella classe `AuthorizeAadRole`. Questo metodo riceve l'ID oggetto di un utente. Il metodo usa anche l'API REST di AAD Graph per verificare che l'ID oggetto sia un ID membro del gruppo associato al ruolo selezionato nella classe `AuthorizeAadRole`.

        private bool CheckMembership(string memberId)
        {
            bool membership = false;
            string url = GraphResourceId + tenantdomain + "/isMemberOf" + APIVersion;
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);

            // Use the Graph REST API to check group membership in the AAD
            try
            {
                request.Method = "POST";
                request.ContentType = "application/json";
                request.Headers.Add("Authorization", token);
                using (var sw = new StreamWriter(request.GetRequestStream()))
                {
                    // Request body must have the group id and a member id to check for membership
                    string body = String.Format(""groupId":"{0}","memberId":"{1}"",
                        groupIds[(int)Role], memberId);
                    sw.Write("{" + body + "}");
                }

                WebResponse response = request.GetResponse();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string json = sr.ReadToEnd();
                MembershipResponse membershipResponse = JsonConvert.DeserializeObject<MembershipResponse>(json);
                membership = membershipResponse.value;
            }
            catch (Exception e)
            {
                services.Log.Error("OnAuthorization() exception : " + e.Message);
            }

            return membership;
        }


11. In AuthorizeAadRole.cs aggiornare il metodo `OnAuthorization` nella classe `AuthorizeAadRole` con il codice seguente. Questo codice prevede che l'utente che chiama il servizio mobile abbia eseguito l'autenticazione ad AAD. Ottiene quindi l'ID oggetto AAD dell'utente e verifica l'appartenenza al gruppo Active Directory corrispondente al ruolo.

    >[AZURE.NOTE]È possibile cercare il gruppo Active Directory in base al nome. In molti casi è tuttavia consigliabile memorizzare l'ID gruppo come impostazione app del servizio mobile. Questo perché il nome del gruppo può cambiare mentre l'ID resta invariato.

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed
            // through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
            }

            bool isAuthorized = false;
            try
            {
                // Initialize a mapping for the group id to our enumerated type
                InitGroupIds();

                // Retrieve a AAD token from ADAL
                GetAADToken();
                if (token == null)
                {
                    services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                }
                else
                {
                    // Check group membership to see if the user is part of the group that corresponds to the role
                    if (!string.IsNullOrEmpty(groupIds[(int)Role]))
                    {
                        ServiceUser serviceUser = controller.User as ServiceUser;
                        if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                        {
                            var idents = serviceUser.GetIdentitiesAsync().Result;
                            AzureActiveDirectoryCredentials clientAadCredentials =
                                idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                            if (clientAadCredentials != null)
                            {
                                isAuthorized = CheckMembership(clientAadCredentials.ObjectId);
                            }
                        }
                    }
                }
            }
            catch (Exception e)
            {
                services.Log.Error(e.Message);
            }
            finally
            {
                if (isAuthorized == false)
                {
                    services.Log.Error("Denying access");

                    actionContext.Response = actionContext.Request
                        .CreateErrorResponse(HttpStatusCode.Forbidden,
                            "User is not logged in or not a member of the required group");
                }
            }
        }

12. Salvare le modifiche in AuthorizeAadRole.cs.

##Aggiungere il controllo di accesso basato sui ruoli alle operazioni del database

1. In Visual Studio espandere la cartella **Controller** nel progetto di servizio mobile. Aprire TodoItemController.cs.

2. In TodoItemController.cs aggiungere un'istruzione `using` per lo spazio dei nomi Utilità che contiene l'attributo di autorizzazione personalizzato.

        using todolistService.Utilities;

3. In TodoItemController.cs è possibile aggiungere l'attributo alla classe del controller o ai singoli metodi a seconda di come si vuole controllare l'accesso. Per controllare l'accesso a tutte le operazioni del controller in base allo stesso ruolo, aggiungere semplicemente l'attributo alla classe. Per il test in questa esercitazione, aggiungere l'attributo alla classe come descritto di seguito.

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    Per controllare l'accesso delle sole operazioni di inserimento, aggiornamento ed eliminazione, impostare l'attributo solo in quei metodi, come descritto di seguito.

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }


4. Salvare TodoItemController.cs e compilare il servizio mobile per verificare che non vi siano errori di sintassi.
5. Pubblicare il servizio mobile nell'account Azure.


##Testare l'accesso client

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[Aggiungere l'autenticazione all'app]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure Management Portal]: https://manage.windowsazure.com/
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[accesso alle informazioni di Azure Active Directory Graph]: mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info.md
[ADAL per .NET]: https://msdn.microsoft.com/library/azure/jj573266.aspx

<!---HONumber=September15_HO1-->