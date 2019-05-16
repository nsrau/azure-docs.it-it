---
title: Esercitazione per l'integrazione con una pipeline di integrazione e recapito continui tramite Configurazione app di Azure | Microsoft Docs
description: Questa esercitazione descrive come generare un file di configurazione usando i dati nella Configurazione app di Azure durante l'integrazione e il recapito continui
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 26bd49af7245d6e6dde3162a2e1d95c54f13e35b
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415923"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrare una pipeline CI/CD

Questo articolo descrive vari modi d'uso dei dati di Configurazione app di Azure in un sistema di integrazione continua e distribuzione continua.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Usare Configurazione app nella pipeline di Azure DevOps

Se è presente una pipeline di Azure DevOps, è possibile recuperare le coppie chiave-valore da Configurazione app e impostarle come variabili di attività. L'[estensione Configurazione app di DevOps](https://go.microsoft.com/fwlink/?linkid=2091063) è un modulo aggiuntivo che fornisce questa funzionalità. È sufficiente seguire le istruzioni per usare l'estensione in una compilazione o sequenza di attività di rilascio.

## <a name="deploy-app-configuration-data-with-your-application"></a>Distribuire i dati di Configurazione app con l'applicazione

L'applicazione potrebbe non essere eseguita se dipende da Configurazione app di Azure e non riesce ad accedervi. È possibile migliorare la resilienza dell'applicazione per gestire un evento di questo tipo, anche se è improbabile che si verifichi. A tale scopo, comprimere i dati di configurazione correnti in un file che viene distribuito con l'applicazione e caricato localmente all'avvio. Questo approccio garantisce che l'applicazione abbia almeno i valori predefiniti delle impostazioni. Questi valori vengono sovrascritti da tutte le modifiche più recenti in un archivio di configurazione app quando è disponibile.

Usando la funzione [Esporta](./howto-import-export-data.md#export-data) di Configurazione app di Azure è possibile automatizzare il processo di recupero dei dati della configurazione corrente come un singolo file. Incorporare quindi questo file in un passaggio di compilazione o di distribuzione nella pipeline di integrazione e di distribuzione continua (CI/CD).

L'esempio seguente illustra come includere i dati della Configurazione app di Azure come un passaggio di compilazione per l'app Web introdotta nei modelli di avvio rapido. Prima di continuare, completare le procedure descritte in [Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md).

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

### <a name="prerequisites"></a>Prerequisiti

Se la compilazione avviene in locale, scaricare e installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se non lo si è già fatto.

Per eseguire una compilazione cloud, ad esempio con Azure DevOps, assicurarsi che nel sistema di compilazione sia installata l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="export-an-app-configuration-store"></a>Esportare un archivio di configurazione app

1. Aprire il file *.csproj* e aggiungere il seguente script:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Aggiungere *ConnectionString* associata all'archivio di configurazione app come una variabile di ambiente.

2. Aprire il file *Program.cs* e aggiornare il metodo `CreateWebHostBuilder` per usare il file JSON esportato effettuando una chiamata al metodo `config.AddJsonFile()`.

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

### <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Impostare una variabile di ambiente denominata **ConnectionString** sulla chiave di accesso all'archivio di configurazione app. Se si usa il prompt dei comandi di Windows, eseguire il comando seguente e riavviare il prompt per rendere effettiva la modifica:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se si usa Windows PowerShell, eseguire il comando seguente:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se si usa macOS o Linux, eseguire il comando seguente:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire questo comando nella shell dei comandi:

        dotnet build

3. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

        dotnet run

4. Aprire una finestra del browser e passare a `http://localhost:5000`, che è l'URL predefinito per l'app Web ospitata in locale.

    ![Guida introduttiva: avvio dell'app in locale](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, sono stati esportati i dati della Configurazione app di Azure da usare in una pipeline di distribuzione. Per altre informazioni sull'uso di Configurazione app, passare agli esempi dell'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Integrazione dell'identità gestita](./howto-integrate-azure-managed-service-identity.md)
