---
title: "Esercitazione: Accesso dell'app Web a Microsoft Graph come utente | Azure"
description: Questa esercitazione illustra come accedere ai dati in Microsoft Graph per conto di un utente connesso.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ef007f045a5c53bf70f6d042167c157ab3f4decc
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428254"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>Esercitazione: Accedere a Microsoft Graph da un'app protetta come utente

Informazioni su come accedere a Microsoft Graph da un'app Web in esecuzione nel Servizio app di Azure.

:::image type="content" alt-text="Accesso a Microsoft Graph" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Si vuole aggiungere l'accesso a Microsoft Graph dall'app Web ed eseguire alcune azioni come utente connesso. Questa sezione descrive come concedere le autorizzazioni delegate all'app Web e ottenere le informazioni sul profilo dell'utente che ha eseguito l'accesso da Azure Active Directory.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Concedere le autorizzazioni delegate a un'app Web
> * Chiamare Microsoft Graph da un'app Web per conto di un utente connesso

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Un'applicazione Web in esecuzione nel Servizio app di Azure con il [modulo di autenticazione/autorizzazione del servizio app abilitato](scenario-secure-app-authentication-app-service.md).

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>Concedere l'accesso front-end per chiamare Microsoft Graph

Dopo aver abilitato l'autenticazione e l'autorizzazione nell'app Web, l'app Web viene registrata con Microsoft Identity Platform ed è supportata da un'applicazione Azure AD. In questo passaggio si concedono all'app Web le autorizzazioni necessarie per accedere a Microsoft Graph per conto dell'utente. Tecnicamente, si concedono all'applicazione Azure AD dell'app Web le autorizzazioni per accedere all'applicazione Azure AD di Microsoft Graph per conto dell'utente.

Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** oppure cercare e selezionare Azure Active Directory da qualsiasi pagina.

Selezionare **Registrazioni app** > **Applicazioni di cui si è proprietari** > **Visualizza tutte le applicazioni nella directory**. Selezionare il nome dell'app Web e quindi scegliere **Autorizzazioni API**.

Selezionare **Aggiungi un'autorizzazione**, quindi selezionare API Microsoft e Microsoft Graph.

Selezionare **Autorizzazioni delegate** e selezionare **User.Read** dall'elenco.  Fare clic su **Aggiungi autorizzazioni**.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>Configurare il servizio app per la restituzione di un token di accesso utilizzabile

L'app Web ha ora le autorizzazioni necessarie per accedere a Microsoft Graph come utente connesso. In questo passaggio si configurano l'autenticazione e l'autorizzazione del servizio app affinché forniscano un token di accesso utilizzabile per accedere a Microsoft Graph. Per questo passaggio, è necessario l'ID client/App del servizio downstream (Microsoft Graph). *00000003-0000-0000-c000-000000000000* è l'ID app di Microsoft Graph.

> [!IMPORTANT]
> Se non si configura il servizio app per restituire un token di accesso utilizzabile, viene visualizzato un errore ```CompactToken parsing failed with error code: 80049217``` quando si chiamano le API Microsoft Graph nel codice.

