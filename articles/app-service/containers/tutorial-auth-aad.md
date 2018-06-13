---
title: Autenticare e autorizzare gli utenti end-to-end nel servizio app di Azure in Linux | Microsoft Docs
description: Informazioni su come usare le funzionalità di autenticazione e autorizzazione del servizio app per proteggere le app del servizio app, incluso l'accesso alle API remote.
keywords: servizio app, Servizio app di Azure, autenticazione, autorizzazione, proteggere, sicurezza, multilivello, Azure Active Directory, Azure AD
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/26/2018
ms.author: cephalin
ms.openlocfilehash: a468c5d0f73cc182927f26ea9b7a85e2c5afb7c8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766360"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service-on-linux"></a>Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel servizio app di Azure in Linux

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Il servizio app include anche il supporto predefinito per [l'autenticazione e l'autorizzazione degli utenti](../app-service-authentication-overview.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). Questa esercitazione illustra come proteggere le app con le funzionalità di autenticazione e autorizzazione del servizio app. Viene usata un'app ASP.NET Core con un front-end Angular.js, ma solo a titolo di esempio. Le funzionalità di autenticazione e autorizzazione del servizio app supportano runtime di tutti i linguaggi e seguendo l'esercitazione si può apprendere come applicarle al linguaggio preferito.

