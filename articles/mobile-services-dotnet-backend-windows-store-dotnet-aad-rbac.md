<properties urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Controllo di accesso basato sui ruoli nei servizi mobili e in Azure Active Directory (Windows Store) | Mobile Developer Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc" />

# Controllo di accesso basato sui ruoli nei servizi mobili e in Azure Active Directory

[WACOM.INCLUDE [mobile-services-selector-rbac](../includes/mobile-services-selector-rbac.md)]

Il controllo di accesso basato sui ruoli consente di assegnare le autorizzazioni ai ruoli applicabili agli utenti, definendo in modo chiaro i limiti delle operazioni eseguibili o meno da determinate classi di utenti. Questa esercitazione descrive la procedura per aggiungere il controllo di accesso basato sui ruoli di base ai servizi mobili di Azure.

Questa esercitazione illustra il controllo di accesso basato sui ruoli, verificando l'appartenenza di ciascun utente a un gruppo Vendite definito in Azure Active Directory (AAD). Il controllo degli accessi viene eseguito con il back-end del servizio mobile .NET usando la [libreria client Graph] per Azure Active Directory. Solo gli utenti che appartengono al gruppo Vendite possono eseguire query sui dati.


>[AZURE.NOTE] L'intento di questa esercitazione è di approfondire la conoscenza dell'autenticazione per includere procedure di autorizzazione. Si presuppone che sia stata completata prima di tutto l'esercitazione [Aggiungere l'autenticazione all'app] usando il provider di autenticazione di Azure Active Directory. Questa esercitazione continua ad aggiornare l'applicazione ToDoItem usata nell'esercitazione [Aggiungere l'autenticazione all'app].

In questa esercitazione viene descritta la procedura seguente:

1. [Creare un gruppo Vendite con l'appartenenza]
2. [Generare una chiave per l'applicazione integrata]
3. [Creare un attributo di autorizzazione personalizzato] 
4. [Aggiungere il controllo di accesso basato sui ruoli alle operazioni del database]
5. [Testare l'accesso client]

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013 in esecuzione su Windows 8.1.
* Completamento dell'esercitazione [Aggiungere l'autenticazione all'app] con il provider di autenticazione di Azure Active Directory.
* Completamento dell'esercitazione sull'[archiviazione degli script del server] per acquisire familiarità con un repository GIT per archiviare gli script del server.
 


## <a name="create-group"></a>Creare un gruppo Vendite con l'appartenenza

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]


## <a name="generate-key"></a>Generare una chiave per l'applicazione integrata


Nel corso dell'esercitazione [Aggiungere l'autenticazione all'app] è stata creata una registrazione per l'applicazione integrata quando è stato completato il passaggio [Registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]. In questa sezione viene generata una chiave da usare nella lettura delle informazioni sulla directory con l'ID client dell'applicazione integrata. 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-custom-authorization-attribute"></a>Creare un attributo di autorizzazione personalizzato nel servizio mobile 

In questa sezione viene creato un nuovo attributo di autorizzazione personalizzato che è possibile usare per eseguire i controlli di accesso sulle operazioni del servizio mobile. L'attributo cerca un gruppo Active Directory basato sul nome del ruolo specificato. Quindi, esegue i controlli di accesso in base all'appartenenza al gruppo.

1. In Visual Studio, fare clic con il pulsante destro del mouse sul progetto di back-end .NET e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nella finestra di dialogo Gestione pacchetti NuGet immettere **ADAL** nei criteri di ricerca per trovare e installare la **libreria di autenticazione di Active Directory** relativa al proprio servizio mobile.

3. Nella finestra di dialogo Gestisci pacchetti NuGet, installare anche la **libreria client di Microsoft Azure Active Directory Graph** relativa al proprio servizio mobile.


4. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di servizio mobile e scegliere **Aggiungi**, quindi **Nuova cartella**. Denominare la nuova cartella **Utilità**.

5. In Visual Studio fare clic con il pulsante destro del mouse sulla nuova cartella **Utilità** e aggiungere un nuovo file della classe denominato **AuthorizeAadRole.cs**.

    ![][0]

6.  Nel file AuthorizeAadRole.cs aggiungere le seguenti istruzioni `using` all'inizio del file. 

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.Linq.Expressions;

7. In AuthorizeAadRole.cs aggiungere il seguente tipo enumerato allo spazio dei nomi Utilità. In questo esempio viene trattato solo il ruolo **Vendite**. Gli altri sono solo esempi di gruppi che è possibile usare.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

8.  In AuthorizeAadRole.cs aggiungere la seguente definizione della classe `AuthorizeAadRole`allo spazio dei nomi Utilità.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            public AadRoles Role { get; private set; }

            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }

9. In AuthorizeAadRole.cs aggiungere il seguente metodo `GetAADToken` alla classe `AuthorizeAadRole`.

    >[WACOM.NOTE] Invece di creare un nuovo token a ogni controllo di accesso, è possibile memorizzarlo nella cache. Aggiornare la cache se viene emessa un'eccezione AccessTokenExpiredException quando si tenta di usare il token, come indicato nella [libreria client Graph]. Nel codice seguente questo passaggio non viene mostrato per semplicità, ma consente di alleggerire il traffico di rete aggiuntivo in Active Directory.  

        private string GetAADToken(ApiServices services)
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;
            
            if (services == null)
                return null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken: Could not retrieve all AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientid, clientCred);

            if (result != null)
                token = result.AccessToken;

            return token;
        }

