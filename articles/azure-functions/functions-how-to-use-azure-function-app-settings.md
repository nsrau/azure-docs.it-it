---
title: Configurare le impostazioni dell'app per le funzioni in Azure
description: Informazioni su come configurare le impostazioni dell'app per le funzioni di Azure.
author: ggailey777
manager: gwallace
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 7ad7f6156bbd8ea86e3e71bda4b23dac9722a0ef
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170755"
---
# <a name="manage-your-function-app"></a>Gestire l'app per le funzioni 

In Funzioni di Azure un'app per le funzioni fornisce il contesto di esecuzione per le singole funzioni. I comportamenti dell'app per le funzioni si applicano a tutte le funzioni ospitate da un'app per le funzioni specifica. Tutte le funzioni in un'app per le funzioni devono essere della stessa [lingua](supported-languages.md). 

Le singole funzioni in un'app per le funzioni vengono distribuite insieme e vengono ridimensionate insieme. Tutte le funzioni nella stessa app per le funzioni condividono le risorse, per ogni istanza, di ridimensionamento dell'app per le funzioni. 

Le stringhe di connessione, le variabili di ambiente e altre impostazioni dell'applicazione sono definite separatamente per ogni app per le funzioni. Tutti i dati che devono essere condivisi tra app per le funzioni devono essere archiviati esternamente in un archivio permanente.

Questo articolo descrive come configurare e gestire le app per le funzioni. 

> [!TIP]  
> Molte opzioni di configurazione possono essere gestite anche tramite l' [Interfaccia della riga di comando di Azure]della riga di comando di Azure. 

## <a name="get-started-in-the-azure-portal"></a>Inizia nel portale di Azure

Innanzitutto passare al [portale di Azure] e accedere all'account di Azure. Nella barra di ricerca nella parte superiore del portale digitare il nome dell'app per le funzioni e selezionarla dall'elenco. Dopo aver selezionato l'app per le funzioni, viene visualizzata la pagina seguente:

