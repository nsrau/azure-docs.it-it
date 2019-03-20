---
title: Integrare con una pipeline di integrazione e recapito continuata tramite configurazione delle App di Azure | Microsoft Docs
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
ms.openlocfilehash: cb9fe6dc234c317daa5eabec01812324e7c81663
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224329"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrare una pipeline CI/CD

È possibile migliorare la resilienza dell'applicazione contro la possibilità di non essere in grado di raggiungere configurazione delle App di Azure remota. A tale scopo, comprimere i dati di configurazione corrente in un file che viene distribuito con l'applicazione e caricati in locale durante l'avvio. Questo approccio garantisce che l'applicazione abbia almeno i valori delle impostazioni predefinite. Questi valori vengono sovrascritti da tutte le modifiche più recenti in un archivio di configurazione delle app quando è disponibile.

Usando il [esportare](./howto-import-export-data.md#export-data) funzione di configurazione di App di Azure, è possibile automatizzare il processo di recupero corrente dei dati di configurazione come singolo file. Quindi incorporare questo file in un passaggio di compilazione o distribuzione di continuo pipeline di integrazione e distribuzione continua (CI/CD).

Nell'esempio seguente viene illustrato come includere la configurazione dell'App i dati come una compilazione passaggio per l'app web, introdotto in Guide introduttive. Prima di continuare, completare [creare un'app ASP.NET Core con configurazione delle App](./quickstart-aspnet-core-app.md) prima.

È possibile usare qualsiasi editor di codice per eseguire i passaggi in questa Guida introduttiva. [Visual Studio Code](https://code.visualstudio.com/) è disponibile un'ottima scelta in di Windows, macOS e le piattaforme Linux.

## <a name="prerequisites"></a>Prerequisiti

Se si compila in locale, scaricare e installare il [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se hai già fatto.

Per eseguire la compilazione cloud, con la metodologia DevOps di Azure, ad esempio, assicurarsi che il [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) è installato nel sistema di compilazione.

## <a name="export-an-app-configuration-store"></a>Esportare un archivio di configurazione app

1. Aprire il *file con estensione csproj* file, quindi aggiungere lo script seguente:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Aggiungere il *ConnectionString* associata con l'archivio di configurazione di app come una variabile di ambiente.

2. Aprire Program.cs e aggiornare il `CreateWebHostBuilder` metodo da usare il file JSON esportato chiamando il `config.AddJsonFile()` (metodo).

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

1. Impostare una variabile di ambiente denominata **ConnectionString**e impostarlo per la chiave di accesso all'archivio di configurazione app. Se si usa il prompt dei comandi di Windows, eseguire il comando seguente e riavviare il prompt dei comandi per consentire le modifiche abbiano effetto:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se si usa Windows PowerShell, eseguire il comando seguente:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se si usa macOS o Linux, eseguire il comando seguente:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Per compilare l'app tramite la CLI di .NET Core, eseguire il comando seguente nella shell dei comandi:

        dotnet build

3. Dopo la compilazione viene completata correttamente, eseguire il comando seguente per eseguire localmente l'app web:

        dotnet run

4. Aprire una finestra del browser e passare a `http://localhost:5000`, ovvero l'URL predefinito per l'app web ospitata in locale.

    ![Guida introduttiva: avvio dell'app in locale](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Passaggi successivi

* [Identità gestite per l'integrazione con le risorse di Azure](./integrate-azure-managed-service-identity.md)
