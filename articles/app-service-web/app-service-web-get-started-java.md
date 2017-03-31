---
title: Creare la prima app Web Java in Azure in cinque minuti | Microsoft Docs
description: Informazioni su come eseguire facilmente app Web nel servizio app mediante la distribuzione di un&quot;app di esempio.
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: ab9b49cd86f37499ebf8fd8162779be305019f36
ms.lasthandoff: 03/18/2017


---
# <a name="create-your-first-java-web-app-in-azure-in-five-minutes"></a>Creare la prima app Web Java in Azure in cinque minuti
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

Questa guida introduttiva illustra come distribuire la prima app Web Java nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md) in pochi minuti.

Prima di iniziare, verificare che l'interfaccia della riga di comando di Azure sia stata installata. Per altre informazioni, vedere [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli) (Guida all'installazione dell'interfaccia della riga di comando di Azure).

## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere ad Azure eseguendo `az login` e attenendosi alle indicazioni visualizzate.
   
```azurecli
az login
```
   
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse   
Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md). In tale gruppo si inseriranno tutte le risorse che si vogliono gestire insieme, ad esempio l'app Web e il back-end del database SQL.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Per visualizzare i possibili valori utilizzabili per `---location`, usare il comando `az appservice list-locations` dell'interfaccia della riga di comando di Azure.

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app
Creare un [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO". 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
```

## <a name="create-a-web-app"></a>Creare un'app Web
Creare un'app Web con un nome univoco in `<app_name>`.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="deploy-sample-application"></a>Distribuire l'applicazione di esempio
Distribuire un'app Java di esempio da GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/azure-appservice-samples/JavaCoffeeShopTemplate.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Passare all'app Web
Per osservare l'app in esecuzione in Azure, eseguire questo comando.

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

La prima app Web Java è ora in esecuzione nel servizio app di Azure.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli [script dell'interfaccia della riga di comando per le app Web](app-service-cli-samples.md) già creati.