10. In AuthorizeAadRole.cs aggiornare il metodo `OnAuthorization` nella classe `AuthorizeAadRole` con il seguente codice. Questo codice usa la [libreria client Graph] per cercare il gruppo Active Directory corrispondente al ruolo. Quindi, controlla l'appartenenza dell'utente al gruppo per autorizzarlo.

    >[WACOM.NOTE] Il codice cerca il gruppo Active Directory per nome. In molti casi è consigliabile memorizzare l'ID gruppo come impostazione dell'app del servizio mobile. Il nome del gruppo, infatti, può cambiare mentre l'ID resta inalterato. Tuttavia, quando si modifica il nome del gruppo, si verifica anche almeno una modifica nell'ambito del ruolo che può richiedere un aggiornamento del codice del servizio mobile.  

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            ApiServices services = new ApiServices(actionContext.ControllerContext.Configuration);

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
                return;
            }

            string accessToken = GetAADToken(services);
            if (accessToken == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                return;
            }

            GraphConnection graphConnection = new GraphConnection(accessToken);
            if (graphConnection == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get a graph connection.");
                return;
            }

            bool isAuthorized = false;

            // Find the group using a filter on the name
            FilterGenerator groupFilter = new FilterGenerator();
            groupFilter.QueryFilter = ExpressionHelper
                .CreateConditionalExpression(typeof(Group), GraphProperty.DisplayName, Role.ToString(), 
                    ExpressionType.Equal);

            // Get the group id from the filtered results
            PagedResults<Group> groupPages = graphConnection.List<Group>(null, groupFilter);
            string groupId = groupPages.Results.FirstOrDefault().ObjectId;

            // Check group membership to see if the user is part of the group that corresponds to the role
            if (!string.IsNullOrEmpty(groupId))
            {
                ServiceUser serviceUser = controller.User as ServiceUser;
                if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                {
                    var idents = serviceUser.GetIdentitiesAsync().Result;
                    var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                    if (clientAadCredentials != null)
                    {
                        bool isMember = graphConnection.IsMemberOf(groupId, clientAadCredentials.ObjectId);
                        if (isMember)
                        {
                            isAuthorized = true;
                        }
                    }
                }
            }

            if (!isAuthorized)
            {
                actionContext.Response = actionContext.Request
                    .CreateErrorResponse(HttpStatusCode.Forbidden, 
						"User is not logged in or not a member of the required group");
            }
        }

8. Salvare le modifiche in AuthorizeAadRole.cs.

## <a name="add-access-checking"></a>Aggiungere il controllo di accesso basato sui ruoli alle operazioni del database

1. In Visual Studio espandere la cartella **Controller** nel progetto di servizio mobile. Aprire TodoItemController.cs.

2. In TodoItemController.cs aggiungere un'istruzione `using` per lo spazio dei nomi Utilità che contiene l'attributo di autorizzazione personalizzato. 

        using todolistService.Utilities;

3. In TodoItemController.cs è possibile aggiungere l'attributo alla classe del controller o ai singoli metodi a seconda di come si vuole controllare l'accesso. Per controllare l'accesso a tutte le operazioni del controller in base allo stesso ruolo, aggiungere semplicemente l'attributo alla classe. Per testare questa esercitazione, aggiungere l'attributo alla classe come descritto di seguito.

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


## <a name="test-client"></a>Testare l'accesso client

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]





<!-- Anchors. -->
[Creare un gruppo Vendite con l'appartenenza]: #create-group
[Generare una chiave per l'applicazione integrata]: #generate-key
[Creare un attributo di autorizzazione personalizzato]: #create-custom-authorization-attribute
[Aggiungere il controllo di accesso basato sui ruoli alle operazioni del database]: #add-access-checking
[Testare l'accesso client]: #test-client



<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[Aggiungere l'autenticazione all'app]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Come eseguire la registrazione ad Azure Active Directory]: /it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Scenari di sincronizzazione della directory]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Archiviazione degli script del server]: /it-it/documentation/articles/mobile-services-store-scripts-source-control/
[Registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]: /it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Libreria client Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
[IsMemberOf]: http://msdn.microsoft.com/it-it/library/azure/dn151601.aspx