Passare ad [Azure Resource Explorer](https://resources.azure.com/) e usare l'albero delle risorse per individuare l'app Web.  L'URL della risorsa sarà simile a: `https://resources.azure.com/subscriptions/subscription-id/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914`

Azure Resource Explorer viene aperto con l'app Web selezionata nell'albero delle risorse. Nella parte superiore della pagina fare clic su **Read/Write** (Lettura/scrittura) per abilitare la modifica delle risorse di Azure.

Nel browser di sinistra eseguire il drill-down fino a **config** > **authsettings**.

Nella visualizzazione **authsettings** fare clic su **Edit** (Modifica). Impostare ```additionalLoginParams``` sulla stringa JSON seguente, usando l'ID client copiato.

```json
"additionalLoginParams": ["response_type=code id_token","resource=00000003-0000-0000-c000-000000000000"],
```

Salvare le impostazioni facendo clic su **PUT**. Potrebbero essere necessari alcuni minuti prima che questa impostazione diventi effettiva.  L'app Web è ora configurata per accedere a Microsoft Graph con un token di accesso appropriato.  In caso contrario, Microsoft Graph restituisce un errore che indica che il formato del token compatto non è corretto.

## <a name="call-microsoft-graph-net"></a>Chiamare Microsoft Graph (.NET)

L'app Web ha ora le autorizzazioni necessarie e aggiunge inoltre l'ID client di Microsoft Graph ai parametri di accesso. Usando la [libreria Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/), l'app Web ottiene un token di accesso per l'autenticazione con Microsoft Graph. Nella versione 1.2.0 e successive la libreria Microsoft.Identity.Web si integra con il modulo di autenticazione/autorizzazione del servizio app e può essere eseguita unitamente ad esso.  La libreria Microsoft.Identity.Web rileva che l'app Web è ospitata in Servizi app e ottiene il token di accesso dal modulo di autenticazione/autorizzazione di Servizi app.  Il token di accesso viene quindi passato alle richieste autenticate con l'API Microsoft Graph.

> [!NOTE]
> Nell'app Web la libreria Microsoft.Identity.Web non è necessaria per l'autenticazione/autorizzazione di base o per autenticare le richieste con Microsoft Graph.  È possibile [chiamare in modo sicuro le API downstream](tutorial-auth-aad.md#call-api-securely-from-server-code) solo con il modulo di autenticazione/autorizzazione del servizio app abilitato.  
> Tuttavia, il modulo di autenticazione/autorizzazione del servizio app è progettato per scenari di autenticazione più semplici.  Per gli scenari più complessi, ad esempio la gestione di attestazioni personalizzate, è necessario usare la libreria Microsoft.Identity.Web o [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview). Inizialmente, richiede un maggior numero operazioni di configurazione, ma la libreria Microsoft.Identity.Web può essere eseguita insieme al modulo di autenticazione/autorizzazione del servizio app.  In seguito, quando l'app Web deve gestire scenari più complessi, è possibile disabilitare il modulo di autenticazione/autorizzazione del servizio app e la libreria Microsoft.Identity.Web farà già parte dell'app.

### <a name="install-client-library-packages"></a>Installare i pacchetti della libreria client

Installare i pacchetti NuGet [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) e [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph) nel progetto usando l'interfaccia della riga di comando di .NET Core o la Console di Gestione pacchetti in Visual Studio.

# <a name="command-line"></a>[Riga di comando](#tab/command-line)

Aprire una riga di comando e passare alla directory che contiene il file di progetto.

Eseguire i comandi di installazione:

```dotnetcli
dotnet add package Microsoft.Graph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[Gestione pacchetti](#tab/package-manager)
Aprire il progetto o la soluzione in Visual Studio e aprire la console usando il comando **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.

Eseguire i comandi di installazione:
```powershell
Install-Package Microsoft.Graph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

Nel file *Startup.cs* il metodo ```AddMicrosoftIdentityWebApp``` aggiunge la libreria Microsoft.Identity.Web all'app Web.  Il metodo ```AddMicrosoftGraph``` aggiunge il supporto per Microsoft Graph.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

*AzureAd* specifica la configurazione per la libreria Microsoft.Identity.Web.  Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** dal menu del portale e quindi scegliere **Registrazioni app**. Selezionare la registrazione dell'app creata quando è stato abilitato il modulo di autenticazione/autorizzazione del servizio app (il nome della registrazione app deve corrispondere a quello dell'app Web).  L'ID tenant e l'ID client sono disponibili nella pagina di panoramica della registrazione app.  Il nome di dominio è disponibile nella pagina di panoramica di Azure Active Directory per il tenant in uso.

*Graph* specifica l'endpoint Microsoft Graph e gli ambiti iniziali necessari per l'app.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

Nell'esempio seguente viene illustrato come chiamare Microsoft Graph come utente connesso e ottenere alcune informazioni sull'utente.  L'oggetto ```GraphServiceClient``` viene inserito nel controller e l'autenticazione è stata configurata automaticamente dalla libreria Microsoft.Identity.Web.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'esercitazione è stata completata e l'app Web o le risorse associate non sono più necessarie, [pulire le risorse create](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Concedere le autorizzazioni delegate a un'app Web
> * Chiamare Microsoft Graph da un'app Web per conto di un utente connesso

> [!div class="nextstepaction"]
> [Il Servizio app accede a Microsoft Graph come app](scenario-secure-app-access-microsoft-graph-as-app.md)
