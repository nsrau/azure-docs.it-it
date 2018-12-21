---
title: Creare app multi-contenitore tramite Docker Compose - Servizio App di Azure
description: Distribuire la prima app multi-contenitore nell'app Web per contenitori di Azure in pochi minuti
keywords: servizio app di azure, app web, linux, docker, compose, multicontenitore, multi-contenitore, app web per contenitori, più contenitori, contenitore, kubernetes, wordpress, db azure per mysql, database di produzione con contenitori
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
ms.custom: seodec18
ms.openlocfilehash: 404be7a064e0e4b09178830b7802192e8775e121
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316581"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Creare un'app multi-contenitore (anteprima) con una configurazione di Docker Compose

[App Web per contenitori](app-service-linux-intro.md) offre un modo flessibile per usare le immagini Docker. Questa guida introduttiva illustra come distribuire un'app multi-contenitore nell'app Web per contenitori in [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) con una configurazione di Docker Compose. Per Kubernetes e una soluzione completa end-to-end usando il database di Azure per MySQL, seguire l'[esercitazione sui multi-contenitori](tutorial-multi-container-app.md).

Questa guida introduttiva verrà completata in Cloud Shell, ma gli stessi comandi possono essere eseguiti anche in locale con l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.32 o versioni successive). 

![App multi-contenitore di esempio in App Web per contenitori][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

Per questa guida introduttiva, usare il file Compose da [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). Il file di configurazione è disponibile negli [Esempi di Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

In Cloud Shell creare una directory quickstart e passare ad essa.

```bash
mkdir quickstart

cd quickstart
```

Eseguire quindi il comando seguente per clonare il repository dell'app di esempio nella directory quickstart. Quindi, passare alla directory `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

In Cloud Shell creare un gruppo di risorse con il comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *South Central US*. Per visualizzare tutte le località supportate per il servizio app in Linux nel livello **Standard**, eseguire il comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

In genere, il gruppo di risorse e le risorse vengono create in un'area vicina alla località dell'utente.

Al termine del comando, un output JSON visualizza le proprietà del gruppo di risorse.

## <a name="create-an-azure-app-service-plan"></a>Creare un piano di servizio app di Azure

In Cloud Shell creare un piano di servizio app nel gruppo di risorse con il comando [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).

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

Nel terminale Cloud Shell creare un'[app Web](app-service-linux-intro.md) multi-contenitore nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Non dimenticare di sostituire _\<app_name>_ con un nome univoco per l'app.

```bash
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

![App multi-contenitore di esempio in App Web per contenitori][1]

La creazione di un'app multi-contenitore in App Web per contenitori è stata **completata**.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app WordPress multi-contenitore in un'app Web per contenitori](tutorial-multi-container-app.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png