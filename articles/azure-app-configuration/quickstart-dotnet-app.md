---
title: Guida introduttiva di Configurazione app di Azure con .NET Framework | Microsoft Docs
description: Guida introduttiva per l'uso di Configurazione app di Azure con le app .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b5e41b1f9ee982b8ff8c86232f715d5dab705cd6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962163"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Guida introduttiva: Creare un'app .NET Framework con Configurazione app di Azure

Configurazione app di Azure è un servizio di configurazione gestito di Azure. Consente di archiviare e gestire con facilità tutte le impostazioni delle applicazioni in un'unica risorsa separata dal codice. Questa guida introduttiva mostra come incorporare il servizio in un'app console desktop di Windows basata su .NET Framework.

![Guida introduttiva completa in locale](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, installare [Visual Studio 2017](https://visualstudio.microsoft.com/vs) e [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) o versione successiva, se non è già stato fatto.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Creare un archivio di configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Creare un'app console .NET

1. Avviare Visual Studio e selezionare **File** > **Nuovo** > **Progetto**.

2. Nella finestra di dialogo **Nuovo progetto** selezionare **Installati**, espandere **Visual C#** > **Windows Desktop**, selezionare **App console (.NET Framework)**, digitare un **nome** per il progetto, scegliere **.NET Framework 4.7.1** o superiore e fare clic su **OK**.

## <a name="connect-to-app-configuration-store"></a>Connettersi all'archivio di configurazione app

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**. Nella scheda **Sfoglia** cercare e aggiungere i pacchetti NuGet seguenti al progetto. Se non si riesce a trovarli, selezionare la casella **Includi versione preliminare**.
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Aggiornare il file *App.config* del progetto come segue:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   Tenere presente che, poiché la stringa di connessione dell'archivio di configurazione app verrà letta dalla variabile di ambiente `ConnectionString`, è importante aggiungere il generatore di configurazioni `Environment` prima di `MyConfigStore` nella proprietà `configBuilders` della sezione `appSettings`.

3. Aprire il file *Program.cs* e aggiornare il metodo `Main` per usare Configurazione app effettuando una chiamata a `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Impostare una variabile di ambiente denominata **ConnectionString** sulla stringa di connessione dell'archivio di configurazione app. Se si usa il prompt dei comandi di Windows, eseguire il comando seguente:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se si usa Windows PowerShell, eseguire il comando seguente:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Riavviare Visual Studio per rendere effettiva la modifica e quindi premere **CTRL + F5** per compilare ed eseguire l'app console.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un nuovo archivio di configurazione app, che è stato usato con un'app console .NET Framework. Per altre informazioni sull'uso di Configurazione app, continuare con l'esercitazione successiva sull'autenticazione.

> [!div class="nextstepaction"]
> [Identità gestite per l'integrazione di risorse di Azure](./integrate-azure-managed-service-identity.md)
