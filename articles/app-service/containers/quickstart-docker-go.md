---
title: Creare un'app Docker/Go su Linux - Servizio app di Azure
description: Come distribuire un'immagine Docker che esegue un'applicazione Go in app Web per contenitori di Azure.
keywords: Servizio app di Azure, app Web, go, Docker, contenitore
services: app-service
author: msangapu
manager: jeconnoc
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 88c9996ce3f2d89ae58881c913f6bd4e549b5814
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547305"
---
# <a name="run-a-custom-linux-container-in-azure-app-service"></a>Eseguire un contenitore Linux personalizzato nel servizio app di Azure

Il [servizio app Linux](app-service-linux-intro.md) fornisce stack di applicazioni predefiniti in Linux con il supporto per linguaggi quali .NET, PHP, Node.js e altri ancora. È anche possibile usare un'immagine Docker personalizzata per eseguire l'app Web in uno stack di applicazioni non ancora definito in Azure. Questa guida introduttiva mostra come creare un'app Web e distribuire un'immagine Go da Docker Hub. Creare l'app Web usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![App di esempio in esecuzione in Azure](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Creare un'app Web

Creare un'[app Web](../overview.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Non dimenticare di sostituire `<app name>` con un nome univoco dell'app a livello globale.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

Nel comando precedente `--deployment-container-image-name` fa riferimento all'immagine Docker Hub pubblica [microsoft/azure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/).

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

```bash
http://<app_name>.azurewebsites.net/hello
```

![App di esempio in esecuzione in Azure](media/quickstart-docker-go/hello-world-in-browser.png)

**Congratulazioni** È stata distribuita un'immagine Docker che esegue un'applicazione Go in app Web per contenitori.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire la distribuzione da un repository di contenitore privato](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Configurare un contenitore personalizzato](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Esercitazione: App WordPress multicontenitore](tutorial-multi-container-app.md)
