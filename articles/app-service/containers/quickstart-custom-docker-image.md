---
title: Eseguire un'immagine hub Docker personalizzata nell'app Web per contenitori | Microsoft Docs
description: Come usare un'immagine Docker personalizzata per l'app Web per contenitori.
keywords: Servizio app di Azure, app Web, Linux, Docker, contenitore
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: c85f79cc14cdcecd2a05fc0ff91c4864b9fba277
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="run-a-custom-docker-hub-image-in-web-app-for-containers"></a>Eseguire un'immagine hub Docker personalizzata nell'app Web per contenitori

Il servizio app fornisce stack di applicazioni predefiniti in Linux con il supporto per versioni specifiche, ad esempio PHP 7.0 e Node.js 4.5. È anche possibile usare un'immagine Docker personalizzata per distribuire l'app Web in uno stack di applicazioni non ancora definito in Azure. Questa guida introduttiva mostra come creare un'app Web e distribuire un'immagine Docker basata su Python nell'app. Creare l'app Web usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Creare un'app Web per contenitori

Creare un'[app Web](../app-service-web-overview.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp#create). Non dimenticare di sostituire `<app name>` con un nome univoco dell'app.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name elnably/dockerimagetest
```

Nel comando precedente `--deployment-container-image-name` punta all'immagine Docker Hub pubblica [https://hub.docker.com/r/elnably/dockerimagetest/](https://hub.docker.com/r/elnably/dockerimagetest/). È possibile esaminarne il contenuto in [https://github.com/ahmedelnably/dockerimagetest](https://github.com/ahmedelnably/dockerimagetest).

Dopo la creazione dell'app Web, l'interfaccia della riga di comando di Azure mostra un output simile all'esempio seguente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Passare all'app

Passare all'URL seguente nel browser Web.

```bash
http://<app_name>.azurewebsites.net
```

![App di esempio in esecuzione in Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**Congratulazioni.** È stata distribuita un'immagine Docker in App Web per contenitori.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web Python Docker e PostgreSQL in Azure](tutorial-docker-python-postgresql-app.md)
