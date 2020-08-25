---
title: 'Esercitazione: Usare la configurazione dinamica di Configurazione app in ASP.NET Core'
titleSuffix: Azure App Configuration
description: In questa esercitazione viene illustrato come aggiornare dinamicamente i dati di configurazione per le app ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 217c564a6bdb340ec15262c1eaf54a75bbffc833
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585016"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Esercitazione: Usare la configurazione dinamica in un'app ASP.NET Core

ASP.NET Core ha un sistema di configurazione modulare che può leggere dati di configurazione da diverse origini, nonché gestire le modifiche in modo dinamico senza causare il riavvio di un'applicazione. ASP.NET Core supporta l'associazione delle impostazioni di configurazione a classi .NET fortemente tipizzate. Vengono inserite nel codice usando i vari modelli `IOptions<T>`. Uno di questi modelli, `IOptionsSnapshot<T>`, ricarica automaticamente la configurazione dell'applicazione in caso di modifica dei dati sottostanti. È possibile inserire `IOptionsSnapshot<T>` nei controller dell'applicazione per accedere alla configurazione più recente archiviata in Configurazione app di Azure.

È anche possibile configurare la libreria client ASP.NET Core di Configurazione app per aggiornare dinamicamente una serie di impostazioni di configurazione tramite middleware. Finché l'app Web continua a ricevere richieste, le impostazioni di configurazione continueranno a essere aggiornate con l'archivio di configurazione.

Configurazione app memorizza nella cache ogni impostazione per evitare un numero eccessivo di chiamate all'archivio di configurazione. L'operazione di aggiornamento resta in attesa fino a quando il valore memorizzato nella cache di un'impostazione non scade per aggiornare tale impostazione, anche quando il relativo valore viene modificato nell'archivio di configurazione. La scadenza predefinita della cache è 30 secondi. Se necessario, è possibile eseguire l'override della scadenza.

Questa esercitazione mostra come è possibile implementare aggiornamenti dinamici della configurazione nel codice. Si basa sull'app Web presentata nelle guide introduttive. Prima di continuare, completare le procedure descritte in [Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md).

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. [Visual Studio Code](https://code.visualstudio.com/) è un'ottima opzione ed è disponibile per le piattaforme Windows, macOS e Linux.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare l'applicazione per aggiornarne la configurazione in risposta alle modifiche in un archivio di Configurazione app.
> * Inserire la configurazione più recente nei controller dell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, installare [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Prima di continuare, completare le procedure descritte in [Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md).

## <a name="add-a-sentinel-key"></a>Aggiungere una chiave Sentinel

Una *chiave Sentinel* è una chiave speciale usata per segnalare quando la configurazione è stata modificata. L'app monitora la chiave Sentinel per rilevare le modifiche. Quando viene rilevata una modifica, si aggiornano tutti i valori di configurazione. Questo approccio riduce il numero complessivo di richieste effettuate dall'app a Configurazione app, evitando così la necessità di monitorare tutte le chiavi per il rilevamento di eventuali modifiche.

1. Nel portale di Azure selezionare **Esplora configurazioni > Crea > Coppia chiave-valore**.

1. In **Chiave** immettere *TestApp:Settings:Sentinel*. In **Valore** immettere 1. Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto**.

1. Selezionare **Applica**.

    > [!NOTE]
    > Se non si usa una chiave Sentinel, è necessario registrare manualmente ogni chiave da controllare.

## <a name="reload-data-from-app-configuration"></a>Ricaricare i dati di Configurazione app

1. Aggiungere un riferimento al pacchetto NuGet `Microsoft.Azure.AppConfiguration.AspNetCore` eseguendo il comando seguente:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Aprire *Program.cs* e aggiornare il metodo `CreateWebHostBuilder` per aggiungere il metodo `config.AddAzureAppConfiguration()`.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
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
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    Il metodo `ConfigureRefresh` consente di specificare le impostazioni usate per aggiornare i dati di configurazione con l'archivio di Configurazione app quando viene attivata un'operazione di aggiornamento. Il parametro `refreshAll` nel metodo `Register` indica che tutti i valori di configurazione devono essere aggiornati se la chiave Sentinel viene modificata.

    Inoltre, il metodo `SetCacheExpiration` sostituisce la scadenza della cache predefinita di 30 secondi, specificando invece un intervallo di 5 minuti. In questo modo si riduce il numero di richieste inviate a Configurazione app.

    > [!NOTE]
    > A scopo di test, è consigliabile ridurre la scadenza della cache.

    Per attivare effettivamente un'operazione di aggiornamento, è necessario configurare un middleware di aggiornamento per l'applicazione, per aggiornare i dati di configurazione quando si verifica una modifica. Si vedrà come eseguire questa operazione in un passaggio successivo.

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

3. Aprire *Startup.cs* e usare `IServiceCollection.Configure<T>` nel metodo `ConfigureServices` per associare i dati di configurazione alla classe `Settings`.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

    > [!TIP]
    > Per altre informazioni sul modello di opzioni durante la lettura dei valori di configurazione, vedere  [Modelli di opzioni in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1).

4. Aggiornare il metodo `Configure` aggiungendo il middleware `UseAzureAppConfiguration` per consentire l'aggiornamento delle impostazioni di configurazione appositamente registrate mentre l'app Web ASP.NET Core continua a ricevere richieste.


    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    Il middleware usa la configurazione di aggiornamento specificata nel metodo `AddAzureAppConfiguration` in `Program.cs` per attivare un aggiornamento per ogni richiesta ricevuta dall'app Web ASP.NET Core. Per ogni richiesta, viene attivata un'operazione di aggiornamento e la libreria client verifica se il valore memorizzato nella cache per l'impostazione di configurazione registrata è scaduto. Se è scaduto, viene aggiornato.

## <a name="use-the-latest-configuration-data"></a>Usare i dati di configurazione più recenti

1. Aprire *HomeController.cs* nella directory Controllers e aggiungere un riferimento al pacchetto `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Aggiornare la classe `HomeController` per ricevere `Settings` tramite l'inserimento delle dipendenze e usare i relativi valori.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
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

        // ...
    }
    ```
    ---



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
            font-size: @ViewData["FontSize"]px;
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

```console
        dotnet build
```

1. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

```console
        dotnet run
```

1. Aprire una finestra del browser e passare all'URL visualizzato nell'output di `dotnet run`.

    ![Avvio dell'app di avvio rapido in locale](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di Configurazione app creata nell'argomento di avvio rapido.

1. Selezionare **Configuration Explorer** e aggiornare i valori delle chiavi seguenti:

    | Chiave | Valore |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Dati di Configurazione app di Azure - ora con aggiornamenti dinamici |
    | TestApp:Settings:Sentinel | 2 |

1. Aggiornare la pagina del browser per visualizzare le nuove impostazioni di configurazione. Potrebbe essere necessario aggiornare più volte per riflettere le modifiche.

    ![Avvio dell'app di avvio rapido aggiornata in locale](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata l'app Web ASP.NET Core per aggiornare in modo dinamico le impostazioni di configurazione di Configurazione app. Per informazioni su come usare un'identità gestita di Azure per semplificare l'accesso a Configurazione app, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Integrazione dell'identità gestita](./howto-integrate-azure-managed-service-identity.md)
