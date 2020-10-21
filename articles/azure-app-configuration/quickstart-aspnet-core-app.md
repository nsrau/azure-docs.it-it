---
title: Guida di avvio rapido di Configurazione app di Azure con ASP.NET Core | Microsoft Docs
description: Creare un'app ASP.NET Core con Configurazione app di Azure per centralizzare l'archiviazione e la gestione delle impostazioni di un'applicazione ASP.NET Core.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperfq1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: lcozzens
ms.openlocfilehash: 13283a9531804502b8a8d72e615be955b413658c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075842"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Guida di avvio rapido: Creare un'app ASP.NET Core con Configurazione app di Azure

In questa guida di avvio rapido si userà Configurazione app di Azure per centralizzare l'archiviazione e la gestione delle impostazioni di un'app ASP.NET Core. ASP.NET Core crea un singolo oggetto configurazione basato su chiave-valore usando le impostazioni di una o più origini dati specificate da un'app. Queste origini dati sono note come *provider di configurazione*. Dato che il client .NET Core di Configurazione app viene implementato come provider di configurazione, il servizio risulta come un'altra origine dati.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/dotnet)
* [ASP.NET Core SDK](https://dotnet.microsoft.com/download)

> [!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire le istruzioni della riga di comando riportate in questo articolo. Include strumenti comuni di Azure preinstallati, tra cui .NET Core SDK. Se è stato eseguito l'accesso alla sottoscrizione di Azure, avviare [Azure Cloud Shell](https://shell.azure.com) da shell.azure.com. Per altre informazioni su Azure Cloud Shell, [leggere la documentazione](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. Selezionare **Operazioni** > **Esplora configurazioni** > **Crea** > **Coppia chiave-valore** per aggiungere le coppie chiave-valore seguenti:

    | Chiave                                | Valore                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Dati di Configurazione app di Azure* |

    Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto** per il momento. Selezionare **Applica**.

## <a name="create-an-aspnet-core-web-app"></a>Creare un'app Web ASP.NET Core

Usare l'[interfaccia della riga di comando di .NET Core](/dotnet/core/tools) per creare un nuovo progetto MVC ASP.NET Core. [Azure Cloud Shell](https://shell.azure.com) offre questi strumenti, disponibili anche nelle piattaforme Windows, macOS e Linux.

Eseguire il comando seguente per creare un progetto MVC ASP.NET Core in una nuova cartella *TestAppConfig*:

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>Connettersi all'archivio di Configurazione app

1. Eseguire il comando seguente per aggiungere un riferimento al pacchetto NuGet [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore):

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Eseguire il comando seguente nella stessa directory del file con estensione *csproj*. Il comando usa Secret Manager per archiviare un segreto denominato `ConnectionStrings:AppConfig`, che archivia la stringa di connessione per l'archivio di Configurazione app. Sostituire il segnaposto `<your_connection_string>` con la stringa di connessione dell'archivio di Configurazione app. È possibile trovare la stringa di connessione in **Chiavi di accesso** nel portale di Azure.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > Alcune shell troncheranno la stringa di connessione se non è racchiusa tra virgolette. Verificare che l'output del comando `dotnet user-secrets` mostri l'intera stringa di connessione. In caso contrario, eseguire di nuovo il comando racchiudendo la stringa di connessione tra virgolette.

    Secret Manager viene usato solo per testare l'app Web in locale. Quando l'app viene distribuita in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/web), usare l'impostazione **Stringhe di connessione** dell'applicazione nel servizio app invece di Secret Manager per archiviare la stringa di connessione.

    Accedere a questo segreto usando l'API di configurazione di .NET Core. Con tale API è possibile usare i due punti (`:`) nel nome della configurazione in tutte le piattaforme supportate. Per altre informazioni, vedere [Chiavi e valori di configurazione](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. In *Program.cs* aggiungere un riferimento allo spazio dei nomi dell'API di configurazione di .NET Core:

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. Aggiornare il metodo `CreateWebHostBuilder` per usare Configurazione app effettuando una chiamata al metodo `AddAzureAppConfiguration`.

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
                    config.AddAzureAppConfiguration(connection);
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
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    Con la modifica precedente, il [provider di configurazione per Configurazione app](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) è stato registrato con l'API di configurazione di .NET Core.

## <a name="read-from-the-app-configuration-store"></a>Leggere valori dall'archivio di Configurazione app

Completare i passaggi seguenti per leggere e visualizzare i valori archiviati nell'archivio di Configurazione app. Per accedere all'archivio verrà usata l'API di configurazione di .NET Core. Per visualizzare i valori delle chiavi verrà usata la sintassi Razor.

Aprire *\<app root>/Views/Home/Index.cshtml* e sostituire il contenuto con il codice seguente:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<style>
    body {
        background-color: @Configuration["TestApp:Settings:BackgroundColor"]
    }
    h1 {
        color: @Configuration["TestApp:Settings:FontColor"];
        font-size: @Configuration["TestApp:Settings:FontSize"]px;
    }
</style>

<h1>@Configuration["TestApp:Settings:Message"]</h1>
```

Nel codice precedente le chiavi dell'archivio di Configurazione app vengono usate come segue:

* Il valore della chiave `TestApp:Settings:BackgroundColor` viene assegnato alla proprietà CSS `background-color`.
* Il valore della chiave `TestApp:Settings:FontColor` viene assegnato alla proprietà CSS `color`.
* Il valore della chiave `TestApp:Settings:FontSize` viene assegnato alla proprietà CSS `font-size`.
* Il valore della chiave `TestApp:Settings:Message` viene visualizzato come intestazione.

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Per compilare l'app usando il interfaccia della riga di comando di .NET Core, passare alla directory radice del progetto. Eseguire il comando seguente nella shell dei comandi:

    ```dotnetcli
    dotnet build
    ```

1. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

    ```dotnetcli
    dotnet run
    ```

1. Se si sta lavorando sul computer locale, usare un browser per passare a `http://localhost:5000`, che è l'URL predefinito per l'app Web ospitata in locale. Se si sta lavorando in Azure Cloud Shell, selezionare il pulsante **Anteprima Web** e quindi **Configura**.

    ![Individuare il pulsante Anteprima Web](./media/quickstarts/cloud-shell-web-preview.png)

    Quando viene chiesto di configurare la porta per l'anteprima, immettere *5000* e selezionare **Apri ed esplora**. Nella pagina verrà visualizzato il messaggio "Dati di Configurazione app di Azure".

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido:

* È stato effettuato il provisioning di un nuovo archivio di Configurazione app.
* È stato registrato il provider di configurazione .NET Core dell'archivio di Configurazione app.
* Sono state lette le chiavi dell'archivio di Configurazione app con il provider di configurazione.
* Sono stati visualizzati i valori delle chiavi dell'archivio di Configurazione app con la sintassi Razor.

Per informazioni su come configurare l'app ASP.NET Core per aggiornare in modo dinamico le impostazioni di configurazione, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Abilitare la configurazione dinamica](./enable-dynamic-configuration-aspnet-core.md)