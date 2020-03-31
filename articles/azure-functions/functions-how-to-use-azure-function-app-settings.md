---
title: Configurare le impostazioni dell'app per le funzioni in AzureConfigure function app settings in Azure
description: Informazioni su come configurare le impostazioni dell'app per le funzioni di Azure.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276946"
---
# <a name="manage-your-function-app"></a>Gestisci la tua app per le funzioni 

In Funzioni di Azure un'app per le funzioni fornisce il contesto di esecuzione per le singole funzioni. I comportamenti dell'app per le funzioni si applicano a tutte le funzioni ospitate da un'app per le funzioni specifica. Tutte le funzioni in un'app per le funzioni devono avere la stessa [lingua.](supported-languages.md) 

Le singole funzioni in un'app per le funzioni vengono distribuite insieme e vengono ridimensionate insieme. Tutte le funzioni nella stessa app per le funzioni condividono risorse, per istanza, man mano che l'app per le funzioni viene ridimensionata. 

Le stringhe di connessione, le variabili di ambiente e altre impostazioni dell'applicazione vengono definite separatamente per ogni app per le funzioni. Tutti i dati che devono essere condivisi tra le app per le funzioni devono essere archiviati esternamente in un archivio persistente.

Questo articolo descrive come configurare e gestire le app per le funzioni. 

> [!TIP]  
> Molte opzioni di configurazione possono essere gestite anche tramite l'interfaccia della riga di comando di [Azure.] 

## <a name="get-started-in-the-azure-portal"></a>Attività iniziali nel portale di Azure

Innanzitutto passare al [portale di Azure] e accedere all'account di Azure. Nella barra di ricerca nella parte superiore del portale digitare il nome dell'app per le funzioni e selezionarla dall'elenco. Dopo aver selezionato l'app per le funzioni, viene visualizzata la pagina seguente:

