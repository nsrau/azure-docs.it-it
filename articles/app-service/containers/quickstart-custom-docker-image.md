---
title: Eseguire un'immagine Docker Hub personalizzata in App Web di Azure per contenitori | Microsoft Docs
description: Come usare un'immagine Docker personalizzata per App Web di Azure per contenitori.
keywords: Servizio app di Azure, app Web, Linux, Docker, contenitore
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: cephalin;wesmc
ms.custom: mvc
ms.openlocfilehash: a95a8435e4ecef201ad0f6d9ecda68e94f06ea80
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="run-a-custom-docker-hub-image-in-azure-web-app-for-containers"></a>Eseguire un'immagine Docker Hub personalizzata in App Web di Azure per contenitori

Il servizio app fornisce stack di applicazioni predefiniti in Linux con il supporto per versioni specifiche, ad esempio PHP 7.0 e Node.js 4.5. È anche possibile usare un'immagine Docker personalizzata per eseguire l'app Web in uno stack di applicazioni non ancora definito in Azure. Questa guida introduttiva mostra come creare un'app Web e distribuire l'[immagine Docker Nginx ufficiale](https://hub.docker.com/r/_/nginx/) nell'app. Creare l'app Web usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![App di esempio in esecuzione in Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Creare un'app Web per contenitori

Creare un'[app Web](../app-service-web-overview.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Non dimenticare di sostituire `<app name>` con un nome univoco dell'app.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name nginx
```

Nel comando precedente `--deployment-container-image-name` punta all'immagine Docker Hub pubblica [https://hub.docker.com/r/_/nginx/](https://hub.docker.com/r/_/nginx/).

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

**Congratulazioni** È stata distribuita un'immagine Docker in App Web per contenitori.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare un'immagine Docker personalizzata](tutorial-custom-docker-image.md)
