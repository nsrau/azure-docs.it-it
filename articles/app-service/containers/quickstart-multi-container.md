---
title: Creare un'app multi-contenitore (anteprima) nell'app Web per contenitori di Azure con una configurazione di Docker Compose
description: Distribuire la prima app multi-contenitore nell'app Web per contenitori di Azure in pochi minuti
keywords: servizio app di azure, app web, linux, docker, compose, multi-contenitore, contenitore, kubernetes
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: ec5c92415668c925fe360c0c8887fd792a121842
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753718"
---
# <a name="create-a-multicontainer-preview-app-using-web-app-for-containers"></a>Creare un'app multi-contenitore (anteprima) in un'app Web per contenitori

[App Web per contenitori](app-service-linux-intro.md) offre un modo flessibile per usare le immagini Docker. Questa guida introduttiva illustra come distribuire un'app multi-contenitore nell'app Web per contenitori in [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) con una configurazione di Docker Compose. Per Kubernetes seguire i passaggi di Kubernetes nell'[esercitazione del multi-contenitore](tutorial-multi-container-app.md).

Questa guida introduttiva verrà completata in Cloud Shell, ma gli stessi comandi possono essere eseguiti anche in locale con l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.32 o versioni successive). Questa guida introduttiva userà un file di configurazione di Docker Compose.

![App multi-contenitore di esempio in un'app Web per contenitori][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

Per questa guida introduttiva si userà il file compose di [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), ma modificato in modo da includere Database di Azure per MySQL, l'archiviazione permanente e Redis. I file di configurazione sono disponibili negli [Esempi di Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

In Cloud Shell creare una directory quickstart e passare ad essa.

```bash
mkdir quickstart

cd quickstart
```

Eseguire quindi il comando seguente per clonare il repository dell'app di esempio nella directory quickstart.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

In Cloud Shell creare un gruppo di risorse con il comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *South Central US*. Per visualizzare tutte le località supportate per il servizio app in Linux nel livello **Standard**, eseguire il comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

In genere, il gruppo di risorse e le risorse vengono create in un'area nelle vicinanze.

Al termine del comando, un output JSON mostra le proprietà del gruppo di risorse.

## <a name="create-an-azure-app-service-plan"></a>Creare un piano di servizio app di Azure

In Cloud Shell creare un piano di servizio app nel gruppo di risorse con il comando [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create).

L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` nel piano tariffario **Standard** (`--sku S1`) e in un contenitore Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Al termine della creazione del piano di servizio app, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Creare un'app Docker Compose

Nel terminale Cloud Shell passare alla directory `multicontainerwordpress`. Creare un'[app Web](app-service-linux-intro.md) multi-contenitore nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Non dimenticare di sostituire _\<app_name>_ con un nome univoco per l'app.

```bash
cd multicontainerwordpress

az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Dopo la creazione dell'app Web, l'interfaccia della riga di comando di Azure mostra un output simile all'esempio seguente:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app_name>.azurewebsites.net`. Il caricamento dell'app può richiedere alcuni minuti. Se si riceve un errore, attendere qualche minuto e quindi aggiornare il browser.

![App multi-contenitore di esempio in un'app Web per contenitori][1]

La creazione di un'app multi-contenitore in un'app Web per contenitori è stata **completata**.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app WordPress multi-contenitore in un'app Web per contenitori](tutorial-multi-container-app.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png