---
title: Guida introduttiva di Configurazione app di Azure con .NET Framework | Microsoft Docs
description: In questo articolo viene creata un'app .NET Framework con Configurazione app di Azure per centralizzare l'archiviazione e la gestione delle impostazioni dell'applicazione separatamente dal codice.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: d338ca5cf2c794f15d3eb535b5280208236d1e34
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767764"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Guida introduttiva: Creare un'app .NET Framework con Configurazione app di Azure

In questa guida di avvio rapido si incorpora Configurazione app di Azure in un'app console basata su .NET Framework per centralizzare l'archiviazione e la gestione delle impostazioni dell'applicazione separatamente dal codice.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selezionare **Esplora configurazioni** > **Crea** > **Coppia chiave-valore** per aggiungere le coppie chiave-valore seguenti:

    | Chiave | valore |
    |---|---|
    | TestApp:Settings:Message | Dati di Configurazione app di Azure |

    Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto** per il momento.

8. Selezionare **Applica**.

## <a name="create-a-net-console-app"></a>Creare un'app console .NET

1. Avviare Visual Studio e selezionare **File** > **Nuovo** > **Progetto**.

1. In **Crea un nuovo progetto** filtrare in base al tipo di progetto **Console** e quindi fare clic su **App console (.NET Framework)** . Selezionare **Avanti**.

1. In **Configura il nuovo progetto** immettere un nome di progetto. In **Framework** selezionare **.NET Framework 4.7.1** o versione successiva. Selezionare **Create** (Crea).

## <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di Configurazione app

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**. Nella scheda **Sfoglia** cercare e aggiungere i pacchetti NuGet seguenti al progetto.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Aggiornare il file *App.config* del progetto come segue:

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

   La stringa di connessione dell'archivio di Configurazione app viene letta dalla variabile di ambiente `ConnectionString`. Aggiungere il generatore di configurazione `Environment` prima di `MyConfigStore` nella proprietà `configBuilders` della sezione `appSettings`.

1. Aprire il file *Program.cs* e aggiornare il metodo `Main` per usare Configurazione app effettuando una chiamata a `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
        Console.ReadKey();
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Aggiornare il file **App.config** sostituendo `${ConnectionString}` con la stringa di connessione effettiva all'istanza di Configurazione app. È possibile trovarla nella scheda **Chiavi di accesso** della risorsa Configurazione app nel portale di Azure.

1. Premere CTRL+F5 per compilare ed eseguire l'app console.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un nuovo archivio di Configurazione app, che è stato usato con un'app console .NET Framework. Il valore `AppSettings` di `ConfigurationManager` non verrà modificato dopo l'avvio dell'applicazione. La libreria del provider di configurazione .NET Standard di Configurazione app può tuttavia essere usata anche in un'app .NET Framework. Per informazioni su come abilitare l'app .NET Framework per aggiornare in modo dinamico le impostazioni di configurazione, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Abilitare la configurazione dinamica](./enable-dynamic-configuration-dotnet.md)
