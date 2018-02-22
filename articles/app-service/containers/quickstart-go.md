---
title: Distribuire un'app Go in app Web per contenitori di Azure | Microsoft Docs
description: Come distribuire un'immagine Docker che esegue un'applicazione Go per app Web per contenitori di Azure.
keywords: Servizio app di Azure, app Web, go, Docker, contenitore
services: app-service
author: sptramer
manager: cfowler
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: sttramer
ms.custom: mvc
ms.openlocfilehash: dbe4d7bc6f5f1d83a079993c9616206fd82a1b9d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="deploy-a-go-app-in-azure-web-app-for-containers"></a>Distribuire un'app Go in app Web per contenitori di Azure

Il servizio app fornisce stack di applicazioni predefiniti in Linux con il supporto per versioni specifiche, ad esempio PHP 7.0 e Node.js 4.5. È anche possibile usare un'immagine Docker personalizzata per eseguire l'app Web in uno stack di applicazioni non ancora definito in Azure. Questa guida introduttiva illustra come usare un contenitore Docker esistente con un'applicazione Go ed eseguirlo nel Servizio app di Azure. Creare l'app Web usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-the-container"></a>Creare un'app Web per il contenitore

Creare un'[app Web](../app-service-web-overview.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Non dimenticare di sostituire `<app name>` con un nome univoco dell'app a livello globale.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/appservice-go-quickstart
```

Nel comando precedente `--deployment-container-image-name` fa riferimento all'immagine Docker Hub pubblica [microsoft/appservice-go-quickstart](https://hub.docker.com/r/microsoft/appservice-go-quickstart).

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

## <a name="get-data-from-the-apps-endpoint"></a>Ottenere dati dall'endpoint dell'app

Usando il comando `curl`, contattare l'endpoint dell'API REST fornito dall'applicazione del contenitore.

```bash
curl -X GET http://<app_name>.azurewebsites.net:8080/hello
```

```output
Hello world!
```

**Congratulazioni** È stata distribuita un'immagine Docker che esegue un'applicazione Go in App Web per contenitori.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare un'immagine Docker personalizzata](tutorial-custom-docker-image.md)
