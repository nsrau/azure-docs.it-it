---
title: Usare le identità gestite per accedere a Configurazione app
titleSuffix: Azure App Configuration
description: Eseguire l'autenticazione per app Azure configurazione mediante identità gestite
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: b1efeeef09e7c228eb8fc14de52a6beb2e9ffffe
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206839"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Usare le identità gestite per accedere a Configurazione app

Azure Active Directory le [identità gestite](../active-directory/managed-identities-azure-resources/overview.md) semplificano la gestione dei segreti per l'applicazione cloud. Con un'identità gestita, il codice può usare l'entità servizio creata per il servizio di Azure in cui è in esecuzione. È possibile usare un'identità gestita invece di usare credenziali distinte archiviate in Azure Key Vault o una stringa di connessione locale.

App Azure configurazione e le librerie client .NET Core, .NET Framework e Java Spring hanno un supporto di identità gestito incorporato. Sebbene non sia necessario usarlo, l'identità gestita elimina la necessità di un token di accesso che contiene segreti. Il codice può accedere all'archivio di configurazione dell'app usando solo l'endpoint del servizio. È possibile incorporare questo URL direttamente nel codice senza esporre alcun segreto.

Questo articolo illustra come è possibile sfruttare l'identità gestita per accedere alla configurazione dell'app. Si basa sull'app Web presentata nelle guide introduttive. Prima di continuare,  [creare prima un'app ASP.NET Core con la configurazione dell'app](./quickstart-aspnet-core-app.md) .

