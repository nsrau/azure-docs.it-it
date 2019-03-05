---
title: Esercitazione per l'integrazione con Identità gestite di Azure | Microsoft Docs
description: In questa esercitazione si imparerà a usare Identità gestite di Azure per eseguire l'autenticazione e accedere a Configurazione app di Azure
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
ms.openlocfilehash: 874522b6b4ca3739e0736d4f70f76bb82cad25f9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957352"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Esercitazione: Integrazione con Identità gestite di Azure

[Identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) di Azure Active Directory semplifica la gestione dei segreti dell'applicazione cloud. Con un'identità gestita è possibile configurare il codice in modo che usi l'entità servizio creata per il servizio di calcolo di Azure nel quale è in esecuzione, invece di usare credenziali distinte archiviate in Azure Key Vault o una stringa di connessione locale. Configurazione app di Azure e le relative librerie client .NET Core, .NET e Java Spring offrono il supporto MSI incorporato. Benché il suo utilizzo non sia obbligatorio, MSI elimina la necessità di un token di accesso che contiene i segreti. Il codice deve semplicemente conoscere l'endpoint di servizio di un archivio di configurazione delle app per potervi accedere e questo URL può essere incorporato direttamente nel codice senza il rischio di esporre alcun segreto.

Questa esercitazione illustra come sfruttare MSI per accedere a Configurazione app. È basata sull'app Web presentata nelle guide introduttive. Prima di continuare, completare le procedure descritte in [Create an ASP.NET Core app with App Configuration](./quickstart-aspnet-core-app.md) (Creare un'app ASP.NET Core con Configurazione app).

Per completare i passaggi descritti in questa esercitazione è possibile usare qualsiasi editor di codice. Tuttavia, [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Concedere l'accesso tramite identità gestita a Configurazione app
> * Configurare l'app in modo che usi un'identità gestita per connettersi a Configurazione app

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [ASP.NET Core SDK](https://www.microsoft.com/net/download/windows)
* [Azure Cloud Shell configurato](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Aggiungere un'identità gestita

Per configurare un'identità gestita nel portale, è prima necessario creare un'applicazione come di consueto e quindi abilitare la funzionalità.

1. Creare un'app nel [portale di Azure](https://aka.ms/azconfig/portal) come di consueto. Accedervi nel portale.

2. Scorrere verso il basso fino al gruppo **Impostazioni** nel riquadro di spostamento a sinistra e selezionare **Identità**.

3. All'interno della scheda **Assegnata dal sistema** impostare **Stato** su **Attivato** e fare clic su **Salva**.

    ![Impostare l'identità gestita nel servizio app](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Concedere l'accesso a Configurazione app

1. Nel [portale di Azure](https://aka.ms/azconfig/portal) fare clic su **Tutte le risorse** e sull'archivio di configurazione delle app creato nella guida introduttiva.

2. Selezionare **Controllo di accesso (IAM)**.

3. Nella scheda **Verifica l'accesso** fare clic su **Aggiungi** nell'interfaccia utente della scheda **Aggiungi un'assegnazione di ruolo**.

4. Impostare **Ruolo** su *Collaboratore* e **Assegna accesso a** su *Servizio app* (in *Identità gestita assegnata dal sistema*).

5. Impostare **Sottoscrizione** sulla propria sottoscrizione di Azure e selezionare la risorsa Servizio app per la propria app.

6. Fare clic su **Save**.

    ![Aggiungere identità gestita](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Usare un'identità gestita

1. Aprire *appsettings.json*, aggiungere il codice seguente e sostituire *<service_endpoint>* (incluse le parentesi acute) con l'URL dell'archivio di configurazione delle app:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Aprire *Program.cs* e aggiornare il metodo `CreateWebHostBuilder` sostituendo il metodo `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Eseguire la distribuzione dall'archivio Git locale

Il modo più semplice per abilitare la distribuzione dell'archivio Git locale per l'app con il server di compilazione Kudu è usare Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Abilitare l'archivio Git locale con Kudu

Per abilitare la distribuzione dell'archivio Git locale per l'app con il server di compilazione Kudu, eseguire [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) in Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Per creare un'app abilitata per Git, eseguire invece [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) in Cloud Shell con il parametro `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

L'output del comando `az webapp create` dovrebbe essere simile al seguente:

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

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando viene richiesta una password, assicurarsi di immettere la password creata nella sezione relativa alla [configurazione di un utente della distribuzione](#configure-a-deployment-user), anziché quella usata per accedere al portale di Azure.

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

Anche i provider di Configurazione app per .NET Framework e Java Spring offrono supporto incorporato per l'identità gestita. In questi casi è sufficiente usare l'endpoint dell'URL dell'archivio di configurazione delle app invece della stringa di connessione completa durante la configurazione di un provider. Ad esempio, per l'app console di .NET Framework creata nella guida introduttiva occorre specificare le impostazioni seguenti nel file *App.config*:

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

In questa esercitazione è stata aggiunta un'identità del servizio gestita di Azure per facilitare l'accesso a Configurazione app e migliorare la gestione delle credenziali dell'app. Per altre informazioni sull'uso di Configurazione app, continuare con gli esempi dell'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Esempi dell'interfaccia della riga di comando](./cli-samples.md)
