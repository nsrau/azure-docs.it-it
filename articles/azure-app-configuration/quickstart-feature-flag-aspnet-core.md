---
title: Avvio rapido per l'aggiunta di flag di funzionalità ad ASP.NET Core | Microsoft Docs
description: Avvio rapido per l'aggiunta di flag funzionalità alle app ASP.NET Core e gestione di tali app in Configurazione app di Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 1a3d917775f6ba5b0b7f62d19de2b970a8b36838
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571209"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Guida introduttiva: Aggiungere i flag di funzionalità a un'app ASP.NET Core

In questa guida di avvio rapido si incorpora Configurazione app di Azure in un'app Web ASP.NET Core per creare un'implementazione end-to-end della gestione delle funzionalità. È possibile usare il servizio Configurazione app per archiviare tutti i flag di funzionalità in una posizione centralizzata e controllarne gli stati. 

Le librerie di Gestione funzionalità di .NET Core estendono il framework con il supporto completo per i flag di funzionalità. Queste librerie si basano sul sistema di configurazione di .NET Core. Si integrano facilmente con Configurazione app tramite il provider di configurazione di .NET Core.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Creare un archivio di configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selezionare **Gestione funzionalità** >  **+Aggiungi** per aggiungere i flag di funzionalità seguenti:

    | Chiave | Stato |
    |---|---|
    | Beta | Off |

## <a name="create-an-aspnet-core-web-app"></a>Creare un'app Web ASP.NET Core

È possibile usare l'[interfaccia della riga di comando di .NET Core](https://docs.microsoft.com/dotnet/core/tools/) per creare un nuovo progetto di app Web MVC ASP.NET Core. Il vantaggio di usare l'interfaccia della riga di comando di .NET Core anziché Visual Studio è che è disponibile nelle piattaforme Windows, macOS e Linux.

1. Creare una nuova cartella per il progetto. Per questo argomento di avvio rapido assegnarle il nome *TestFeatureFlags*.

1. Nella nuova cartella eseguire il comando seguente per creare un nuovo progetto di app Web ASP.NET Core MVC:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Aggiungere Secret Manager

Aggiungere lo [strumento Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) al progetto. Lo strumento Secret Manager archivia i dati sensibili per operazioni di sviluppo al di fuori dell'albero del progetto. Questo approccio contribuisce a impedire la condivisione accidentale dei segreti dell'app all'interno del codice sorgente.

1. Aprire il file con estensione *csproj*.
1. Aggiungere un elemento `UserSecretsId` come illustrato nell'esempio seguente e sostituire il relativo valore con il proprio, che in genere è un GUID:

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

1. Salvare il file.

## <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di configurazione app

1. Aggiungere il riferimento ai pacchetti NuGet `Microsoft.Azure.AppConfiguration.AspNetCore` e `Microsoft.FeatureManagement.AspNetCore` eseguendo i comandi seguenti:

    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009470001-12
    dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-009000001-1251
    ```

1. Eseguire il comando seguente per ripristinare i pacchetti per il progetto:

    ```
    dotnet restore
    ```

1. Aggiungere un segreto denominato **ConnectionStrings:AppConfig** a Secret Manager.

    Questo segreto contiene la stringa di connessione per accedere all'archivio di Configurazione app. Sostituire il valore `<your_connection_string>` nel comando seguente con la stringa di connessione per l'archivio di Configurazione app.

    Questo comando deve essere eseguito nella stessa directory del file con estensione *csproj*.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Secret Manager si usa solo per testare l'app Web in locale. Quando si distribuisce l'app in [Servizio app di Azure](https://azure.microsoft.com/services/app-service), ad esempio, si usa un'impostazione applicazione denominata **Stringhe di connessione** nel servizio app, invece di usare Secret Manager per archiviare la stringa di connessione.

    È possibile accedere a questo segreto con l'API Configurazione app. Con tale API è possibile usare i due punti (:) nel nome di configurazione in tutte le piattaforme supportate. Vedere la [configurazione in base all'ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Aprire *Program.cs* e aggiungere un riferimento al provider di Configurazione app .NET Core:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Aggiornare il metodo `CreateWebHostBuilder` per usare Configurazione app effettuando una chiamata al metodo `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` sostituisce `CreateWebHostBuilder` in .NET Core 3.0.  Selezionare la sintassi corretta in base all'ambiente.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>Aggiornare `CreateWebHostBuilder` per .NET Core 2.x

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

    ### <a name="update-createhostbuilder-for-net-core-3x"></a>Aggiornare `CreateHostBuilder` per .NET Core 3.x

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"])
                .UseFeatureFlags();
        })
        .UseStartup<Startup>());
    ```


1. Aprire *Startup.cs* e aggiungere i riferimenti alla gestione funzionalità di .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Aggiornare il metodo `ConfigureServices` per aggiungere il supporto dei flag di funzionalità chiamando il metodo `services.AddFeatureManagement()`. Facoltativamente, includere eventuali filtri da usare con i flag di funzionalità chiamando `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Aggiornare il metodo `Configure` per aggiungere un middleware e consentire l'aggiornamento dei valori dei flag di funzionalità a intervalli ricorrenti mentre l'app Web ASP.NET Core continua a ricevere richieste.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```

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

1. Aprire una finestra del browser e passare a `https://localhost:5001`, che è l'URL predefinito per l'app Web ospitata in locale.

    ![Guida introduttiva: avvio dell'app in locale](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di Configurazione app creata nell'argomento di avvio rapido.

1. Selezionare **Gestione funzionalità** e modificare lo stato della chiave **Beta** in **Sì**:

    | Chiave | Stato |
    |---|---|
    | Beta | Attivato |

1. Per riavviare l'applicazione, tornare al prompt dei comandi e premere `Ctrl-C` per annullare il processo `dotnet` in esecuzione e quindi eseguire di nuovo `dotnet run`.

1. Aggiornare la pagina del browser per visualizzare le nuove impostazioni di configurazione.

    ![Guida introduttiva: avvio dell'app in locale](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un nuovo archivio di Configurazione app, che è stato usato per gestire le funzionalità in un'app Web ASP.NET Core con le [librerie di gestione delle funzionalità](https://go.microsoft.com/fwlink/?linkid=2074664).

- Altre informazioni sulla [gestione delle funzionalità](./concept-feature-management.md).
- [Gestire i flag di funzionalità](./manage-feature-flags.md).
- [Usare i flag di funzionalità in un'app ASP.NET Core](./use-feature-flags-dotnet-core.md).
- [Usare la configurazione dinamica in un'app ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)
