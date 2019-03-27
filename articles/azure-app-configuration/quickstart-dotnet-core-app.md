---
title: Avvio rapido di Configurazione app di Azure con .NET Core | Microsoft Docs
description: Avvio rapido per l'uso di Configurazione app di Azure con le app .NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 5501e92b9a9d977f74bf4ed028b3cd3de4e56133
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225383"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Avvio rapido: Creare un'app .NET Core con Configurazione app

Configurazione app di Azure è un servizio di configurazione gestito di Azure. È possibile usarla per archiviare e gestire con facilità tutte le impostazioni delle applicazioni in un'unica risorsa separata dal codice. Questa guida di avvio rapido mostra come incorporare il servizio in un'app console .NET Core.

Per completare i passaggi riportati in questo argomento di avvio rapido, è possibile usare qualsiasi editor di codice. [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido, installare [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Creare un archivio di configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>Creare un'app console .NET Core

In questa sezione viene usata l'[interfaccia della riga di comando di .NET Core](https://docs.microsoft.com/dotnet/core/tools/) per creare un nuovo progetto di app console .NET Core. Il vantaggio di usare l'interfaccia della riga di comando di .NET Core rispetto a Visual Studio è che è disponibile nelle piattaforme Windows, macOS e Linux.

1. Creare una nuova cartella per il progetto.

2. Nella nuova cartella eseguire il comando seguente per creare un nuovo progetto di app Web ASP.NET Core MVC:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di configurazione app

1. Aggiungere un riferimento al pacchetto NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration` eseguendo il comando seguente:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Eseguire il comando seguente per ripristinare i pacchetti per il progetto:

        dotnet restore

3. Aprire il file *Program.cs* e aggiornare il metodo `Main` per usare Configurazione app effettuando una chiamata al metodo `builder.AddAzureAppConfiguration()`.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Impostare una variabile di ambiente denominata **ConnectionString** sulla chiave di accesso all'archivio di configurazione app. Se si usa il prompt dei comandi di Windows, eseguire il comando seguente e riavviare il prompt per rendere effettiva la modifica:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se si usa Windows PowerShell, eseguire il comando seguente:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se si usa macOS o Linux, eseguire il comando seguente:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Eseguire il comando seguente per compilare l'app console:

        dotnet build

3. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

        dotnet run

    ![Avvio rapido: esecuzione dell'app](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un nuovo archivio di configurazione app, che è stato usato con un'app console .NET Core. Per altre informazioni sull'uso di Configurazione app, continuare con l'esercitazione successiva sull'autenticazione.

> [!div class="nextstepaction"]
> [Identità gestite per l'integrazione di risorse di Azure](./integrate-azure-managed-service-identity.md)
