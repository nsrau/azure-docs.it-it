---
title: Guida introduttiva di Configurazione app di Azure con ASP.NET Core | Microsoft Docs
description: Guida introduttiva per l'uso di Configurazione app di Azure con le app ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 10/11/2019
ms.author: yegu
ms.openlocfilehash: 4e08192788329e7a835ddb0b6b3f1aa01b2c73e1
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299946"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Guida introduttiva: Creare un'app ASP.NET Core con Configurazione app di Azure

In questa guida di avvio rapido si incorpora Configurazione app di Azure in un'app Web ASP.NET Core per centralizzare l'archiviazione e la gestione delle impostazioni dell'applicazione separatamente dal codice. ASP.NET Core crea un singolo oggetto configurazione basato su una coppia chiave-valore usando le impostazioni di una o più origini dati specificate da un'applicazione. Queste origini dati sono note come *provider di configurazione*. Poiché il client .NET Core di Configurazione app viene implementato come tale provider, il servizio sembra simile a un'altra origine dati.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [ASP.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Creare un archivio di configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selezionare **Configuration Explorer** >  **+ Crea** per aggiungere le coppie chiave-valore seguenti:

    | Chiave | Valore |
    |---|---|
    | TestApp:Settings:BackgroundColor | Bianco |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Nero |
    | TestApp:Settings:Message | Dati di Configurazione app di Azure |

    Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto** per il momento.

## <a name="create-an-aspnet-core-web-app"></a>Creare un'app Web ASP.NET Core

È possibile usare l'[interfaccia della riga di comando di .NET Core](https://docs.microsoft.com/dotnet/core/tools/) per creare un nuovo progetto di app Web MVC ASP.NET Core. Il vantaggio di usare l'interfaccia della riga di comando di .NET Core rispetto a Visual Studio è che è disponibile nelle piattaforme Windows, macOS e Linux.

1. Creare una nuova cartella per il progetto. Per questa guida di avvio rapido assegnarle il nome *TestAppConfig*.

2. Nella nuova cartella eseguire il comando seguente per creare un nuovo progetto di app Web ASP.NET Core MVC:

    ```CLI
        dotnet new mvc --no-https
    ```

## <a name="add-secret-manager"></a>Aggiungere Secret Manager

Per usare Secret Manager, aggiungere un elemento `UserSecretsId` al file con estensione *csproj*.

- Aprire il file con estensione *csproj*. Aggiungere un elemento `UserSecretsId` come illustrato di seguito. È possibile usare lo stesso GUID oppure sostituire questo valore con uno proprio. Salvare il file.

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

Lo strumento Secret Manager archivia i dati sensibili per operazioni di sviluppo al di fuori dell'albero del progetto. Questo approccio contribuisce a impedire la condivisione accidentale dei segreti dell'app all'interno del codice sorgente. Per altre informazioni su Secret Manager, vedere [Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets).

## <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di configurazione app

1. Aggiungere un riferimento al pacchetto NuGet `Microsoft.Azure.AppConfiguration.AspNetCore` eseguendo il comando seguente:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```
2. Eseguire il comando seguente per ripristinare i pacchetti per il progetto:

    ```CLI
        dotnet restore
    ```
3. Aggiungere un segreto denominato *ConnectionStrings:AppConfig* a Secret Manager.

    Questo segreto contiene la stringa di connessione per accedere all'archivio di configurazione app. Sostituire il valore nel comando seguente con la stringa di connessione per l'archivio di configurazione app.

    Questo comando deve essere eseguito nella stessa directory del file con estensione *csproj*.

    ```CLI
        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Alcune shell troncheranno la stringa di connessione se non è racchiusa tra virgolette. Verificare che l'output del comando `dotnet user-secrets` mostri l'intera stringa di connessione. In caso contrario, eseguire di nuovo il comando racchiudendo la stringa di connessione tra virgolette.

    Secret Manager viene usato solo per testare l'app Web in locale. Quando l'app viene distribuita in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/web), ad esempio, viene usata un'impostazione applicazione **Stringhe di connessione** nel servizio app, invece di Secret Manager per archiviare la stringa di connessione.

    È possibile accedere al segreto con l'API di configurazione. Con l'API di configurazione è possibile usare i due punti (:) nel nome di configurazione in tutte le piattaforme supportate. Vedere la [configurazione in base all'ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Aprire *Program.cs* e aggiungere un riferimento al provider di Configurazione app .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Aggiornare il metodo `CreateWebHostBuilder` per usare Configurazione app effettuando una chiamata al metodo `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` sostituisce `CreateWebHostBuilder` in .NET Core 3.0.  Selezionare la sintassi corretta in base all'ambiente.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>Aggiornare `CreateWebHostBuilder` per .NET Core 2.x

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
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
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

6. Aprire *Index.cshtml* nella directory Views > Home e sostituirne il contenuto con il codice seguente:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

7. Aprire il file *_Layout.cshtml* nella directory Views > Shared e sostituire il relativo contenuto con il codice seguente:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire questo comando nella shell dei comandi:

    ```CLI
       dotnet build
    ```

2. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

    ```CLI
        dotnet run
    ```

3. Aprire una finestra del browser e passare a `http://localhost:5000`, che è l'URL predefinito per l'app Web ospitata in locale.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un nuovo archivio di configurazione app, che è stato usato con un'app Web ASP.NET Core con il [provider di Configurazione app](https://go.microsoft.com/fwlink/?linkid=2074664). Per altre informazioni su come usare Configurazione app, passare all'esercitazione successiva che illustra come configurare l'app Web per l'aggiornamento dinamico delle impostazioni di configurazione.

> [!div class="nextstepaction"]
> [Usare la configurazione dinamica in un'app ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)
