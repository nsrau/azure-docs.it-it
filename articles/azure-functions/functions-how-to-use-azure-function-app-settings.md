---
title: Configure function app settings in Azure
description: Informazioni su come configurare le impostazioni dell'app per le funzioni di Azure.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230580"
---
# <a name="manage-your-function-app"></a>Manage your function app 

In Funzioni di Azure un'app per le funzioni fornisce il contesto di esecuzione per le singole funzioni. I comportamenti dell'app per le funzioni si applicano a tutte le funzioni ospitate da un'app per le funzioni specifica. All functions in a function app must be of the same [language](supported-languages.md). 

Individual functions in a function app are deployed together and are scaled together. All functions in the same function app share resources, per instance, as the function app scales. 

Connection strings, environment variables, and other application settings are defined separately for each function app. Any data that must be shared between function apps should be stored externally in a persisted store.

This article describes how to configure and manage your function apps. 

> [!TIP]  
> Many configuration options can also be managed by using the [interfaccia della riga di comando di Azure]. 

## <a name="get-started-in-the-azure-portal"></a>Inizia nel portale di Azure

Innanzitutto passare al [Azure portal] e accedere all'account di Azure. Nella barra di ricerca nella parte superiore del portale digitare il nome dell'app per le funzioni e selezionarla dall'elenco. Dopo aver selezionato l'app per le funzioni, viene visualizzata la pagina seguente:

![Panoramica dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

You can navigate to everything you need to manage your function app from the overview page, in particular the **[Application settings](#settings)** and **[Platform features](#platform-features)** .

## <a name="settings"></a>Impostazioni dell'applicazione

The **Application Settings** tab maintains settings that are used by your function app. These settings are stored encrypted, and you must select **Show values** to see the values in the portal. You can also access application settings by using the Azure CLI.

### <a name="portal"></a>di Microsoft Azure

To add a setting in the portal, select **New application setting** and add the new key-value pair.

![Function app settings in the Azure portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

The [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) command returns the existing application settings, as in the following example:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

The [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command adds or updates an application setting. The following example creates a setting with a key named `CUSTOM_FUNCTION_APP_SETTING` and a value of `12345`:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Use application settings

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

When you develop a function app locally, you must maintain local copies of these values in the local.settings.json project file. To learn more, see [Local settings file](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Platform features

![Scheda delle funzionalità della piattaforma per l'app per le funzioni.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Le app per le funzioni vengono eseguite e gestite dalla piattaforma Servizio app di Azure. Di conseguenza, le app per le funzioni hanno accesso alla maggior parte delle funzionalità di piattaforma di hosting Web di base di Azure. Nella scheda **Funzionalità della piattaforma** è possibile accedere a molte funzionalità della piattaforma del servizio app che è possibile usare nelle app per le funzioni. 

> [!NOTE]
> Non tutte le funzionalità del servizio app sono disponibili quando un'app per le funzioni viene eseguita nel piano di hosting a consumo.

The rest of this article focuses on the following App Service features in the Azure portal that are useful for Functions:

+ [Editor del servizio app](#editor)
+ [Console](#console)
+ [Strumenti avanzati (Kudu)](#kudu)
+ [Opzioni di distribuzione](#deployment)
+ [CORS](#cors)
+ [Autenticazione](#auth)

Per altre informazioni su come usare le impostazioni del servizio app, vedere [Configurare le impostazioni di del servizio app di Azure](../app-service/configure-common.md).

### <a name="editor"></a>Editor del servizio app

![Editor del servizio app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

L'editor del servizio app è un editor avanzato, disponibile nel portale, che si può usare per modificare i file di configurazione JSON e i file del codice nello stesso modo. Quando si sceglie questa opzione, viene aperta una scheda separata del browser con un editor di base. Ciò consente di realizzare l'integrazione con l'archivio Git, eseguire il codice e il relativo debug e modificare le impostazioni dell'app per le funzioni. This editor provides an enhanced development environment for your functions compared with the built-in function editor.  

We recommend that you consider developing your functions on your local computer. When you develop locally and publish to Azure, your project files are read-only in the portal. To learn more, see [Code and test Azure Functions locally](functions-develop-local.md).

### <a name="console"></a>Console

![Console dell'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

La console nel portale è uno strumento ideale per gli sviluppatori quando si desidera interagire con l'app per le funzioni dalla riga di comando. I comandi comuni includono la creazione e lo spostamento di file e directory, nonché l'esecuzione di script e file batch. 

When developing locally, we recommend using the [Azure Functions Core Tools](functions-run-local.md) and the [interfaccia della riga di comando di Azure].

### <a name="kudu"></a>Strumenti avanzati (Kudu)

![Configurare Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Gli strumenti avanzati per il servizio app, noto anche come Kudu, consentono l'accesso alle funzionalità amministrative avanzate dell'app per le funzioni. Dalla Kudu è possibile gestire informazioni di sistema, impostazioni dell'app, variabili di ambiente, estensioni del sito, intestazioni HTTP e variabili del server. È anche possibile avviare **Kudu** selezionando l'endpoint SCM per l'app per le funzioni, ad esempio`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment"></a>Deployment Center

When you use a source control solution to develop and maintain your functions code, Deployment Center lets you build and deploy from source control. Your project is built and deployed to Azure when you make updates. For more information, see [Deployment technologies in Azure Functions](functions-deployment-technologies.md).

### <a name="cors"></a>Cross-origin resource sharing

To prevent malicious code execution on the client, modern browsers block requests from web applications to resources running in a separate domain. [Cross-origin resource sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) lets an `Access-Control-Allow-Origin` header declare which origins are allowed to call endpoints on your function app.

#### <a name="portal"></a>di Microsoft Azure

When you configure the **Allowed origins** list for your function app, the `Access-Control-Allow-Origin` header is automatically added to all responses from HTTP endpoints in your function app. 

![Configure function app's CORS list](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

When the wildcard (`*`) is used, all other domains are ignored. 

Use the [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) command to add a domain to the allowed origins list. The following example adds the contoso.com domain:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Use the [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) command to list the current allowed origins.

### <a name="auth"></a>Autenticazione

![Configurare l'autenticazione per un'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Quando le funzioni usano un trigger HTTP, è possibile richiedere innanzitutto l'autenticazione delle chiamate. App Service supports Azure Active Directory authentication and sign-in with social providers, such as Facebook, Microsoft, and Twitter. Per informazioni dettagliate sulla configurazione di specifici provider di autenticazione, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Passaggi successivi

+ [Configurare le impostazioni del servizio app di Azure](../app-service/configure-common.md)
+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[interfaccia della riga di comando di Azure]: /cli/azure/
[Azure portal]: https://portal.azure.com