L'esercitazione usa l'app di esempio per illustrare come proteggere un'app autonoma (vedere [Abilitare l'autenticazione e l'autorizzazione per l'app back-end](#enable-authentication-and-authorization-for-back-end-app)).

![Autenticazione e autorizzazione semplici](./media/tutorial-auth-aad/simple-auth.png)

Illustra anche come proteggere un'app multilivello, accedendo a un'API back-end protetta per conto dell'utente autenticato sia [dal codice del server](#call-api-securely-from-server-code) che [dal codice del browser](#call-api-securely-from-browser-code).

![Autenticazione e autorizzazione avanzate](./media/tutorial-auth-aad/advanced-auth.png)

Questi sono solo alcuni degli scenari di autenticazione e autorizzazione possibili nel servizio app. 

Ecco un elenco più completo delle operazioni illustrate nell'esercitazione:

> [!div class="checklist"]
> * Abilitare l'autenticazione e l'autorizzazione predefinite
> * Proteggere le app da richieste non autenticate
> * Usare Azure Active Directory come provider di identità
> * Accedere a un'app remota per conto dell'utente connesso
> * Proteggere le chiamate da servizio a servizio con l'autenticazione tramite token
> * Usare token di accesso dal codice del server
> * Usare token di accesso dal codice del client (browser)

I passaggi illustrati in questa esercitazione possono essere eseguiti in macOS, Linux e Windows.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione:

* [Installare Git](https://git-scm.com/).
* [Installare .NET Core 2.0](https://www.microsoft.com/net/core/).

## <a name="create-local-net-core-app"></a>Creare l'app .NET Core locale

In questo passaggio si configura il progetto .NET Core locale. Lo stesso progetto verrà usato per distribuire un'app per le API back-end e un'app Web front-end.

### <a name="clone-and-run-the-sample-application"></a>Clonare ed eseguire l'applicazione di esempio

Usare questi comandi per clonare il repository di esempio ed eseguirlo.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Passare a `http://localhost:5000` e provare ad aggiungere, modificare e rimuovere attività. 

![API ASP.NET Core in esecuzione in locale](./media/tutorial-auth-aad/local-run.png)

Per arrestare ASP.NET Core in qualsiasi momento, premere `Ctrl+C` nel terminale.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Distribuire le app in Azure

In questo passaggio si distribuisce il progetto in due app del servizio app: una è l'app front-end e l'altra è l'app back-end.

### <a name="create-azure-resources"></a>Creare le risorse di Azure

In Cloud Shell eseguire questi comandi per creare due app Web. Sostituire _&lt;front\_end\_app\_name>_ e _&lt;back\_end\_app\_name>_ con due nomi di app univoci a livello globale. I caratteri validi sono `a-z`, `0-9` e `-`. Per altre informazioni su ogni comando, vedere [Creare un'app Web .NET Core nel Servizio app in Linux](quickstart-dotnetcore.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku B1 --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front_end_app_name> --runtime "dotnetcore|2.0" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back_end_app_name> --runtime "dotnetcore|2.0" --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> Salvare gli URL degli elementi Git remoti per l'app front-end e l'app back-end, riportati nell'output di `az webapp create`.
>

### <a name="configure-cors"></a>Configurare CORS

Questo passaggio non è correlato all'autenticazione e all'autorizzazione. Sarà tuttavia necessario più avanti per [chiamare l'API back-end dal codice del browser front-end](#call-api-securely-from-browser-code), in modo che il browser consenta le chiamate API tra domini dall'app Angular.js. Il servizio app in Linux non ha funzionalità CORS predefinite come la [controparte Windows](../app-service-web-tutorial-rest-api.md#add-cors-functionality), quindi è necessario aggiungerle manualmente per l'app back-end.

Nel repository locale aprire il file _Startup.cs_. Nel metodo `ConfigureServices(IServiceCollection services)` aggiungere la riga di codice seguente:

```csharp
services.AddCors();
```

Nel metodo `Configure(IApplicationBuilder app)` aggiungere la riga di codice seguente all'inizio, sostituendo *\<front_end_app_name>*:

```csharp
app.UseCors(builder =>
    builder.WithOrigins("http://<front_end_app_name>.azurewebsites.net"));
```

Salvare le modifiche. Nella _finestra del terminale locale_ usare i comandi seguenti per eseguire il commit delle modifiche nel repository Git.

```bash
git add .
git commit -m "add CORS to back end"
```

> [!NOTE]
> Non è necessario condividere questo codice tra le app back-end e front-end, perché non ha alcun effetto CORS sull'app front-end.
> 

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Nella finestra del terminale locale eseguire questi comandi Git per la distribuzione nell'app back-end. Sostituire _&lt;deploymentLocalGitUrl-of-back-end-app>_ con l'URL dell'elemento Git remoto salvato in [Creare le risorse di Azure](#create-azure-resources). Quando Git Credential Manager richiede le credenziali, assicurarsi di immettere le [credenziali per la distribuzione](../app-service-deployment-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) e non quelle usate per accedere al portale di Azure.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Nella finestra del terminale locale eseguire questi comandi Git per distribuire lo stesso codice nell'app front-end. Sostituire _&lt;deploymentLocalGitUrl-of-front-end-app>_ con l'URL dell'elemento Git remoto salvato in [Creare le risorse di Azure](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-azure-web-apps"></a>Passare alle app Web di Azure

Passare agli URL seguenti in un browser e visualizzare le due app in esecuzione.

```
http://<back_end_app_name>.azurewebsites.net
http://<front_end_app_name>.azurewebsites.net
```

![API ASP.NET Core in esecuzione nel servizio app di Azure](./media/tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Se l'app viene riavviata, si noterà che sono stati cancellati nuovi dati. Questo comportamento è determinato dalla progettazione, perché l'app ASP.NET Core usa un database in memoria.
>
>

## <a name="call-back-end-api-from-front-end"></a>Chiamare l'API back-end dal front-end

In questo passaggio si definisce il codice del server dell'app front-end per l'accesso all'API back-end. Successivamente si abiliterà l'accesso autenticato dal front-end al back-end.

### <a name="modify-front-end-code"></a>Modificare il codice del front-end

Nel repository locale aprire _Controllers/TodoController.cs_. All'inizio della classe `TodoController` aggiungere le righe seguenti sostituendo _&lt;back\_end\_app\_name>_ con il nome dell'app back-end:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back_end_app_name>.azurewebsites.net";
```

Trovare il metodo `GetAll()` e sostituire il codice all'interno delle parentesi graffe con:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

La prima riga effettua una chiamata `GET /api/Todo` all'app per le API back-end.

Trovare quindi il metodo `GetById(long id)` e sostituire il codice all'interno delle parentesi graffe con:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

La prima riga effettua una chiamata `GET /api/Todo/{id}` all'app per le API back-end.

Trovare quindi il metodo `Create([FromBody] TodoItem item)` e sostituire il codice all'interno delle parentesi graffe con:

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

La prima riga effettua una chiamata `POST /api/Todo` all'app per le API back-end.

Trovare quindi il metodo `Update(long id, [FromBody] TodoItem item)` e sostituire il codice all'interno delle parentesi graffe con:

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

La prima riga effettua una chiamata `PUT /api/Todo/{id}` all'app per le API back-end.

Trovare quindi il metodo `Delete(long id)` e sostituire il codice all'interno delle parentesi graffe con:

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

La prima riga effettua una chiamata `DELETE /api/Todo/{id}` all'app per le API back-end.

Salvare tutte le modifiche. Nella finestra del terminale locale distribuire le modifiche nell'app front-end con i comandi Git seguenti:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Verificare le modifiche

Passare a `http://<front_end_app_name>.azurewebsites.net` e aggiungere alcuni elementi, ad esempio `from front end 1` e `from front end 2`.

Passare a `http://<back_end_app_name>.azurewebsites.net` per visualizzare gli elementi aggiunti dall'app front-end. Aggiungere anche alcuni elementi, ad esempio `from back end 1` e `from back end 2`, quindi aggiornare l'app front-end per verificare se riflette le modifiche.

![API ASP.NET Core in esecuzione nel servizio app di Azure](./media/tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Configurare l'autenticazione e l'autorizzazione

In questo passaggio si abilitano l'autenticazione e l'autorizzazione per le due app. Si configura anche l'app front-end per generare un token di accesso che potrà essere usato per effettuare chiamate autenticate all'app back-end.

Come provider di identità viene usato Azure Active Directory. Per altre informazioni, vedere [Configurare l'autenticazione di Azure Active Directory per un'applicazione dei servizi app](../app-service-mobile-how-to-configure-active-directory-authentication.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Abilitare l'autenticazione e l'autorizzazione per l'app back-end

Nel [portale di Azure](https://portal.azure.com) aprire la pagina di gestione dell'app back-end facendo clic su **Gruppi di risorse** > **myAuthResourceGroup** > _\<nome\_app\_back\_end>_ dal menu a sinistra.

![API ASP.NET Core in esecuzione nel servizio app di Azure](./media/tutorial-auth-aad/portal-navigate-back-end.png)

Nel menu a sinistra dell'app back-end fare clic su **Autenticazione/Autorizzazione** e quindi abilitare l'autenticazione del servizio app facendo clic su **Sì**.

In **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.

In **Provider di autenticazione** fare clic su **Azure Active Directory**. 

![API ASP.NET Core in esecuzione nel servizio app di Azure](./media/tutorial-auth-aad/configure-auth-back-end.png)

Fare clic su **Rapida**, quindi accettare le impostazioni predefinite per creare una nuova app AD e fare clic su **OK**.

Nella pagina **Autenticazione/Autorizzazione** fare clic su **Salva**. 

Quando viene visualizzata la notifica con il messaggio `Successfully saved the Auth Settings for <back_end_app_name> App`, aggiornare la pagina.

Fare di nuovo clic su **Azure Active Directory** e quindi su **Gestisci l'applicazione**.

Dalla pagina di gestione dell'applicazione AD copiare l'**ID applicazione** in un blocco note. Questo valore sarà necessario in un secondo momento.

![API ASP.NET Core in esecuzione nel servizio app di Azure](./media/tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Abilitare l'autenticazione e l'autorizzazione per l'app front-end

Seguire la stessa procedura per l'app front-end, ignorando però l'ultimo passaggio. Per l'app front-end non è necessario l'**ID applicazione**. Tenere aperta la pagina **Impostazioni di Azure Active Directory**.

Se si vuole, passare a `http://<front_end_app_name>.azurewebsites.net`. Si dovrebbe essere indirizzati a una pagina di accesso. Dopo aver effettuato l'accesso, non è comunque possibile accedere ai dati dell'app back-end perché è necessario eseguire ancora tre operazioni:

- Concedere al front-end l'accesso al back-end
- Configurare il servizio app per la restituzione di un token utilizzabile
- Usare il token nel codice

> [!TIP]
> Se si verificano errori e le impostazioni di autenticazione/autorizzazione dell'app vengono riconfigurate, i token nell'archivio token potrebbero non essere rigenerati dalle nuove impostazioni. Per assicurarsi che i token vengano rigenerati, è necessario disconnettersi e accedere di nuovo all'app. Un modo semplice per eseguire tale operazione consiste nell'usare il browser in modalità privata, chiudendo e riaprendo il browser in modalità privata dopo aver modificato le impostazioni nelle app.

### <a name="grant-front-end-app-access-to-back-end"></a>Concedere all'app front-end l'accesso al back-end

Dopo l'abilitazione dell'autenticazione e dell'autorizzazione per entrambe le app, ognuna di queste è supportata da un'applicazione AD. In questo passaggio si concedono all'app front-end le autorizzazioni necessarie per accedere al back-end per conto dell'utente. Tecnicamente, si concedono all'_applicazione AD_ del front-end le autorizzazioni per accedere all'_applicazione AD_ del back-end per conto dell'utente.

A questo punto dovrebbe essere visualizzata la pagina **Impostazioni di Azure Active Directory** per l'app front-end. In caso contrario, passare a tale pagina. 

Fare clic su **Gestisci autorizzazioni** > **Aggiungi** > **Selezionare un'API**.

![API ASP.NET Core in esecuzione nel servizio app di Azure](./media/tutorial-auth-aad/add-api-access-front-end.png)

Nella pagina **Selezionare un'API** digitare il nome dell'applicazione AD per l'app back-end, che per impostazione predefinita è uguale al nome dell'app back-end. Selezionare l'applicazione nell'elenco e fare clic su **Seleziona**.

Selezionare la casella di controllo accanto ad **Accedi a _&lt;nome\_applicazione\_AD>_**. Fare clic su **Seleziona** > **Fine**.

![API ASP.NET Core in esecuzione nel servizio app di Azure](./media/tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Configurare il servizio app per la restituzione di un token di accesso utilizzabile

L'app front-end ha ora le autorizzazioni necessarie. In questo passaggio si configurano l'autenticazione e l'autorizzazione del servizio app affinché forniscano un token di accesso utilizzabile per accedere al back-end. Per questo passaggio è necessario l'ID applicazione del back-end copiato in [Abilitare l'autenticazione e l'autorizzazione per l'app back-end](#enable-authentication-and-authorization-for-back-end-app).

Accedere ad [Azure Resource Explorer](https://resources.azure.com). Nella parte superiore della pagina fare clic su **Read/Write** (Lettura/scrittura) per abilitare la modifica delle risorse di Azure.

![API ASP.NET Core in esecuzione nel servizio app di Azure](./media/tutorial-auth-aad/resources-enable-write.png)

Nel browser a sinistra fare clic su **subscriptions** > **_&lt;propria\_sottoscrizione>_** > **resourceGroups** > **myAuthResourceGroup** > **providers** > **Microsoft.Web** > **sites** > **_\<front\_end\_app\_name>_** > **config** > **authsettings**.

Nella visualizzazione **authsettings** fare clic su **Edit** (Modifica). Impostare `additionalLoginParams` sulla stringa JSON seguente, usando l'ID applicazione copiato. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back_end_application_id>"],
```

![API ASP.NET Core in esecuzione nel servizio app di Azure](./media/tutorial-auth-aad/additional-login-params-front-end.png)

Salvare le impostazioni facendo clic su **PUT**.

Le app sono ora configurate. Il front-end è pronto per accedere al back-end con un token di accesso appropriato.

Per informazioni su come eseguire questa configurazione per altri provider, vedere [Refresh access tokens](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#refresh-access-tokens) (Aggiornare i token di accesso).

## <a name="call-api-securely-from-server-code"></a>Chiamare l'API in modo sicuro dal codice del server

In questo passaggio si abilita il codice del server modificato in precedenza per effettuare chiamate autenticate all'API back-end.

L'app front-end ha ora l'autorizzazione necessaria e aggiunge inoltre l'ID applicazione del back-end ai parametri di accesso. Può quindi ottenere un token di accesso per l'autenticazione con l'app back-end. Il servizio app passa questo token al codice del server inserendo un'intestazione `X-MS-TOKEN-AAD-ACCESS-TOKEN` in ogni richiesta autenticata. Vedere [Retrieve tokens in app code](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code) (Recuperare i token nel codice dell'app).

> [!NOTE]
> Queste intestazioni vengono inserite per tutti i linguaggi supportati. Per accedervi, usare il modello standard per il rispettivo linguaggio.

Nel repository locale aprire di nuovo _Controllers/TodoController.cs_. Sotto il costruttore `TodoController(TodoContext context)` aggiungere il codice seguente:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["x-ms-token-aad-access_token"]);
}
```

Questo codice aggiunge l'intestazione HTTP standard `Authorization: Bearer <access_token>` a tutte le chiamate ad API remote. Nella pipeline di esecuzione della richiesta ASP.NET Core MVC viene eseguito `OnActionExecuting` subito prima del rispettivo metodo di azione (ad esempio `GetAll()`), di conseguenza ogni chiamata API in uscita presenta ora il token di accesso.

Salvare tutte le modifiche. Nella finestra del terminale locale distribuire le modifiche nell'app front-end con i comandi Git seguenti:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Accedere di nuovo a `http://<front_end_app_name>.azurewebsites.net`. Nella pagina del contratto per l'utilizzo dei dati utente fare clic su **Accetta**.

Dovrebbe ora essere possibile creare, leggere, aggiornare ed eliminare i dati dell'app back-end come prima. L'unica differenza consiste nel fatto che ora entrambe le app, incluse le chiamate da servizio a servizio, sono protette dall'autenticazione e dall'autorizzazione del servizio app.

Congratulazioni! Il codice del server accede ora ai dati del back-end per conto dell'utente autenticato.

## <a name="call-api-securely-from-browser-code"></a>Chiamare l'API in modo sicuro dal codice del browser

In questo passaggio si configura l'app Angular.js front-end in modo che punti all'API back-end. In questo modo si apprenderà come recuperare il token di accesso ed effettuare chiamate API all'app back-end con tale token.

Mentre il codice del server ha accesso alle intestazioni delle richieste, il codice del client può ottenere gli stessi token di accesso accedendo a `GET /.auth/me`. Vedere [Retrieve tokens in app code](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code) (Recuperare i token nel codice dell'app).

> [!TIP]
> Questa sezione illustra le chiamate HTTP sicure usando i metodi HTTP standard. È tuttavia possibile usare [Active Directory Authentication Library (ADAL) for JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) per semplificare il modello di applicazione Angular.js.
>

### <a name="point-angularjs-app-to-back-end-api"></a>Puntare l'app Angular.js all'API back-end

Nel repository locale aprire _wwwroot/index.html_.

Nella riga 51 impostare la variabile `apiEndpoint` sull'URL dell'app back-end (`http://<back_end_app_name>.azurewebsites.net`). Sostituire _\<back\_end\_app\_name>_ con il nome dell'app nel servizio app.

Nel repository locale aprire _wwwroot/app/scripts/todoListSvc.js_ e verificare che `apiEndpoint` sia anteposto a tutte le chiamate API. L'app Angular.js chiama ora le API back-end. 

### <a name="add-access-token-to-api-calls"></a>Aggiungere il token di accesso alle chiamate API

In _wwwroot/app/scripts/todoListSvc.js_ aggiungere la funzione seguente all'elenco sopra l'elenco delle chiamate API (sopra la riga `getItems : function(){`):

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Questa funzione viene chiamata per impostare l'intestazione `Authorization` predefinita con il token di accesso. Verrà chiamata nel passaggio successivo.

Nel repository locale aprire _wwwroot/app/scripts/app.js_ e trovare il codice seguente:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Sostituire l'intero blocco di codice con il codice seguente:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

La nuova modifica aggiunge il mapping `revolve`, che chiama `/.auth/me` e imposta il token di accesso. Viene così garantita la disponibilità del token di accesso prima della creazione di un'istanza del controller `todoListCtrl`. In questo modo, tutte le chiamate API da parte del controller includeranno il token.

### <a name="deploy-updates-and-test"></a>Distribuire aggiornamenti ed eseguire un test

Salvare tutte le modifiche. Nella finestra del terminale locale distribuire le modifiche nell'app front-end con i comandi Git seguenti:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Passare di nuovo a `http://<front_end_app_name>.azurewebsites.net`. Dovrebbe ora essere possibile creare, leggere, aggiornare ed eliminare i dati dell'app back-end direttamente nell'app Angular.js.

Congratulazioni! Il codice del client accede ora ai dati del back-end per conto dell'utente autenticato.

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Se si ritiene che queste risorse non saranno necessarie in futuro, eliminare il gruppo di risorse eseguendo questo comando in Cloud Shell:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

L'esecuzione del comando può richiedere un minuto.

<a name="next"></a>
## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Abilitare l'autenticazione e l'autorizzazione predefinite
> * Proteggere le app da richieste non autenticate
> * Usare Azure Active Directory come provider di identità
> * Accedere a un'app remota per conto dell'utente connesso
> * Proteggere le chiamate da servizio a servizio con l'autenticazione tramite token
> * Usare token di accesso dal codice del server
> * Usare token di accesso dal codice del client (browser)

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
