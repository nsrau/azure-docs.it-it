---
title: Avvio rapido per l'aggiunta di flag funzionalità ad ASP.NET Core
description: Aggiungere flag funzionalità alle app ASP.NET Core e gestirli in Configurazione app di Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: d8582dfc796fe3e87b8bdc5be763dddfb5d0176b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245413"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Guida di avvio rapido: Aggiungere i flag di funzionalità a un'app ASP.NET Core

Questa guida di avvio rapido illustra come creare un'implementazione end-to-end di gestione delle funzionalità in un'applicazione ASP.NET Core tramite Configurazione app di Azure. Verrà usato il servizio Configurazione app per archiviare tutti i flag funzionalità in una posizione centralizzata e controllarne gli stati. 

Le librerie di Gestione funzionalità di .NET Core estendono il framework con il supporto completo per i flag di funzionalità. Queste librerie si basano sul sistema di configurazione di .NET Core. Si integrano facilmente con Configurazione app tramite il provider di configurazione di .NET Core.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selezionare **Gestione funzionalità** >  **+Aggiungi** per aggiungere un flag funzionalità denominato `Beta`.

    > [!div class="mx-imgBorder"]
    > ![Abilitare un flag funzionalità denominato Beta](media/add-beta-feature-flag.png)

    Per il momento lasciare il campo `label` non definito. Selezionare **Applica** per salvare il nuovo flag di funzionalità.

## <a name="create-an-aspnet-core-web-app"></a>Creare un'app Web ASP.NET Core

Usare l'[interfaccia della riga di comando di .NET Core](https://docs.microsoft.com/dotnet/core/tools/) per creare un nuovo progetto di app Web MVC ASP.NET Core. Il vantaggio di usare l'interfaccia della riga di comando di .NET Core anziché Visual Studio è che è disponibile nelle piattaforme Windows, macOS e Linux.

1. Creare una nuova cartella per il progetto. Per questo argomento di avvio rapido assegnarle il nome *TestFeatureFlags*.

1. Nella nuova cartella eseguire il comando seguente per creare un nuovo progetto di app Web ASP.NET Core MVC:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Aggiungere Secret Manager

Per usare Secret Manager, aggiungere un elemento `UserSecretsId` al file con estensione *csproj*.

1. Aprire il file con estensione *csproj*.

1.  Aggiungere un elemento `UserSecretsId` come illustrato di seguito. È possibile usare lo stesso GUID oppure sostituire questo valore con uno proprio.

    > [!IMPORTANT]
    > `CreateHostBuilder` sostituisce `CreateWebHostBuilder` in .NET Core 3.0.  Selezionare la sintassi corretta in base all'ambiente.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>

    </Project>
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

    </Project>
    ```
    ---

1. Salvare il file con estensione *csproj*.

Lo strumento Secret Manager archivia i dati sensibili per operazioni di sviluppo al di fuori dell'albero del progetto. Questo approccio contribuisce a impedire la condivisione accidentale dei segreti dell'app all'interno del codice sorgente.

> [!TIP]
> Per altre informazioni su Secret Manager, vedere [Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets).

## <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di Configurazione app

1. Aggiungere il riferimento ai pacchetti NuGet `Microsoft.Azure.AppConfiguration.AspNetCore` e `Microsoft.FeatureManagement.AspNetCore` eseguendo i comandi seguenti:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Eseguire il comando seguente per ripristinare i pacchetti per il progetto:

    ```dotnetcli
    dotnet restore
    ```

1. Aggiungere un segreto denominato **ConnectionStrings:AppConfig** a Secret Manager.

    Questo segreto contiene la stringa di connessione per accedere all'archivio di Configurazione app. Sostituire il valore `<your_connection_string>` nel comando seguente con la stringa di connessione per l'archivio di Configurazione app. È possibile trovare la stringa di connessione in **Chiavi di accesso** nel portale di Azure.

    Questo comando deve essere eseguito nella stessa directory del file con estensione *csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Secret Manager si usa solo per testare l'app Web in locale. Quando si distribuisce l'app in [Servizio app di Azure](https://azure.microsoft.com/services/app-service), ad esempio, si usa un'impostazione applicazione denominata **Stringhe di connessione** nel servizio app, invece di usare Secret Manager per archiviare la stringa di connessione.

    È possibile accedere a questo segreto con l'API Configurazione app. Con tale API è possibile usare i due punti (:) nel nome di configurazione in tutte le piattaforme supportate. Vedere la [configurazione in base all'ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Aggiornare il metodo `CreateWebHostBuilder` per usare Configurazione app effettuando una chiamata al metodo `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` sostituisce `CreateWebHostBuilder` in .NET Core 3.0.  Selezionare la sintassi corretta in base all'ambiente.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                        .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```
    ---

1. Aprire *Startup.cs* e aggiungere i riferimenti alla gestione funzionalità di .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Aggiornare il metodo `ConfigureServices` per aggiungere il supporto dei flag di funzionalità chiamando il metodo `services.AddFeatureManagement()`. Facoltativamente, includere eventuali filtri da usare con i flag di funzionalità chiamando `services.AddFeatureFilter<FilterType>()`:

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```
    ---

1. Aggiornare il metodo `Configure` per aggiungere un middleware e consentire l'aggiornamento dei valori dei flag di funzionalità a intervalli ricorrenti mentre l'app Web ASP.NET Core continua a ricevere richieste.
    
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. Aggiungere un file *MyFeatureFlags.cs*:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Aggiungere *BetaController.cs* alla directory *Controllers*:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Aprire *_ViewImports.cshtml* nella directory *Views* e aggiungere l'helper per i tag di gestione funzionalità:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Aprire il file *_Layout.cshtml* nella directory *Views*\\*Shared* e sostituire il codice nella barra `<nav>` sotto `<body>` > `<header>` con il codice seguente:

    ```html
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

1. Creare una directory *Beta* sotto *Views* e aggiungervi *Index.cshtml*:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire questo comando nella shell dei comandi:

    ```
    dotnet build
    ```

1. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

    ```
    dotnet run
    ```

1. Aprire una finestra del browser e passare a `https://localhost:5000`, che è l'URL predefinito per l'app Web ospitata in locale.
    Se si sta lavorando in Azure Cloud Shell, selezionare il pulsante *Anteprima Web* e quindi *Configura*.  Quando richiesto, selezionare la porta 5000.

    ![Individuare il pulsante Anteprima Web](./media/quickstarts/cloud-shell-web-preview.png)

    Nel browser verrà visualizzata una pagina simile all'immagine seguente.
    ![Avvio rapido: avvio dell'app in locale](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di Configurazione app creata nell'argomento di avvio rapido.

1. Selezionare **Gestione funzionalità** e modificare lo stato della chiave **Beta** impostandolo su **Sì**.

1. Tornare al prompt dei comandi e annullare il processo `dotnet` in esecuzione premendo `Ctrl-C`.  Riavviare l'applicazione usando `dotnet run`.

1. Aggiornare la pagina del browser per visualizzare le nuove impostazioni di configurazione.

    ![Guida di avvio rapido: avvio dell'app in locale](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un nuovo archivio di Configurazione app, che è stato usato per gestire le funzionalità in un'app Web ASP.NET Core con le [librerie di gestione delle funzionalità](https://go.microsoft.com/fwlink/?linkid=2074664).

- Altre informazioni sulla [gestione delle funzionalità](./concept-feature-management.md).
- [Gestire i flag di funzionalità](./manage-feature-flags.md).
- [Usare i flag di funzionalità in un'app ASP.NET Core](./use-feature-flags-dotnet-core.md).
- [Usare la configurazione dinamica in un'app ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)
