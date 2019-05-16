---
title: Esercitazione per l'uso della configurazione dinamica di Configurazione app di Azure in un'app ASP.NET Core | Microsoft Docs
description: In questa esercitazione viene illustrato come aggiornare dinamicamente i dati di configurazione per le app ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: cae29fe045d1bdc17f414ff016642635b74320df
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408822"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Esercitazione: Usare la configurazione dinamica in un'app ASP.NET Core

ASP.NET Core ha un sistema di configurazione modulare che può leggere dati di configurazione da diverse origini, nonché gestire immediatamente le modifiche senza causare il riavvio di un'applicazione. ASP.NET Core supporta l'associazione delle impostazioni di configurazione a classi .NET fortemente tipizzate. Vengono inserite nel codice usando i vari modelli `IOptions<T>`. Uno di questi modelli, `IOptionsSnapshot<T>`, ricarica automaticamente la configurazione dell'applicazione in caso di modifica dei dati sottostanti.

È possibile inserire `IOptionsSnapshot<T>` nei controller dell'applicazione per accedere alla configurazione più recente archiviata in Configurazione app di Azure. È anche possibile configurare la libreria client ASP.NET Core di Configurazione app per monitorare e recuperare continuamente qualsiasi modifica in un archivio di configurazione app, definendo l'intervallo periodico di polling.

Questa esercitazione mostra come è possibile implementare aggiornamenti dinamici della configurazione nel codice. Si basa sull'app Web presentata nelle guide introduttive. Prima di continuare, completare le procedure descritte in [Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md).

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. [Visual Studio Code](https://code.visualstudio.com/) è un'ottima opzione ed è disponibile per le piattaforme Windows, macOS e Linux.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare l'applicazione per aggiornarne la configurazione in risposta alle modifiche in un archivio di configurazione app.
> * Inserire la configurazione più recente nei controller dell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, installare [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Ricaricare i dati di Configurazione app

1. Aprire *Program.cs* e aggiornare il metodo `CreateWebHostBuilder` aggiungendo il metodo `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .Watch("TestApp:Settings:BackgroundColor")
                           .Watch("TestApp:Settings:FontColor")
                           .Watch("TestApp:Settings:Message"));
            })
            .UseStartup<Startup>();
    ```

    Il secondo parametro del metodo `.Watch` è l'intervallo di polling con cui la libreria client ASP.NET esegue query su un archivio di configurazione app. La libreria client controlla la specifica impostazione di configurazione per verificare se sono state apportate modifiche.

2. Aggiungere un file *Settings.cs* che definisce e implementa una nuova classe `Settings`.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Aprire *Startup.cs* e aggiornare il metodo `ConfigureServices` per associare i dati di configurazione alla classe `Settings`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

## <a name="use-the-latest-configuration-data"></a>Usare i dati di configurazione più recenti

1. Aprire *HomeController.cs* nella directory Controllers e aggiungere un riferimento al pacchetto `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Aggiornare la classe `HomeController` per ricevere `Settings` tramite l'inserimento delle dipendenze e usare i relativi valori.

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

3. Aprire *Index.cshtml* nella directory Views > Home e sostituirne il contenuto con lo script seguente:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire questo comando nella shell dei comandi:

        dotnet build

2. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

        dotnet run

3. Aprire una finestra del browser e passare a `http://localhost:5000`, che è l'URL predefinito per l'app Web ospitata in locale.

    ![Guida introduttiva: avvio dell'app in locale](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Accedere al [portale di Azure](https://aka.ms/azconfig/portal). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di configurazione app creata nella guida di avvio rapido.

5. Selezionare **Configuration Explorer** e aggiornare i valori delle chiavi seguenti:

    | Chiave | Valore |
    |---|---|
    | TestAppSettings:BackgroundColor | green |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Dati di Configurazione app di Azure - ora con aggiornamenti dinamici |

6. Aggiornare la pagina del browser per visualizzare le nuove impostazioni di configurazione.

    ![Avvio rapido: aggiornamento dell'app in locale](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aggiunta un'identità del servizio gestita di Azure per facilitare l'accesso a Configurazione app e migliorare la gestione delle credenziali dell'app. Per altre informazioni sull'uso di Configurazione app, passare agli esempi dell'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Esempi dell'interfaccia della riga di comando](./cli-samples.md)
