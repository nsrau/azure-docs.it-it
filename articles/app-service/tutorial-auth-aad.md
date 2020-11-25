---
title: 'Esercitazione: Autenticare gli utenti E2E'
description: Informazioni su come usare le funzionalità di autenticazione e autorizzazione di Servizio app per proteggere le app di Servizio app end-to-end, incluso l'accesso alle API remote.
keywords: servizio app, Servizio app di Azure, autenticazione, autorizzazione, proteggere, sicurezza, multilivello, Azure Active Directory, Azure AD
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 8620d6bc403882cb308405e8ffb4412917d0c6f1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993467"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Esercitazione: Autenticare e autorizzare gli utenti end-to-end nel Servizio app di Azure

::: zone pivot="platform-windows"  

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Il servizio app include anche il supporto predefinito per [l'autenticazione e l'autorizzazione degli utenti](overview-authentication-authorization.md). Questa esercitazione illustra come proteggere le app con le funzionalità di autenticazione e autorizzazione del servizio app. Viene usata un'app ASP.NET Core con un front-end Angular.js a titolo di esempio. Le funzionalità di autenticazione e autorizzazione del servizio app supportano runtime di tutti i linguaggi e seguendo l'esercitazione si può apprendere come applicarle al linguaggio preferito.

::: zone-end

::: zone pivot="platform-linux"

