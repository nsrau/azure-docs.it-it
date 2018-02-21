---
title: Distribuzione continua da un registro contenitori Docker con l'app Web per contenitori | Microsoft Docs
description: Come configurare la distribuzione continua da un registro contenitori Docker con l'app Web per contenitori.
keywords: servizio app di azure, linux, docker, acr,oss
services: app-service
documentationcenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: cccbd4952c66d3d8140e2a03e3b76afaa5ba3fbf
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Distribuzione continua con l'app Web per contenitori

In questa esercitazione si configura la distribuzione continua per un'immagine personalizzata del contenitore da repository del [Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/) o dall'[hub Docker](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com)

## <a name="enable-container-continuous-deployment-feature"></a>Abilitare la funzionalità di distribuzione continua dei contenitori

È possibile abilitare la funzionalità di distribuzione continua tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) ed eseguendo il comando seguente

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

Nel **[portale di Azure](https://portal.azure.com/)** fare clic sull'opzione **Servizio app** a sinistra nella pagina.

Fare clic sul nome dell'app per cui si desidera configurare la distribuzione continua dell'hub Docker.

In **Impostazioni app** aggiungere un'app denominata `DOCKER_ENABLE_CI` con il valore `true`.

![inserire l'immagine dell'impostazione dell'app](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-webhook-url"></a>Preparare l'URL webhook

È possibile ottenere l'URL Webhook tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) ed eseguendo il comando seguente

```azurecli-interactive
az webapp deployment container show-cd-url -n sname1 -g rgname
```

Per l'URL del Webhook, è necessario avere l'endpoint seguente: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

È possibile ottenere `publishingusername` e `publishingpwd` scaricando il profilo di pubblicazione dell'app Web tramite il portale di Azure.

![inserire l'immagine dell'aggiunta del webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-web-hook"></a>Aggiungere un webhook

### <a name="azure-container-registry"></a>Registro di sistema del contenitore di Azure

Nel pannello del portale del registro fare clic su **Webhook** e creare un nuovo webhook facendo clic su **Aggiungi**. Nel pannello **Create webhook** (Crea webhook) assegnare un nome al webhook. Per l'URI del webhook è necessario specificare l'URL ottenuto dal **passaggio 3**

Assicurarsi di definire come ambito il repository che contiene l'immagine del contenitore.

![inserimento dell'immagine del webhhok](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Quando l'immagine viene aggiornata, l'app Web viene aggiornata automaticamente con la nuova immagine.

### <a name="docker-hub"></a>Hub Docker

Nella pagina Hub Docker, fare clic su **Webhook**, quindi **CREATE A WEBHOOK** (CREA UN WEBHOOK).

![inserire l'immagine dell'aggiunta del webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Per l'URL del webhook è necessario specificare l'URL ottenuto dal **passaggio 3**

![inserire l'immagine dell'aggiunta del webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Quando l'immagine viene aggiornata, l'app Web viene aggiornata automaticamente con la nuova immagine.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è il Servizio app di Azure in Linux?](./app-service-linux-intro.md)
* [Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/)
* [Uso di .NET Core nel Servizio app di Azure in Linux](quickstart-dotnetcore.md)
* [Uso di Ruby in Servizio app di Azure in Linux](quickstart-ruby.md)
* [Come usare un'immagine Docker personalizzata per l'app Web per contenitori](quickstart-custom-docker-image.md)
* [Azure App Service Web App for Containers FAQ (Domande frequenti sulle app Web per contenitori del servizio app di Azure)](./app-service-linux-faq.md)
* [Gestire l'app Web per i contenitori tramite l'interfaccia della riga di comando di Azure 2.0](./app-service-linux-cli.md)