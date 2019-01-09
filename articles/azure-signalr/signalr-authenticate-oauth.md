---
title: Esercitazione per l'autenticazione dei client del servizio Azure SignalR
description: Questa esercitazione spiega come autenticare i client del servizio Azure SignalR
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: be44a233c2b09aa91ec04b5a64b523a2d0b00db1
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599860"
---
# <a name="tutorial-azure-signalr-service-authentication"></a>Esercitazione: autenticazione del servizio Azure SignalR

Questa esercitazione si basa sull'applicazione di chat room presentata nella guida di avvio rapido. Se non è stato completato l'esercizio [Creare una chat room con il servizio SignalR](signalr-quickstart-dotnet-core.md), completarlo ora.

Questa esercitazione spiegherà come implementare la propria autenticazione e integrarla con il servizio Microsoft Azure SignalR.

L'autenticazione utilizzata inizialmente nell'applicazione di chat room della guida di avvio rapido è troppo semplice per gli scenari reali. L'applicazione consente a ogni client di dichiarare la propria identità, mentre il server si limita ad accettare tale identità. Questo approccio non è molto utile nelle applicazioni reali in cui un utente non autorizzato potrebbe rappresentare altri utenti per accedere a dati sensibili.

[GitHub](https://github.com/) fornisce API di autenticazione basate su un diffuso protocollo standard di settore chiamato [OAuth](https://oauth.net/). Queste API consentono alle applicazioni di terze parti di autenticare account GitHub. Le API saranno usate in questa esercitazione per implementare l'autenticazione tramite un account GitHub prima di consentire accessi dei client all'applicazione di chat room. Dopo l'autenticazione di un account GitHub, le informazioni sull'account verranno aggiunte come cookie che potrà essere usato dal client Web per l'autenticazione.

Per altre informazioni sulle API di autenticazione OAuth fornite tramite GitHub, vedere [Basics of Authentication](https://developer.github.com/v3/guides/basics-of-authentication/) (Nozioni di base sull'autenticazione).

Per completare i passaggi descritti in questa guida di avvio rapido è possibile usare qualsiasi editor di codice. Tuttavia, [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

Il codice per questa esercitazione è disponibile per il download nel [repository GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat).

![OAuth completa ospitato in Azure](media/signalr-authenticate-oauth/signalr-oauth-complete-azure.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Registrare una nuova app OAuth con l'account GitHub
> * Aggiungere un controller di autenticazione per supportare l'autenticazione di GitHub
> * Distribuire l'app Web ASP.NET Core in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono previsti i prerequisiti seguenti:

* Un account creato in [GitHub](https://github.com/)
* [Git](https://git-scm.com/)
* [ASP.NET Core SDK](https://www.microsoft.com/net/download/windows)
* [Azure Cloud Shell configurato](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Scaricare o clonare il repository GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).

## <a name="create-an-oauth-app"></a>Creare un'app OAuth

1. Aprire un Web browser, passare a `https://github.com` e accedere al proprio account.

2. Per l'account, passare a **Impostazioni** > **Impostazioni modalità sviluppatore** e fare clic su **Register a new application** (Registra una nuova applicazione) o **New OAuth App** (Nuova app OAuth) sotto *OAuth Apps* (App OAuth).

3. Usare le seguenti impostazioni per la nuova app OAuth, quindi fare clic su **Register application**(Registra applicazione):

    | Nome impostazione | Valore consigliato | Descrizione |
    | ------------ | --------------- | ----------- |
    | Nome dell'applicazione | *Chat Azure SignalR* | L'utente di GitHub deve essere in grado di riconoscere e considerare attendibile l'app con cui sta eseguendo l'autenticazione.   |
    | URL della home page | *http://localhost:5000/home* | |
    | Descrizione applicazione | *Un esempio di chat room che usa il servizio Azure SignalR con l'autenticazione di GitHub* | Una descrizione utile dell'applicazione che consenta agli utenti di comprendere il contesto di autenticazione utilizzato. |
    | URL di callback dell'autorizzazione | *http://localhost:5000/signin-github* | Questa impostazione è la più importante per l'applicazione OAuth. È l'URL di callback che GitHub restituisce all'utente al termine dell'autenticazione. In questa esercitazione è necessario usare l'URL di callback predefinito per il pacchetto *AspNet.Security.OAuth.GitHub*, ovvero */signin-github*.  |

4. Una volta completata la registrazione della nuova app OAuth, aggiungere l'*ID client* e il *segreto client* a Secret Manager usando i comandi seguenti. Sostituire *Your_GitHub_Client_Id* e *Your_GitHub_Client_Secret* con i valori per l'app OAuth.

        dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
        dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret

## <a name="implement-the-oauth-flow"></a>Implementare il flusso di OAuth

### <a name="update-the-startup-class-to-support-github-authentication"></a>Aggiornare la classe Startup per il supporto dell'autenticazione di GitHub

1. Aggiungere un riferimento ai pacchetti *Microsoft.AspNetCore.Authentication.Cookies* e *AspNet.Security.OAuth.GitHub* più recenti e ripristinare tutti i pacchetti.

        dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
        dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
        dotnet restore

1. Aprire *Startup.cs* e aggiungere istruzioni `using` per gli spazi dei nomi seguenti:

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. Nella parte superiore della classe `Startup` aggiungere le costanti per le chiavi di Secret Manager che contengono i segreti dell'app OAuth di GitHub.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. Aggiungere il codice seguente al metodo `ConfigureServices` per supportare l'autenticazione con l'app OAuth di GitHub:

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. Aggiungere il metodo helper `GetUserCompanyInfoAsync` alla classe `Startup`.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Aggiornare il metodo `Configure` della classe Startup con la riga di codice seguente e salvare il file.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>Aggiungere un controller di autenticazione

In questa sezione sarà implementata un'API `Login` che autentica i client che usano l'app OAuth di GitHub. Dopo l'autenticazione, l'API aggiungerà un cookie alla risposta del client Web prima di reindirizzare il client all'app di chat. Quel cookie si userà in seguito per identificare il client.

1. Aggiungere un nuovo file di codice controller alla directory *chattest\Controllers*. Assegnare il nome *AuthController.cs* al file.

2. Aggiungere il codice seguente per il controller di autenticazione. Assicurarsi di aggiornare lo spazio dei nomi, se la directory di progetto non era *chattest*:

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. Salvare le modifiche.

### <a name="update-the-hub-class"></a>Aggiornare la classe Hub

Per impostazione predefinita, quando un client Web tenta di connettersi al servizio SignalR, la connessione è concessa in base a un token di accesso fornito internamente. Questo token di accesso non è associato a un'identità autenticata. L'accesso è effettivamente anonimo.

In questa sezione si attiverà l'autenticazione reale aggiungendo l'attributo `Authorize` alla classe Hub e aggiornando i metodi di Hub per leggere il nome utente dall'attestazione dell'utente autenticato.

1. Aprire *Hub\Chat.cs* e aggiungere riferimenti a questi spazi dei nomi:

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. Aggiornare il codice di Hub come illustrato di seguito. Questo codice aggiunge l'attributo `Authorize` alla classe `Chat` e usa l'identità dell'utente autenticato nei metodi di Hub. Viene anche aggiunto il metodo `OnConnectedAsync`, che registra un messaggio di sistema nella chat room ogni volta che un nuovo client si connette.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. Salvare le modifiche.

### <a name="update-the-web-client-code"></a>Aggiornare il codice del client Web

1. Aprire *wwwroot\index.html* e sostituire il codice che richiede il nome utente con il codice che usa il cookie restituito dal controller di autenticazione.

    Rimuovere il codice seguente da *index.html*:

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Aggiungere il codice seguente al posto del codice precedente per usare il cookie:

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Subito sotto la riga di codice aggiunta per usare il cookie, aggiungere la definizione seguente per la funzione `appendMessage`:

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. Aggiornare le funzioni `bindConnectionMessage` e `onConnected` con il codice seguente per usare `appendMessage`.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. In fondo a *index.html* aggiornare il gestore degli errori per `connection.start()` come illustrato di seguito per richiedere all'utente di eseguire l'accesso.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. Salvare le modifiche.

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Salvare le modifiche di tutti i file.

2. Compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire il comando seguente nella shell dei comandi:

        dotnet build

3. Dopo che la compilazione è stata completata correttamente, eseguire il comando seguente per avviare l'app Web in locale:

        dotnet run

    Per impostazione predefinita, l'applicazione sarà ospitata in locale sulla porta 5000:

        E:\Testing\chattest>dotnet run
        Hosting environment: Production
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.

4. Avviare una finestra del browser e passare a `http://localhost:5000`. Fare clic sul collegamento **qui** nella parte superiore per eseguire l'accesso con GitHub.

    ![OAuth completa ospitato in Azure](media/signalr-authenticate-oauth/signalr-oauth-complete-azure.png)

    Viene chiesto di autorizzare l'accesso dell'app chat al proprio account GitHub. Fare clic sul pulsante **Autorizza**.

    ![Autorizzare l'app OAuth](media/signalr-authenticate-oauth/signalr-authorize-oauth-app.png)

    L'utente sarà reindirizzato all'applicazione di chat ed eseguirà automaticamente l'accesso con il nome dell'account GitHub. L'applicazione Web ha determinato il nome dell'account eseguendo l'autenticazione mediante la nuova autenticazione che è stata aggiunta.

    ![Account identificato](media/signalr-authenticate-oauth/signalr-oauth-account-identified.png)

    Ora che l'app di chat esegue l'autenticazione con GitHub e archivia le informazioni di autenticazione come cookie, è necessario distribuirla in Azure in modo che altri utenti possono eseguire l'autenticazione con i propri account e comunicare da altre workstation.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-app-to-azure"></a>Distribuire l'app in Azure

In questa sezione si userà l'interfaccia della riga di comando di Azure da Azure Cloud Shell per creare una nuova app Web in [Servizio app di Azure](https://docs.microsoft.com/azure/app-service/) per ospitare l'applicazione ASP.NET in Azure. L'app Web verrà configurata per usare la distribuzione Git locale. L'app Web verrà configurata anche con la stringa di connessione SignalR, i segreti dell'app OAuth di GitHub e un utente di distribuzione.

I passaggi di questa sezione usano l'estensione *signalr* per l'interfaccia della riga di comando di Azure. Eseguire il comando seguente per installare l'estensione *signalr* per l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az extension add -n signalr
```

Quando si creano le risorse seguenti, assicurarsi di usare lo stesso gruppo di risorse in cui si trova la risorsa servizio SignalR. Questo approccio semplificherà la pulizia quando in seguito si vorrà rimuovere tutte le risorse. Gli esempi proposti presuppongono che si usi il nome di gruppo consigliato nelle esercitazioni precedenti: *SignalRTestResources*.

### <a name="create-the-web-app-and-plan"></a>Creare l'app Web e un piano

Copiare il testo per i comandi seguenti e aggiornare i parametri. Incollare lo script aggiornato in Azure Cloud Shell, quindi premere **Invio** per creare un nuovo piano di servizio app e una nuova app Web.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| Parametro | Description |
| -------------------- | --------------- |
| ResourceGroupName | Questo nome di gruppo di risorse è stato suggerito nelle esercitazioni precedenti. È consigliabile mantenere tutte le risorse dell'esercitazione raggruppate insieme. Usare lo stesso gruppo di risorse utilizzato nelle esercitazioni precedenti. |
| WebAppPlan | Immettere un nome di piano di servizio app nuovo e univoco. |
| WebAppName | Questo sarà il nome della nuova app Web e parte dell'URL. Usare un nome univoco. Ad esempio signalrtestwebapp22665120.   |

### <a name="add-app-settings-to-the-web-app"></a>Aggiungere le impostazioni dell'app all'app Web

In questa sezione si aggiungeranno le impostazioni dell'app per i componenti seguenti:

* Stringa di connessione della risorsa servizio SignalR
* ID client dell'app OAuth di GitHub
* Segreto client dell'app OAuth di GitHub

Copiare il testo per i comandi seguenti e aggiornare i parametri. Incollare lo script aggiornato in Azure Cloud Shell, quindi premere **Invio** per aggiungere le impostazioni dell'app:

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR Service resource hostName
signalRhostname=$(az signalr show --name $SignalRServiceResource \
    --resource-group $ResourceGroupName --query hostName -o tsv)

# Get the SignalR primary key
signalRprimarykey=$(az signalr key list --name $SignalRServiceResource \
    --resource-group $ResourceGroupName --query primaryKey -o tsv)

# Form the connection string to the service resource
connstring="Endpoint=https://$signalRhostname;AccessKey=$signalRprimarykey;"

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$connstring"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| Parametro | Descrizione |
| -------------------- | --------------- |
| GitHubClientId | Assegnare a questa variabile l'ID client segreto per l'app OAuth di GitHub. |
| GitHubClientSecret | Assegnare a questa variabile la password segreta per l'app OAuth di GitHub. |
| ResourceGroupName | Aggiornare questa variabile in modo che corrisponda al nome del gruppo di risorse che è stato usato nella sezione precedente. |
| SignalRServiceResource | Aggiornare questa variabile con il nome della risorsa servizio SignalR creata nella guida di avvio rapido. Ad esempio signalrtestsvc48778624. |
| WebAppName | Aggiornare questa variabile con il nome della nuova app Web che è stata creata nella sezione precedente. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>Configurare l'app Web per la distribuzione Git locale

In Azure Cloud Shell incollare lo script seguente. Questo script crea un nuovo nome utente di distribuzione e una nuova password che saranno usati durante la distribuzione del codice per l'app Web con Git. Lo script consente anche di configurare l'app Web per la distribuzione con un repository Git locale e restituisce l'URL di distribuzione Git.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| Parametro | Descrizione |
| -------------------- | --------------- |
| DeploymentUserName | Scegliere un nuovo nome utente di distribuzione. |
| DeploymentUserPassword | Scegliere una password per il nuovo utente di distribuzione. |
| ResourceGroupName | Usare lo stesso nome del gruppo di risorse utilizzato nella sezione precedente. |
| WebAppName | Questo sarà il nome della nuova app Web che è stata creata in precedenza. |

Annotare l'URL di distribuzione Git restituito dal comando. Questo URL sarà necessario in seguito.

### <a name="deploy-your-code-to-the-azure-web-app"></a>Distribuire il codice nell'app Web di Azure

Per distribuire il codice, eseguire i comandi seguenti in una shell di Git.

1. Passare alla radice della directory di progetto. Se il progetto non è inizializzato con un repository Git, eseguire il comando seguente:

    ```bash
    git init
    ```

2. Aggiungere un remote per l'URL di distribuzione Git annotato in precedenza:

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. Eseguire lo staging di tutti i file nel repository inizializzato e aggiungere un commit.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Distribuire il codice nell'app Web in Azure.

    ```bash
    git push Azure master
    ```

    Verrà chiesto di autenticarsi per distribuire il codice in Azure. Immettere il nome utente e la password dell'utente di distribuzione creato in precedenza.

### <a name="update-the-github-oauth-app"></a>Aggiornare l'app OAuth di GitHub

L'ultima operazione da eseguire è l'aggiornamento dell'**URL della home page** e dell'**URL di callback dell'autorizzazione** dell'app OAuth di GitHub in modo da puntare alla nuova app ospitata.

1. Aprire [http://github.com](http://github.com) in un browser e passare a **Impostazioni** > **Impostazioni modalità sviluppatore** > **Oauth Apps** (Oauth App) per il proprio account.

2. Fare clic sull'app di autenticazione e aggiornare l'**URL della home page** e l'**URL di callback dell'autorizzazione** come illustrato di seguito:

    | Impostazione | Esempio |
    | ------- | ------- |
    | URL della home page | https://signalrtestwebapp22665120.azurewebsites.net/home |
    | URL di callback dell'autorizzazione | https://signalrtestwebapp22665120.azurewebsites.net/signin-github |

3. Passare all'URL dell'app Web e testare l'applicazione.

    ![OAuth completa ospitato in Azure](media/signalr-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si proseguirà con l'esercitazione successiva, sarà possibile conservare le risorse create in questa guida introduttiva e riutilizzarle.

In caso contrario, se si è terminato il lavoro con l'applicazione di esempio della guida di avvio rapido, è possibile eliminare le risorse di Azure create in questa guida di avvio rapido per evitare i costi correlati.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e comporta l'eliminazione definitiva del gruppo di risorse e di tutte le risorse incluse nel gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dai rispettivi pannelli anziché eliminare il gruppo di risorse.

Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse. Le istruzioni di questo articolo usano un gruppo di risorse denominato *SignalRTestResources*. Nel gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

![Delete](./media/signalr-authenticate-oauth/signalr-delete-resource-group.png)

Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere il nome del gruppo di risorse per confermare e fare clic su **Elimina**.

Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aggiunta l'autenticazione con OAuth per fornire un approccio migliore all'autenticazione con il servizio Azure SignalR. Per altre informazioni sull'uso di Azure SignalR Server, continuare con gli esempi dell'interfaccia della riga di comando di Azure per il servizio SignalR.

> [!div class="nextstepaction"]
> [Esempi dell'interfaccia della riga di comando di Azure SignalR](./signalr-cli-samples.md)