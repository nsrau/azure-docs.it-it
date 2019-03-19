---
title: Integrazione con una pipeline di integrazione e distribuzione continua tramite configurazione delle App di Azure | Microsoft Docs
description: Informazioni su come generare un file di configurazione utilizzando i dati nella configurazione di App di Azure durante l'integrazione e recapito continui
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957369"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrare una pipeline CI/CD

Per migliorare la resilienza dell'applicazione contro la possibilità di non essere in grado di raggiungere configurazione delle App di Azure remota, è necessario creare un pacchetto i dati di configurazione corrente in un file che viene distribuito con l'applicazione e in locale caricato all'avvio . Questo approccio garantisce che l'applicazione avrà almeno i valori delle impostazioni predefinite. Questi valori verranno sovrascritti da tutte le modifiche più recenti in un archivio di configurazione delle app quando è disponibile.

Usando il [ **esportare** ](./howto-import-export-data.md#export-data) funzione di configurazione di App di Azure, è possibile automatizzare il processo di recupero corrente dei dati di configurazione come singolo file. È quindi possibile incorporare questo file in un passaggio di compilazione o distribuzione di continuo pipeline di integrazione e distribuzione continua.

Nell'esempio seguente viene illustrato come includere la configurazione dell'App i dati come una compilazione passaggio per l'app web, introdotto in Guide introduttive. Prima di continuare, completare le procedure descritte in [Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md).

Per completare i passaggi descritti in questa guida di avvio rapido è possibile usare qualsiasi editor di codice. Tuttavia, [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

## <a name="prerequisites"></a>Prerequisiti

Se si compila in locale, scaricare e installare [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se hai già fatto.

Se si desidera eseguire la compilazione cloud, con la metodologia DevOps di Azure, ad esempio, assicurarsi che [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) è installato nel sistema di compilazione.

## <a name="export-app-configuration-store"></a>Esportare un archivio di configurazione app

1. Aprire il *file con estensione csproj* file e aggiungere quanto segue:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Il *ConnectionString* associati con la configurazione dell'app store deve essere aggiunti come una variabile di ambiente.

2. Aprire *Program.cs* e aggiornare le `CreateWebHostBuilder` metodo da usare il file json esportati chiamando il `config.AddJsonFile()` (metodo).

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Impostare una variabile di ambiente denominata **ConnectionString** sulla chiave di accesso all'archivio di configurazione app. Se si usa il prompt dei comandi di Windows, eseguire il comando seguente e riavviare il prompt per rendere effettiva la modifica:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se si usa Windows PowerShell, eseguire il comando seguente:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se si usa macOS o Linux, eseguire il comando seguente:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire il comando seguente nella shell dei comandi:

        dotnet build

3. Dopo che la compilazione è stata completata correttamente, eseguire il comando seguente per avviare l'app Web in locale:

        dotnet run

4. Avviare una finestra del browser e passare a `http://localhost:5000`, ossia l'URL predefinito per l'app Web ospitata in locale.

    ![Guida introduttiva: avvio dell'app in locale](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Passaggi successivi

* [Identità gestite per l'integrazione di risorse di Azure](./integrate-azure-managed-service-identity.md)