![Panoramica dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

È possibile passare a tutto il necessario per gestire l'app per le funzioni dalla pagina di panoramica, in particolare le **[impostazioni dell'applicazione](#settings)** e **[le funzionalità della piattaforma](#platform-features)**.

## <a name="application-settings"></a><a name="settings"></a>Impostazioni dell'applicazione

La scheda **Impostazioni applicazione** mantiene le impostazioni utilizzate dall'app per le funzioni. Queste impostazioni sono archiviate crittografate ed è necessario selezionare **Mostra valori** per visualizzare i valori nel portale. È anche possibile accedere alle impostazioni dell'applicazione usando l'interfaccia della riga di comando di Azure.You can also access application settings by using the Azure CLI.

### <a name="portal"></a>Portale

Per aggiungere un'impostazione nel portale, selezionare **Nuova impostazione applicazione** e aggiungere la nuova coppia chiave-valore.

![Impostazioni dell'app per le funzioni nel portale di Azure.Function app settings in the Azure portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) comando restituisce le impostazioni dell'applicazione esistente, come nell'esempio seguente:The command returns the existing application settings, as in the following example:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Il [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando aggiunge o aggiorna un'impostazione dell'applicazione. Nell'esempio seguente viene creata `CUSTOM_FUNCTION_APP_SETTING` un'impostazione `12345`con una chiave denominata e un valore di :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Utilizzare le impostazioni dell'applicazione

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Quando si sviluppa un'app per le funzioni in locale, è necessario gestire le copie locali di questi valori nel file di progetto local.settings.json. Per ulteriori informazioni, consultate [File di impostazioni locali.](functions-run-local.md#local-settings-file)

## <a name="platform-features"></a>Funzionalità della piattaforma

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

### <a name="app-service-editor"></a><a name="editor"></a>Editor del servizio app

![Editor del servizio app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

L'editor del servizio app è un editor avanzato, disponibile nel portale, che si può usare per modificare i file di configurazione JSON e i file del codice nello stesso modo. Quando si sceglie questa opzione, viene aperta una scheda separata del browser con un editor di base. Ciò consente di realizzare l'integrazione con l'archivio Git, eseguire il codice e il relativo debug e modificare le impostazioni dell'app per le funzioni. Questo editor fornisce un ambiente di sviluppo avanzato per le funzioni rispetto all'editor di funzioni incorporato.  

Si consiglia di prendere in considerazione lo sviluppo delle funzioni nel computer locale. Quando si sviluppa in locale e si pubblica in Azure, i file di progetto sono di sola lettura nel portale. Per altre informazioni, vedere Codice e test di Funzioni di [Azure in locale.](functions-develop-local.md)

### <a name="console"></a><a name="console"></a>Console

![Console dell'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

La console nel portale è uno strumento ideale per gli sviluppatori quando si desidera interagire con l'app per le funzioni dalla riga di comando. I comandi comuni includono la creazione e lo spostamento di file e directory, nonché l'esecuzione di script e file batch. 

Quando si sviluppa in locale, è consigliabile usare gli strumenti [di base](functions-run-local.md) di Funzioni di Azure e l'interfaccia della riga di comando di [Azure.]

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Strumenti avanzati (Kudu)

![Configurare Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Gli strumenti avanzati per il servizio app, noto anche come Kudu, consentono l'accesso alle funzionalità amministrative avanzate dell'app per le funzioni. Dalla Kudu è possibile gestire informazioni di sistema, impostazioni dell'app, variabili di ambiente, estensioni del sito, intestazioni HTTP e variabili del server. È anche possibile avviare **Kudu** selezionando l'endpoint SCM per l'app per le funzioni, ad esempio`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Centro distribuzione

Quando si utilizza una soluzione di controllo del codice sorgente per sviluppare e gestire il codice delle funzioni, Deployment Center consente di compilare e distribuire dal controllo del codice sorgente. Il progetto viene compilato e distribuito in Azure quando si apportano aggiornamenti. Per altre informazioni, vedere Tecnologie di distribuzione in Funzioni di Azure.For more information, see [Deployment technologies in Azure Functions.](functions-deployment-technologies.md)

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Condivisione di risorse tra le origini

Per impedire l'esecuzione di codice dannoso sul client, i browser moderni bloccano le richieste dalle applicazioni Web alle risorse in esecuzione in un dominio separato. [La condivisione di risorse tra origini (CORS, Cross-origin resource sharing)](https://developer.mozilla.org/docs/Web/HTTP/CORS) consente a un'intestazione `Access-Control-Allow-Origin` di dichiarare quali origini sono autorizzate a chiamare gli endpoint nell'app per le funzioni.

#### <a name="portal"></a>Portale

Quando si configura l'elenco **Origini consentite** per l'app per le funzioni, l'intestazione `Access-Control-Allow-Origin` viene aggiunta automaticamente a tutte le risposte dagli endpoint HTTP nell'app per le funzioni. 

![Configurare l'elenco CORS dell'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Quando viene`*`utilizzato il carattere jolly ( ), tutti gli altri domini vengono ignorati. 

Utilizzare [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) il comando per aggiungere un dominio all'elenco delle origini consentite. L'esempio seguente aggiunge il dominio contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Utilizzare [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) il comando per elencare le origini consentite correnti.

### <a name="authentication"></a><a name="auth"></a>Authentication

![Configurare l'autenticazione per un'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Quando le funzioni usano un trigger HTTP, è possibile richiedere innanzitutto l'autenticazione delle chiamate. App Service supports Azure Active Directory authentication and sign-in with social providers, such as Facebook, Microsoft, and Twitter. Per informazioni dettagliate sulla configurazione di specifici provider di autenticazione, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Passaggi successivi

+ [Configurare le impostazioni del servizio app di Azure](../app-service/configure-common.md)
+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[Interfaccia della riga di comando di AzureAzure]: /cli/azure/
[Portale di Azure]: https://portal.azure.com
