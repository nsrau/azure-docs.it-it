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
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 44ae922b182874eef378d4868fb278c3c76252db
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884414"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Esercitazione: Usare la configurazione dinamica in un'app ASP.NET Core

ASP.NET Core ha un sistema di configurazione modulare che è in grado di leggere i dati di configurazione da una varietà di origini e di gestire immediatamente le modifiche senza causare il riavvio di un'applicazione. Supporta l'associazione delle impostazioni di configurazione alle classi .NET fortemente tipizzate inserendole nel codice usando i vari criteri `IOptions<T>`. Uno di questi criteri, in particolare `IOptionsSnapshot<T>`, fornisce il ricaricamento automatico della configurazione dell'applicazione quando cambiano i dati sottostanti. È possibile inserire `IOptionsSnapshot<T>` nei controller dell'applicazione per accedere alla configurazione più recente archiviata in Configurazione app di Azure. Inoltre, è possibile configurare la libreria client ASP.NET Core di Configurazione app per monitorare e recuperare continuamente qualsiasi modifica in un archivio di configurazione app tramite il polling a intervalli periodici definiti dall'utente.

Questa esercitazione mostra come è possibile implementare aggiornamenti dinamici della configurazione nel codice. Si basa sull'app Web presentata nelle guide introduttive. Prima di continuare, completare le procedure descritte in [Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md).

Per completare i passaggi descritti in questa guida di avvio rapido è possibile usare qualsiasi editor di codice. Tuttavia, [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare l'applicazione per aggiornarne la configurazione in risposta alle modifiche in un archivio di configurazione app
> * Inserire la configurazione più recente nei controllori dell'applicazione

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, installare [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Ricaricare i dati di Configurazione app

1. Aprire *Program.cs* e aggiornare il metodo `CreateWebHostBuilder` aggiungendo il metodo `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.Connect(settings["ConnectionStrings:AppConfig"])
                    .Watch("TestApp:Settings:BackgroundColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:FontColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:Message", TimeSpan.FromSeconds(1)));
            })
            .UseStartup<Startup>();
    ```

    Il secondo parametro del metodo `.Watch` è l'intervallo di polling con cui la libreria client ASP.NET esegue una query su un archivio di configurazione app per verificare se ci sono state delle modifiche alle impostazioni di configurazione specifiche.

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

1. Aprire *HomeController.cs* nella directory *Controllers*, aggiornare la classe `HomeController` per ricevere `Settings` tramite l'inserimento delle dipendenze e usare i relativi valori.

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

2. Aprire il file *Index.cshtml* nella directory *Views* > *Home* e sostituire il relativo contenuto con il codice seguente:

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

1. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire il comando seguente nella shell dei comandi:

        dotnet build

2. Dopo che la compilazione è stata completata correttamente, eseguire il comando seguente per avviare l'app Web in locale:

        dotnet run

3. Avviare una finestra del browser e passare a `http://localhost:5000`, che è l'URL predefinito per l'app Web ospitata in locale.

    ![Avvio rapido: avvio dell'app in locale](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Accedere al [portale di Azure](https://aka.ms/azconfig/portal), fare clic su **Tutte le risorse** e sull'istanza dell'archivio di configurazione app creata nella guida introduttiva.

5. Fare clic su **Esplora chiave-valore** e aggiornare i valori delle chiavi seguenti:

    | Chiave | Valore |
    |---|---|
    | TestAppSettings:BackgroundColor | blue |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Dati di Configurazione app di Azure - ora con aggiornamenti dinamici |

6. Aggiornare la pagina del browser per visualizzare le nuove impostazioni di configurazione.

    ![Avvio rapido: aggiornamento dell'app in locale](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aggiunta un'identità del servizio gestita di Azure per facilitare l'accesso a Configurazione app e migliorare la gestione delle credenziali dell'app. Per altre informazioni sull'uso di Configurazione app, continuare con gli esempi dell'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Esempi dell'interfaccia della riga di comando](./cli-samples.md)
