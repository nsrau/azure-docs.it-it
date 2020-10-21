---
title: Avvio rapido per l'aggiunta di flag funzionalità ad ASP.NET Core
description: Aggiungere flag funzionalità alle app ASP.NET Core e gestirli in Configurazione app di Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: 866f1c404df2de87c2b3ce58b791ceb5257fca1b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074448"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Guida di avvio rapido: Aggiungere i flag di funzionalità a un'app ASP.NET Core

In questa guida di avvio rapido si crea un'implementazione end-to-end di gestione di funzionalità in un'app ASP.NET Core tramite Configurazione app di Azure. Verrà usato il servizio Configurazione app per archiviare tutti i flag di funzionalità in una posizione centralizzata e controllarne gli stati. 

Le librerie di Gestione funzionalità di .NET Core estendono il framework con il supporto completo per i flag di funzionalità. Queste librerie si basano sul sistema di configurazione di .NET Core. Si integrano facilmente con Configurazione app tramite il provider di configurazione di .NET Core.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/dotnet)
* [ASP.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

8. Selezionare **Operazioni** > **Gestione funzionalità** > **Aggiungi** per aggiungere un flag di funzionalità denominato *Beta*.

    > [!div class="mx-imgBorder"]
    > ![Abilitare un flag funzionalità denominato Beta](media/add-beta-feature-flag.png)

    Lasciare il campo **Etichetta** vuoto per il momento. Selezionare **Applica** per salvare il nuovo flag di funzionalità.

## <a name="create-an-aspnet-core-web-app"></a>Creare un'app Web ASP.NET Core

Usare l'[interfaccia della riga di comando di .NET Core](/dotnet/core/tools) per creare un nuovo progetto MVC ASP.NET Core. Il vantaggio di usare l'interfaccia della riga di comando di .NET Core anziché Visual Studio è che è disponibile nelle piattaforme Windows, macOS e Linux.

Eseguire il comando seguente per creare un progetto MVC ASP.NET Core in una nuova cartella *TestFeatureFlags*:

```dotnetcli
dotnet new mvc --no-https --output TestFeatureFlags
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di Configurazione app

1. Installare i pacchetti NuGet [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) e [Microsoft.FeatureManagement.AspNetCore](https://www.nuget.org/packages/Microsoft.FeatureManagement.AspNetCore) eseguendo questi comandi:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

    ```dotnetcli
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Eseguire il comando seguente nella stessa directory del file con estensione *csproj*. Il comando usa Secret Manager per archiviare un segreto denominato `ConnectionStrings:AppConfig`, che archivia la stringa di connessione per l'archivio di Configurazione app. Sostituire il segnaposto `<your_connection_string>` con la stringa di connessione dell'archivio di Configurazione app. È possibile trovare la stringa di connessione in **Chiavi di accesso** nel portale di Azure.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    Secret Manager viene usato solo per testare l'app Web in locale. Quando l'app viene distribuita in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/web), usare l'impostazione **Stringhe di connessione** dell'applicazione nel servizio app invece di Secret Manager per archiviare la stringa di connessione.

    Accedere a questo segreto usando l'API di configurazione di .NET Core. Con tale API è possibile usare i due punti (`:`) nel nome della configurazione in tutte le piattaforme supportate. Per altre informazioni, vedere [Chiavi e valori di configurazione](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. In *Program.cs* aggiornare il metodo `CreateWebHostBuilder` in modo da usare Configurazione app chiamando il metodo `AddAzureAppConfiguration`.

    > [!IMPORTANT]
    > `CreateHostBuilder` sostituisce `CreateWebHostBuilder` in .NET Core 3.x. Selezionare la sintassi corretta in base all'ambiente.

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration(config =>
               {
                   var settings = config.Build();
                   var connection = settings.GetConnectionString("AppConfig");
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(connection).UseFeatureFlags());
               }).UseStartup<Startup>();
    ```

    ---

    Con la modifica precedente, il [provider di configurazione per Configurazione app](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) è stato registrato con l'API di configurazione di .NET Core.

1. In *Startup.cs* aggiungere un riferimento alla gestione funzionalità di .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Aggiornare il metodo `Startup.ConfigureServices` per aggiungere il supporto dei flag di funzionalità chiamando il metodo `AddFeatureManagement`. Facoltativamente, includere eventuali filtri da usare con i flag di funzionalità chiamando `AddFeatureFilter<FilterType>()`:

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        services.AddFeatureManagement();
    }
    ```

    ---

1. Aggiungere un file *MyFeatureFlags.cs* alla directory radice del progetto con il codice seguente:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Aggiungere un file *BetaController.cs* alla directory *Controllers* con il codice seguente:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager) =>
                _featureManager = featureManager;

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index() => View();
        }
    }
    ```

1. In *views/_ViewImports.cshtml* registrare l'helper tag di gestione funzionalità usando una direttiva `@addTagHelper`:

    ```cshtml
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

    Il codice precedente consente l'uso dell'helper tag `<feature>` nei file con estensione *cshtml* del progetto.

1. In *Views/Shared/_Layout.cshtml* sostituire il codice a barre `<nav>` in `<body>` > `<header>` con il markup seguente:

    ```cshtml
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

    Nel markup precedente notare l'helper tag `<feature>` che circonda la voce dell'elenco *Beta*.

1. Creare una directory *Views/Beta* e un file *Index.cshtml* contenente il markup seguente:

    ```cshtml
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>This is the beta website.</h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire questo comando nella shell dei comandi:

    ```dotnetcli
    dotnet build
    ```

1. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

    ```dotnetcli
    dotnet run
    ```

1. Aprire una finestra del browser e passare a `http://localhost:5000`, che è l'URL predefinito per l'app Web ospitata in locale. Se si sta lavorando in Azure Cloud Shell, selezionare il pulsante **Anteprima Web** e quindi **Configura**. Quando richiesto, selezionare la porta 5000.

    ![Individuare il pulsante Anteprima Web](./media/quickstarts/cloud-shell-web-preview.png)

    Nel browser verrà visualizzata una pagina simile all'immagine seguente.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-before.png" alt-text="App di avvio rapido locale prima della modifica" border="true":::

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di Configurazione app creata nell'argomento di avvio rapido.

1. Selezionare **Gestione funzionalità** e cambiare lo stato della chiave *Beta* impostandolo su **Sì**.

1. Tornare nella shell dei comandi. Annullare il processo `dotnet` in esecuzione premendo <kbd>CTRL+C</kbd>. Riavviare l'app usando `dotnet run`.

1. Aggiornare la pagina del browser per visualizzare le nuove impostazioni di configurazione.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-after.png" alt-text="App di avvio rapido locale prima della modifica" border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un nuovo archivio di Configurazione app, che è stato usato per gestire le funzionalità in un'app Web ASP.NET Core con le [librerie di gestione delle funzionalità](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration).

* Altre informazioni sulla [gestione delle funzionalità](./concept-feature-management.md).
* [Gestire i flag di funzionalità](./manage-feature-flags.md).
* [Usare i flag di funzionalità in un'app ASP.NET Core](./use-feature-flags-dotnet-core.md).
* [Usare la configurazione dinamica in un'app ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)