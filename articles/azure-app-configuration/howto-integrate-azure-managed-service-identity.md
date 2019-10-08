---
title: Eseguire l'integrazione con le identità gestite di Azure | Microsoft Docs
description: Informazioni su come usare le identità gestite di Azure per l'autenticazione con e ottenere l'accesso alla configurazione di app Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 3a5517c31cdac0bf6f5ea386a8614d15521d4479
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035543"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrazione con identità gestite di Azure

Le [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) di Azure Active Directory semplificano la gestione dei segreti dell'applicazione cloud. Con un'identità gestita, è possibile configurare il codice in modo da usare l'entità servizio creata per il servizio di calcolo di Azure in cui viene eseguita. È possibile usare un'identità gestita invece di usare credenziali distinte archiviate in Azure Key Vault o una stringa di connessione locale. 

Configurazione app di Azure e le relative librerie client .NET Core, .NET e Java Spring offrono il supporto dell'identità del servizio gestita incorporato. Benché il suo utilizzo non sia obbligatorio, l'identità del servizio gestita elimina la necessità di un token di accesso che contiene i segreti. Il codice può accedere all'archivio di configurazione dell'app usando solo l'endpoint del servizio. È possibile incorporare questo URL direttamente nel codice senza il rischio di esporre alcun segreto.

Questa esercitazione illustra come sfruttare MSI per accedere a Configurazione app. Si basa sull'app Web presentata nelle guide introduttive. Prima di continuare, completare le procedure descritte in [Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md).

Questa esercitazione, inoltre, Mostra come è possibile usare l'identità del servizio gestito insieme ai riferimenti Key Vault della configurazione dell'app. Questo consente di accedere facilmente ai segreti archiviati in Key Vault, oltre ai valori di configurazione nella configurazione dell'app. Per esplorare questa funzionalità, terminare usare prima di tutto [Key Vault riferimenti con ASP.NET Core](./use-key-vault-references-dotnet-core.md) .

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Concedere a un'identità gestita l'accesso a Configurazione app.
> * Configurare l'app in modo da usare un'identità gestita per connettersi a Configurazione app.
> * Facoltativamente, configurare l'app per l'uso di un'identità gestita quando ci si connette a Key Vault tramite una configurazione dell'app Key Vault riferimento.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell configurato](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Aggiungere un'identità gestita

Per configurare un'identità gestita nel portale, è prima necessario creare un'applicazione come di consueto e quindi abilitare la funzionalità.

1. Creare un'istanza di servizi app nel [portale di Azure](https://portal.azure.com) come di consueto. Passare all'app nel portale.

2. Scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro a sinistra e selezionare **Identità**.

3. Nella scheda **Assegnata dal sistema** impostare **Stato** su **Attivato** e selezionare **Salva**.

4. Risposta **Sì** quando viene richiesto di abilitare l'identità gestita assegnata dal sistema.

    ![Impostare l'identità gestita nel servizio app](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Concedere l'accesso a Configurazione app

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutte le risorse** e quindi selezionare l'archivio di configurazione app creato nella guida di avvio rapido.

1. Selezionare **Controllo di accesso (IAM)** .

1. Nella scheda **Verifica l'accesso** selezionare **Aggiungi** nell'interfaccia utente della scheda **Aggiungi un'assegnazione di ruolo**.

1. In **Ruolo** selezionare **Collaboratore**. In **Assegna accesso a** selezionare **Servizio app** in **Identità gestita assegnata dal sistema**.

1. In **Sottoscrizione** selezionare la sottoscrizione di Azure. Selezionare la risorsa Servizio app per la propria app.

1. Selezionare **Salva**.

    ![Aggiungere un'identità gestita](./media/add-managed-identity.png)

1. Facoltativo: Se si vuole concedere l'accesso anche a Key Vault, seguire le istruzioni in [fornire Key Vault l'autenticazione con un'identità gestita](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Usare un'identità gestita

1. Trovare l'URL dell'archivio di configurazione dell'app accedendo alla relativa schermata di configurazione nel portale di Azure, quindi facendo clic sulla scheda **chiavi di accesso** .

1. Aprire *appsettings.json* e aggiungere lo script seguente. Sostituire *\<service_endpoint >* , incluse le parentesi, con l'URL dell'archivio di configurazione dell'app. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Se si vuole accedere solo ai valori archiviati direttamente nella configurazione dell'app, aprire *Program.cs*e aggiornare il metodo `CreateWebHostBuilder` sostituendo il metodo `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

1. Se si vuole usare i valori di configurazione dell'app, nonché Key Vault riferimenti, aprire *Program.cs*e aggiornare il metodo `CreateWebHostBuilder`, come illustrato di seguito. In questo modo viene creato un nuovo `KeyVaultClient` usando un `AzureServiceTokenProvider` e il riferimento viene passato a una chiamata al metodo `UseAzureKeyVault`.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    È ora possibile accedere a Key Vault riferimenti come qualsiasi altra chiave di configurazione dell'app. Il provider di configurazione utilizzerà il `KeyVaultClient` configurato per l'autenticazione per Key Vault e il recupero del valore.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Eseguire la distribuzione dall'archivio Git locale

Il modo più semplice per abilitare la distribuzione dell'archivio Git locale per l'app con il server di compilazione Kudu è usare Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Abilitare l'archivio Git locale con Kudu
Se non si dispone di un repository git locale per l'app, è necessario inizializzarne uno. A tale scopo, eseguire i comandi seguenti dalla directory del progetto dell'app:

```cmd
git init
git add .
git commit -m "Initial version"
```

Per abilitare la distribuzione dell'archivio Git locale per l'app con il server di compilazione Kudu, eseguire [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) in Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Per creare un'app abilitata per Git, eseguire invece [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) in Cloud Shell con il parametro `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

L'output del comando `az webapp create` sarà simile al seguente:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Distribuire il progetto

Nella _finestra del terminale locale_ aggiungere un'istanza remota di Azure al repository Git locale. Sostituire _\<url>_ con l'URL del repository Git remoto ottenuto al passaggio [Abilitare la distribuzione Git per l'app](#enable-local-git-with-kudu).

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

![App in esecuzione nel servizio app](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Usare l'identità gestita in altri linguaggi

Anche i provider di Configurazione app per .NET Framework e Java Spring offrono supporto incorporato per l'identità gestita. In questi casi usare l'endpoint dell'URL dell'archivio di configurazione app invece della stringa di connessione completa durante la configurazione di un provider. Ad esempio, per l'app console di .NET Framework creata nella guida di avvio rapido occorre specificare le impostazioni seguenti nel file *App.config*:

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

> [!div class="nextstepaction"]
> [Esempi dell'interfaccia della riga di comando](./cli-samples.md)
