---
title: Creare la prima app Web ASP.NET in Azure in cinque minuti | Microsoft Docs
description: Informazioni su come eseguire facilmente app Web nel servizio app distribuendo un&quot;app ASP.NET di esempio.
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: c55f844b89729b1ef5a6316b0b2731472074219e
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-aspnet-web-app-in-azure-in-five-minutes"></a>Creare la prima app Web ASP.NET in Azure in cinque minuti

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Questa guida introduttiva illustra come distribuire la prima app Web ASP.NET nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md) in pochi minuti.

Prima di iniziare, verificare che nel computer sia [installata l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## <a name="create-an-aspnet-web-app-in-azure"></a>Creare un'app Web ASP.NET in Azure
2. Accedere ad Azure eseguendo `az login` e attenendosi alle indicazioni visualizzate.
   
    ```azurecli
    az login
    ```
   
3. Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md). In tale gruppo si inseriranno tutte le risorse che si vogliono gestire insieme, ad esempio l'app Web e il back-end del database SQL.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    Per visualizzare i possibili valori utilizzabili per `---location`, usare il comando `az appservice list-locations` dell'interfaccia della riga di comando di Azure.

3. Creare un [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO". 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. Creare una nuova app Web con un nome univoco in `<app_name>`.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Distribuire un'app ASP.NET di esempio da GitHub.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git" --branch master --manual-integration 
    ```

5. Per osservare l'app in esecuzione in Azure, eseguire questo comando.

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

La prima app Web ASP.NET è ora in esecuzione nel servizio app di Azure.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli [script dell'interfaccia della riga di comando per le app Web](app-service-cli-samples.md) già creati.