Questo articolo illustra anche come è possibile usare l'identità gestita insieme ai riferimenti Key Vault della configurazione dell'app. Con una singola identità gestita, è possibile accedere facilmente a entrambi i segreti da Key Vault e i valori di configurazione dalla configurazione dell'app. Per esplorare questa funzionalità, terminare usare prima di tutto [Key Vault riferimenti con ASP.NET Core](./use-key-vault-references-dotnet-core.md) .

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Concedere a un'identità gestita l'accesso a Configurazione app.
> * Configurare l'app in modo da usare un'identità gestita per connettersi a Configurazione app.
> * Facoltativamente, configurare l'app per l'uso di un'identità gestita quando ci si connette a Key Vault tramite una configurazione dell'app Key Vault riferimento.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure cloud Shell configurata](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Aggiungere un'identità gestita

Per configurare un'identità gestita nel portale, è innanzitutto necessario creare un'applicazione e quindi abilitare la funzionalità.

1. Creare un'istanza di servizi app nel [portale di Azure](https://portal.azure.com) come di consueto. Passare all'app nel portale.

1. Scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro a sinistra e selezionare **Identità**.

1. Nella scheda **Assegnata dal sistema** impostare **Stato** su **Attivato** e selezionare **Salva**.

1. Risposta **Sì** quando viene richiesto di abilitare l'identità gestita assegnata dal sistema.

    ![Impostare l'identità gestita nel servizio app](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Concedere l'accesso a Configurazione app

1. Nella [portale di Azure](https://portal.azure.com)selezionare tutte le **risorse** e selezionare l'archivio di configurazione dell'app creato nella Guida introduttiva.

1. Selezionare **Controllo di accesso (IAM)** .

1. Nella scheda **Verifica l'accesso** selezionare **Aggiungi** nell'interfaccia utente della scheda **Aggiungi un'assegnazione di ruolo**.

1. In **ruolo**selezionare **lettore dati di configurazione dell'app**. In **Assegna accesso a** selezionare **Servizio app** in **Identità gestita assegnata dal sistema**.

1. In **Sottoscrizione** selezionare la sottoscrizione di Azure. Selezionare la risorsa Servizio app per la propria app.

1. Selezionare **Salva**.

    ![Aggiungere un'identità gestita](./media/add-managed-identity.png)

1. Facoltativo: se si vuole concedere l'accesso anche a Key Vault, seguire le istruzioni in [fornire Key Vault l'autenticazione con un'identità gestita](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Usare un'identità gestita

1. Aggiungere un riferimento al pacchetto *Azure. Identity* :

    ```cli
    dotnet add package Azure.Identity
    ```

1. Trovare l'endpoint per l'archivio di configurazione dell'app. Questo URL è elencato nella scheda **chiavi di accesso** per l'archivio nel portale di Azure.

1. Aprire *appsettings.json* e aggiungere lo script seguente. Sostituire *\<service_endpoint>* , incluse le parentesi, con l'URL dell'archivio di configurazione dell'app.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Aprire *Program.cs*e aggiungere un riferimento agli `Azure.Identity` `Microsoft.Azure.Services.AppAuthentication` spazi dei nomi e:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Se si vuole accedere solo ai valori archiviati direttamente nella configurazione dell'app, aggiornare il `CreateWebHostBuilder` Metodo sostituendo il `config.AddAzureAppConfiguration()` metodo.

    > [!IMPORTANT]
    > `CreateHostBuilder` sostituisce `CreateWebHostBuilder` in .NET Core 3.0.  Selezionare la sintassi corretta in base all'ambiente.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. Per usare sia i valori di configurazione delle app che i riferimenti Key Vault, aggiornare *Program.cs* come illustrato di seguito. Questo codice crea un nuovo oggetto `KeyVaultClient` usando `AzureServiceTokenProvider` e passa questo riferimento a una chiamata al `UseAzureKeyVault` metodo.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>());
    ```
    ---

    È ora possibile accedere a Key Vault riferimenti come qualsiasi altra chiave di configurazione dell'app. Il provider di configurazione utilizzerà l'oggetto `KeyVaultClient` configurato per l'autenticazione Key Vault e il recupero del valore.

> [!NOTE]
> `ManagedIdentityCredential` supporta solo l'autenticazione di identità gestita. Non funziona negli ambienti locali. Se si vuole eseguire il codice localmente, provare a usare `DefaultAzureCredential` , che supporta anche l'autenticazione basata su entità servizio. Per informazioni dettagliate, vedere il [collegamento](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential) .

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Eseguire la distribuzione dall'archivio Git locale

Il modo più semplice per abilitare la distribuzione git locale per l'app con il server di compilazione Kudu consiste nell'usare [Azure cloud Shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Abilitare l'archivio Git locale con Kudu
Se non si dispone di un repository git locale per l'app, è necessario inizializzarne uno. Per inizializzare un repository git locale, eseguire i comandi seguenti dalla directory del progetto dell'app:

```cmd
git init
git add .
git commit -m "Initial version"
```

Per abilitare la distribuzione git locale per l'app con il server di compilazione Kudu, eseguire [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) in cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Questo comando offre un risultato simile all'output seguente:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Distribuire il progetto

Nella _finestra del terminale locale_aggiungere un'area remota di Azure al repository git locale. Sostituire _\<url>_ con l'URL del GIT remoto ottenuto dall' [Abilitazione di git locale con Kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando viene chiesta la password, immettere quella creata in [Configurare un utente della distribuzione](#configure-a-deployment-user). Non usare la stessa password usata per accedere al portale di Azure.

```bash
git push azure master
```

Nell'output potrebbe essere visualizzata l'automazione specifica di runtime, ad esempio MSBuild per ASP.NET, `npm install` per Node.js e `pip install` per Python.

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure

Passare all'app Web usando un browser per verificare che il contenuto sia stato distribuito.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Usare l'identità gestita in altri linguaggi

Anche i provider di Configurazione app per .NET Framework e Java Spring offrono supporto incorporato per l'identità gestita. Quando si configura uno di questi provider, è possibile usare l'endpoint URL dell'archivio invece della relativa stringa di connessione completa.

Ad esempio, è possibile aggiornare l'app console .NET Framework creata nella Guida introduttiva per specificare le impostazioni seguenti nel file di *App.config* :

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata aggiunta un'identità gestita di Azure per semplificare l'accesso alla configurazione dell'app e migliorare la gestione delle credenziali per l'app. Per altre informazioni sull'uso di Configurazione app, passare agli esempi dell'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Esempi dell'interfaccia della riga di comando](./cli-samples.md)