Il [Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e applicazione automatica di patch basato sul sistema operativo Linux. Il servizio app include anche il supporto predefinito per [l'autenticazione e l'autorizzazione degli utenti](overview-authentication-authorization.md). Questa esercitazione illustra come proteggere le app con le funzionalità di autenticazione e autorizzazione del servizio app. Viene usata un'app ASP.NET Core con un front-end Angular.js a titolo di esempio. Le funzionalità di autenticazione e autorizzazione del servizio app supportano runtime di tutti i linguaggi e seguendo l'esercitazione si può apprendere come applicarle al linguaggio preferito.

::: zone-end

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

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* <a href="https://git-scm.com/" target="_blank">Installare Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installare l'ultima versione di .NET Core 3.1 SDK</a>

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

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Distribuire le app in Azure

In questo passaggio si distribuisce il progetto in due app del servizio app: una è l'app front-end e l'altra è l'app back-end.

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Creare le risorse di Azure

::: zone pivot="platform-windows"  

In Cloud Shell eseguire questi comandi per creare due app Web Windows. Sostituire _\<front-end-app-name>_ e _\<back-end-app-name>_ con due nomi di app univoci globali (i caratteri validi sono `a-z`, `0-9` e `-`). Per altre informazioni su ogni comando, vedere [API RESTful con CORS nel servizio app di Azure](app-service-web-tutorial-rest-api.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

::: zone pivot="platform-linux"

In Cloud Shell eseguire questi comandi per creare due app Web. Sostituire _\<front-end-app-name>_ e _\<back-end-app-name>_ con due nomi di app univoci globali (i caratteri validi sono `a-z`, `0-9` e `-`). Per altre informazioni su ogni comando, vedere [Creare un'app .NET Core nel Servizio app di Azure](quickstart-dotnetcore.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

> [!NOTE]
> Salvare gli URL degli elementi Git remoti per l'app front-end e l'app back-end, riportati nell'output di `az webapp create`.
>

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Nella _finestra del terminale locale_ eseguire questi comandi Git per la distribuzione nell'app back-end. Sostituire _\<deploymentLocalGitUrl-of-back-end-app>_ con l'URL dell'istanza remota di Git salvata nella sezione [Creare le risorse di Azure](#create-azure-resources). Quando Git Credential Manager richiede le credenziali, assicurarsi di immettere le [credenziali per la distribuzione](deploy-configure-credentials.md) e non quelle usate per accedere al portale di Azure.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Nella finestra del terminale locale eseguire questi comandi Git per distribuire lo stesso codice nell'app front-end. Sostituire _\<deploymentLocalGitUrl-of-front-end-app>_ con l'URL dell'istanza remota di Git salvata nella sezione [Creare le risorse di Azure](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Passare alle app

Passare agli URL seguenti in un browser e visualizzare le due app in esecuzione.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

:::image type="content" source="./media/tutorial-auth-aad/azure-run.png" alt-text="Screenshot di un esempio di API REST del servizio app di Azure in una finestra del browser che mostra un'app elenco attività.":::

> [!NOTE]
> Se l'app viene riavviata, si noterà che sono stati cancellati nuovi dati. Questo comportamento è determinato dalla progettazione, perché l'app ASP.NET Core usa un database in memoria.
>
>

## <a name="call-back-end-api-from-front-end"></a>Chiamare l'API back-end dal front-end

In questo passaggio si definisce il codice del server dell'app front-end per l'accesso all'API back-end. Successivamente si abiliterà l'accesso autenticato dal front-end al back-end.

### <a name="modify-front-end-code"></a>Modificare il codice del front-end

Nel repository locale aprire _Controllers/TodoController.cs_. All'inizio della classe `TodoController` aggiungere le righe seguenti sostituendo _\<back-end-app-name>_ con il nome dell'app back-end:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

Trovare il metodo decorato con `[HttpGet]` e sostituire il codice all'interno delle parentesi graffe con:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

La prima riga effettua una chiamata `GET /api/Todo` all'app per le API back-end.

Trovare quindi il metodo decorato con `[HttpGet("{id}")]` e sostituire il codice all'interno delle parentesi graffe con:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

La prima riga effettua una chiamata `GET /api/Todo/{id}` all'app per le API back-end.

Trovare quindi il metodo decorato con `[HttpPost]` e sostituire il codice all'interno delle parentesi graffe con:

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

La prima riga effettua una chiamata `POST /api/Todo` all'app per le API back-end.

Trovare quindi il metodo decorato con `[HttpPut("{id}")]` e sostituire il codice all'interno delle parentesi graffe con:

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

La prima riga effettua una chiamata `PUT /api/Todo/{id}` all'app per le API back-end.

Trovare quindi il metodo decorato con `[HttpDelete("{id}")]` e sostituire il codice all'interno delle parentesi graffe con:

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
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

Passare a `http://<front-end-app-name>.azurewebsites.net` e aggiungere alcuni elementi, ad esempio `from front end 1` e `from front end 2`.

Passare a `http://<back-end-app-name>.azurewebsites.net` per visualizzare gli elementi aggiunti dall'app front-end. Aggiungere anche alcuni elementi, ad esempio `from back end 1` e `from back end 2`, quindi aggiornare l'app front-end per verificare se riflette le modifiche.

:::image type="content" source="./media/tutorial-auth-aad/remote-api-call-run.png" alt-text="Screenshot di un esempio di API REST del servizio app di Azure in una finestra del browser che mostra un'app elenco attività con elementi aggiunti dall'app front-end.":::

## <a name="configure-auth"></a>Configurare l'autenticazione e l'autorizzazione

In questo passaggio si abilitano l'autenticazione e l'autorizzazione per le due app. Si configura anche l'app front-end per generare un token di accesso che potrà essere usato per effettuare chiamate autenticate all'app back-end.

Come provider di identità viene usato Azure Active Directory. Per altre informazioni, vedere [Configurare l'autenticazione di Azure Active Directory per un'applicazione dei servizi app](configure-authentication-provider-aad.md).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Abilitare l'autenticazione e l'autorizzazione per l'app back-end

Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Gruppi di risorse** oppure cercare e selezionare *Gruppi di risorse* da qualsiasi pagina.

In **Gruppi di risorse** cercare e selezionare il gruppo di risorse desiderato. In **Panoramica** selezionare la pagina di gestione dell'app back-end.

:::image type="content" source="./media/tutorial-auth-aad/portal-navigate-back-end.png" alt-text="Screenshot della finestra Gruppi di risorse, che mostra la Panoramica di un gruppo di risorse di esempio e la pagina di gestione di un'app back-end selezionata.":::

Nel menu a sinistra dell'app back-end selezionare **Autenticazione/Autorizzazione** e quindi abilitare l'autenticazione del servizio app scegliendo **Sì**.

In **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.

In **Provider di autenticazione** fare clic su **Azure Active Directory**.

:::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="Screenshot del menu a sinistra dell'app back-end che mostra Autenticazione/Autorizzazione selezionato e le impostazioni selezionate nel menu a destra.":::

Selezionare **Rapida**, quindi accettare le impostazioni predefinite per creare una nuova app AD e scegliere **OK**.

Nella pagina **Autenticazione/Autorizzazione** selezionare **Salva**.

Quando viene visualizzata la notifica con il messaggio `Successfully saved the Auth Settings for <back-end-app-name> App`, aggiornare la pagina del portale.

Selezionare di nuovo **Azure Active Directory** e quindi **App Azure AD**.

Copiare l'**ID client** dell'applicazione di Azure AD negli appunti. Questo valore sarà necessario in un secondo momento.

:::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Screenshot della finestra Impostazioni di Azure Active Directory che mostra l'app Azure AD e la finestra Applicazioni Azure AD che mostra l'ID client da copiare.":::

Se ci si ferma qui, si avrà un'app autonoma che è già protetta dall'autenticazione e dall'autorizzazione del servizio app. Le sezioni rimanenti illustrano come proteggere una soluzione di più app "propagando" l'utente autenticato dal front-end al back-end. 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Abilitare l'autenticazione e l'autorizzazione per l'app front-end

Seguire la stessa procedura per l'app front-end, ignorando però l'ultimo passaggio. Per l'app front-end non è necessario l'ID client.

Se si vuole, passare a `http://<front-end-app-name>.azurewebsites.net`. Si verrà indirizzati a una pagina di accesso protetta. Dopo aver eseguito l'accesso, *non è ancora possibile accedere ai dati dall'app back-end* perché l'app back-end richiede ora l'accesso di Azure Active Directory dall'app front-end. È necessario eseguire tre operazioni:

- Concedere al front-end l'accesso al back-end
- Configurare il servizio app per la restituzione di un token utilizzabile
- Usare il token nel codice

> [!TIP]
> Se si verificano errori e le impostazioni di autenticazione/autorizzazione dell'app vengono riconfigurate, i token nell'archivio token potrebbero non essere rigenerati dalle nuove impostazioni. Per assicurarsi che i token vengano rigenerati, è necessario disconnettersi e accedere di nuovo all'app. Un modo semplice per eseguire tale operazione consiste nell'usare il browser in modalità privata, chiudendo e riaprendo il browser in modalità privata dopo aver modificato le impostazioni nelle app.

### <a name="grant-front-end-app-access-to-back-end"></a>Concedere all'app front-end l'accesso al back-end

Dopo l'abilitazione dell'autenticazione e dell'autorizzazione per entrambe le app, ognuna di queste è supportata da un'applicazione AD. In questo passaggio si concedono all'app front-end le autorizzazioni necessarie per accedere al back-end per conto dell'utente. Tecnicamente, si concedono all'_applicazione AD_ del front-end le autorizzazioni per accedere all'_applicazione AD_ del back-end per conto dell'utente.

Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** oppure cercare e selezionare *Azure Active Directory* da qualsiasi pagina.

Selezionare **Registrazioni app** > **Applicazioni di cui si è proprietari** > **Visualizza tutte le applicazioni nella directory**. Selezionare il nome dell'app front-end e quindi scegliere **Autorizzazioni API**.

:::image type="content" source="./media/tutorial-auth-aad/add-api-access-front-end.png" alt-text="Screenshot della finestra Microsoft - Registrazioni app con le applicazioni di proprietà, il nome di un'app front-end e le autorizzazioni API selezionate.":::

Selezionare **Aggiungi un'autorizzazione** e quindi **API usate dall'organizzazione** >  **\<back-end-app-name>** .

Nella pagina **Richiedi le autorizzazioni dell'API** per l'app back-end, selezionare **Autorizzazioni delegate** e **user_impersonation**, quindi selezionare **Aggiungere autorizzazioni**.

:::image type="content" source="./media/tutorial-auth-aad/select-permission-front-end.png" alt-text="Screenshot della pagina Richiedi le autorizzazioni dell'API che mostra le autorizzazioni delegate, user_impersonation e il pulsante Aggiungi autorizzazione selezionato.":::

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Configurare il servizio app per la restituzione di un token di accesso utilizzabile

L'app front-end ha ora le autorizzazioni necessarie per accedere all'app back-end come utente connesso. In questo passaggio si configurano l'autenticazione e l'autorizzazione del servizio app affinché forniscano un token di accesso utilizzabile per accedere al back-end. Per questo passaggio è necessario l'ID client del back-end copiato in [Abilitare l'autenticazione e l'autorizzazione per l'app back-end](#enable-authentication-and-authorization-for-back-end-app).

Passare ad [Azure Resource Explorer](https://resources.azure.com) e usare l'albero delle risorse per individuare l'app Web front-end.

[Azure Resource Explorer](https://resources.azure.com) viene aperto con l'app front-end selezionata nell'albero delle risorse. Nella parte superiore della pagina fare clic su **Read/Write** (Lettura/scrittura) per abilitare la modifica delle risorse di Azure.

:::image type="content" source="./media/tutorial-auth-aad/resources-enable-write.png" alt-text="Screenshot dei pulsanti di sola lettura e di lettura/scrittura nella parte superiore della pagina Azure Resource Explorer, con il pulsante lettura/scrittura selezionato.":::

Nel browser di sinistra eseguire il drill-down fino a **config** > **authsettings**.

Nella visualizzazione **authsettings** fare clic su **Edit** (Modifica). Impostare `additionalLoginParams` sulla stringa JSON seguente, usando l'ID client copiato. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

:::image type="content" source="./media/tutorial-auth-aad/additional-login-params-front-end.png" alt-text="Screenshot di un esempio di codice nella visualizzazione authsettings che mostra la stringa additionalLoginParams con un esempio di ID client.":::

Salvare le impostazioni facendo clic su **PUT**.

Le app sono ora configurate. Il front-end è pronto per accedere al back-end con un token di accesso appropriato.

Per informazioni su come configurare il token di accesso per altri provider, vedere [Aggiornare i token del provider di identità](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="call-api-securely-from-server-code"></a>Chiamare l'API in modo sicuro dal codice del server

In questo passaggio si abilita il codice del server modificato in precedenza per effettuare chiamate autenticate all'API back-end.

L'app front-end ha ora l'autorizzazione necessaria e aggiunge inoltre l'ID client del back-end ai parametri di accesso. Può quindi ottenere un token di accesso per l'autenticazione con l'app back-end. Il servizio app passa questo token al codice del server inserendo un'intestazione `X-MS-TOKEN-AAD-ACCESS-TOKEN` in ogni richiesta autenticata. Vedere [Retrieve tokens in app code](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) (Recuperare i token nel codice dell'app).

> [!NOTE]
> Queste intestazioni vengono inserite per tutti i linguaggi supportati. Per accedervi, usare il modello standard per il rispettivo linguaggio.

Nel repository locale aprire di nuovo _Controllers/TodoController.cs_. Sotto il costruttore `TodoController(TodoContext context)` aggiungere il codice seguente:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Questo codice aggiunge l'intestazione HTTP standard `Authorization: Bearer <access-token>` a tutte le chiamate ad API remote. Nella pipeline di esecuzione della richiesta ASP.NET Core MVC `OnActionExecuting` viene eseguito subito prima dell'azione, di conseguenza ogni chiamata API in uscita presenta ora il token di accesso.

Salvare tutte le modifiche. Nella finestra del terminale locale distribuire le modifiche nell'app front-end con i comandi Git seguenti:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Accedere di nuovo a `https://<front-end-app-name>.azurewebsites.net`. Nella pagina del contratto per l'utilizzo dei dati utente fare clic su **Accetta**.

Dovrebbe ora essere possibile creare, leggere, aggiornare ed eliminare i dati dell'app back-end come prima. L'unica differenza consiste nel fatto che ora entrambe le app, incluse le chiamate da servizio a servizio, sono protette dall'autenticazione e dall'autorizzazione del servizio app.

Congratulazioni! Il codice del server accede ora ai dati del back-end per conto dell'utente autenticato.

## <a name="call-api-securely-from-browser-code"></a>Chiamare l'API in modo sicuro dal codice del browser

In questo passaggio si configura l'app Angular.js front-end in modo che punti all'API back-end. In questo modo si apprenderà come recuperare il token di accesso ed effettuare chiamate API all'app back-end con tale token.

Mentre il codice del server ha accesso alle intestazioni delle richieste, il codice del client può ottenere gli stessi token di accesso accedendo a `GET /.auth/me`. Vedere [Retrieve tokens in app code](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) (Recuperare i token nel codice dell'app).

> [!TIP]
> Questa sezione illustra le chiamate HTTP sicure usando i metodi HTTP standard. È tuttavia possibile usare [Microsoft Authentication Library per JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) per semplificare il modello di applicazione Angular.js.
>

### <a name="configure-cors"></a>Configurare CORS

In Cloud Shell abilitare CORS per l'URL del client usando il comando [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add). Sostituire i segnaposto _\<back-end-app-name>_ e _\<front-end-app-name>_ .

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

Questo passaggio non è correlato all'autenticazione e all'autorizzazione. È tuttavia necessario affinché il browser consenta chiamate API tra domini dall'app Angular.js. Per altre informazioni, vedere [Aggiungere funzionalità CORS](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Puntare l'app Angular.js all'API back-end

Nel repository locale aprire _wwwroot/index.html_.

A riga 51 impostare la variabile `apiEndpoint` sull'URL HTTPS dell'app back-end (`https://<back-end-app-name>.azurewebsites.net`). Sostituire _\<back-end-app-name>_ con il nome dell'app nel servizio app.

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

La nuova modifica aggiunge il mapping `resolve`, che chiama `/.auth/me` e imposta il token di accesso. Viene così garantita la disponibilità del token di accesso prima della creazione di un'istanza del controller `todoListCtrl`. In questo modo, tutte le chiamate API da parte del controller includeranno il token.

### <a name="deploy-updates-and-test"></a>Distribuire aggiornamenti ed eseguire un test

Salvare tutte le modifiche. Nella finestra del terminale locale distribuire le modifiche nell'app front-end con i comandi Git seguenti:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Passare di nuovo a `https://<front-end-app-name>.azurewebsites.net`. Dovrebbe ora essere possibile creare, leggere, aggiornare ed eliminare i dati dell'app back-end direttamente nell'app Angular.js.

Congratulazioni! Il codice del client accede ora ai dati del back-end per conto dell'utente autenticato.

## <a name="when-access-tokens-expire"></a>Quando scadono i token di accesso

Il token di accesso scade dopo un certo periodo di tempo. Per informazioni su come aggiornare i token di accesso senza chiedere agli utenti di autenticarsi di nuovo nell'app, vedere [Refresh identity provider tokens](app-service-authentication-how-to.md#refresh-identity-provider-tokens) (Aggiornare i token del provider di identità).

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

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente al Servizio app di Azure](app-service-web-tutorial-custom-domain.md)
