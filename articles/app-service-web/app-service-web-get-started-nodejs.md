---
title: Creare la prima app Web Node.js in Azure in cinque minuti | Microsoft Docs
description: Informazioni su come eseguire facilmente app Web nel servizio app distribuendo un&quot;app Node.js di esempio.
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 412cc786-5bf3-4e1b-b696-6a08cf46501e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 89a9e29261e338aceb4ff6feb55cf344afeeb3d4
ms.lasthandoff: 03/21/2017


---
# <a name="create-your-first-nodejs-web-app-in-azure-in-five-minutes"></a>Creare la prima app Web Node.js in Azure in cinque minuti
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Questa guida introduttiva illustra come distribuire la prima app Web Node.js nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md) in pochi minuti.

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
Creare un [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "Standard" che esegue un contenitore Linux. 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --is-linux --sku S1
```

## <a name="create-a-web-app"></a>Creare un'app Web
Creare un'app Web con un nome univoco in `<app_name>`.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="configure-the-linux-container"></a>Configurare il contenitore Linux
Configurare il contenitore Linux per l'uso dell'immagine Node.js 6.9.3 predefinita.

```azurecli
az appservice web config update --node-version 6.9.3 --name <app_name> --resource-group myResourceGroup
```

## <a name="deploy-sample-application"></a>Distribuire l'applicazione di esempio
Distribuire un'app Node.js di esempio da GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Passare all'app Web
Per osservare l'app in esecuzione in Azure, eseguire questo comando.

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

La prima app Web Node.js è ora in esecuzione nel servizio app di Azure.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli [script dell'interfaccia della riga di comando per le app Web](app-service-cli-samples.md) già creati.