![Panoramica dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

È possibile passare a tutti gli elementi necessari per gestire l'app per le funzioni dalla pagina Panoramica, in particolare le **[impostazioni dell'applicazione](#settings)** e le **[funzionalità della piattaforma](#platform-features)** .

## <a name="settings"></a>Impostazioni dell'applicazione

La scheda **Impostazioni applicazione** mantiene le impostazioni usate dall'app per le funzioni. Queste impostazioni vengono archiviate crittografate ed è necessario selezionare **Mostra valori** per visualizzare i valori nel portale. È anche possibile accedere alle impostazioni dell'applicazione usando l'interfaccia della riga di comando di Azure.

### <a name="portal"></a>Portale

Per aggiungere un'impostazione nel portale, selezionare **nuova impostazione applicazione** e aggiungere la nuova coppia chiave-valore.

![Impostazioni dell'app per le funzioni nella portale di Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) comando restituisce le impostazioni dell'applicazione esistenti, come nell'esempio seguente:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Il [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando aggiunge o aggiorna un'impostazione dell'applicazione. Nell'esempio seguente viene creata un'impostazione con una chiave `CUSTOM_FUNCTION_APP_SETTING` denominata e un `12345`valore:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Usare le impostazioni dell'applicazione

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Quando si sviluppa un'app per le funzioni in locale, è necessario mantenere le copie locali di questi valori nel file di progetto local. Settings. JSON. Per altre informazioni, vedere [file di impostazioni locali](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Funzionalità della piattaforma

![Scheda delle funzionalità della piattaforma per l'app per le funzioni.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Le app per le funzioni vengono eseguite e gestite dalla piattaforma Servizio app di Azure. Di conseguenza, le app per le funzioni hanno accesso alla maggior parte delle funzionalità di piattaforma di hosting Web di base di Azure. Nella scheda **Funzionalità della piattaforma** è possibile accedere a molte funzionalità della piattaforma del servizio app che è possibile usare nelle app per le funzioni. 

> [!NOTE]
> Non tutte le funzionalità del servizio app sono disponibili quando un'app per le funzioni viene eseguita nel piano di hosting a consumo.

Il resto di questo articolo è incentrato sulle funzionalità del servizio app seguenti nell'portale di Azure utili per le funzioni:

+ [Editor del servizio app](#editor)
+ [Console](#console)
+ [Strumenti avanzati (Kudu)](#kudu)
+ [Opzioni di distribuzione](#deployment)
+ [CORS](#cors)
+ [Autenticazione](#auth)

Per altre informazioni su come usare le impostazioni del servizio app, vedere [Configurare le impostazioni di del servizio app di Azure](../app-service/configure-common.md).

### <a name="editor"></a>Editor del servizio app

![Editor del servizio app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

L'editor del servizio app è un editor avanzato, disponibile nel portale, che si può usare per modificare i file di configurazione JSON e i file del codice nello stesso modo. Quando si sceglie questa opzione, viene aperta una scheda separata del browser con un editor di base. Ciò consente di realizzare l'integrazione con l'archivio Git, eseguire il codice e il relativo debug e modificare le impostazioni dell'app per le funzioni. Questo editor fornisce un ambiente di sviluppo avanzato per le funzioni rispetto all'editor di funzioni incorporato.  

Si consiglia di provare a sviluppare le funzioni nel computer locale. Quando si sviluppa in locale e si esegue la pubblicazione in Azure, i file di progetto sono di sola lettura nel portale. Per altre informazioni, vedere [codice e test di funzioni di Azure in locale](functions-develop-local.md).

### <a name="console"></a>Console

![Console dell'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

La console nel portale è uno strumento ideale per gli sviluppatori quando si desidera interagire con l'app per le funzioni dalla riga di comando. I comandi comuni includono la creazione e lo spostamento di file e directory, nonché l'esecuzione di script e file batch. 

Quando si sviluppa localmente, è consigliabile usare il [Azure Functions Core Tools](functions-run-local.md) e l'interfaccia della riga di comando di [Interfaccia della riga di comando di Azure].

### <a name="kudu"></a>Strumenti avanzati (Kudu)

![Configurare Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Gli strumenti avanzati per il servizio app, noto anche come Kudu, consentono l'accesso alle funzionalità amministrative avanzate dell'app per le funzioni. Dalla Kudu è possibile gestire informazioni di sistema, impostazioni dell'app, variabili di ambiente, estensioni del sito, intestazioni HTTP e variabili del server. È anche possibile avviare **Kudu** selezionando l'endpoint SCM per l'app per le funzioni, ad esempio`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment"></a>Centro distribuzione

Quando si usa una soluzione di controllo del codice sorgente per sviluppare e gestire il codice delle funzioni, centro distribuzione consente di compilare e distribuire dal controllo del codice sorgente. Il progetto viene compilato e distribuito in Azure quando si effettuano gli aggiornamenti. Per altre informazioni, vedere [tecnologie di distribuzione in funzioni di Azure](functions-deployment-technologies.md).

### <a name="cors"></a>Condivisione di risorse tra le origini

Per impedire l'esecuzione di codice dannoso sul client, i browser moderni bloccano le richieste dalle applicazioni Web alle risorse in esecuzione in un dominio separato. La [condivisione di risorse tra le origini (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) consente a un' `Access-Control-Allow-Origin` intestazione di dichiarare quali origini sono autorizzate a chiamare gli endpoint nell'app per le funzioni.

#### <a name="portal"></a>Portale

Quando si configura l'elenco di **origini** consentite per l'app `Access-Control-Allow-Origin` per le funzioni, l'intestazione viene aggiunta automaticamente a tutte le risposte dagli endpoint HTTP nell'app per le funzioni. 

![Configurare l'elenco CORS dell'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Quando si usa il`*`carattere jolly (), tutti gli altri domini vengono ignorati. 

Usare il [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) comando per aggiungere un dominio all'elenco delle origini consentite. Nell'esempio seguente viene aggiunto il dominio contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Usare il [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) comando per elencare le origini consentite correnti.

### <a name="auth"></a>Autenticazione

![Configurare l'autenticazione per un'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Quando le funzioni usano un trigger HTTP, è possibile richiedere innanzitutto l'autenticazione delle chiamate. Il servizio app supporta l'autenticazione Azure Active Directory e l'accesso con i provider di social networking, ad esempio Facebook, Microsoft e Twitter. Per informazioni dettagliate sulla configurazione di specifici provider di autenticazione, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Passaggi successivi

+ [Configurare le impostazioni del servizio app di Azure](../app-service/configure-common.md)
+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[Interfaccia della riga di comando di Azure]: /cli/azure/
[Portale di Azure]: https://portal.azure.com
