---
title: Distribuzione continua con App Web di Azure in Linux | Microsoft Docs
description: Come configurare la distribuzione continua in App Web di Azure in Linux.
keywords: servizio app di azure, linux, oss, acr
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: f8f7d51003f8a55b7f51e8cc2cea838e8e5a6196
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="continuous-deployment-with-azure-web-app-on-linux"></a>Distribuzione continua con App Web di Azure in Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

In questa esercitazione si configura la distribuzione continua per un'immagine personalizzata del contenitore da repository del [Registro contenitori di Azure](https://azure.microsoft.com/en-us/services/container-registry/) o dall'[hub Docker](https://hub.docker.com).

## <a name="step-1---sign-in-to-azure"></a>Passaggio 1 - Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com

## <a name="step-2---enable-container-continuous-deployment-feature"></a>Passaggio 2: Abilitare la funzionalità di distribuzione continua del contenitore

È possibile abilitare la funzionalità di distribuzione continua tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) ed eseguendo il comando seguente

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
``` 

Nel **[portale di Azure](https://portal.azure.com/)** fare clic sull'opzione **Servizio app** a sinistra nella pagina.

Fare clic sul nome dell'app per cui si desidera configurare la distribuzione continua dell'hub Docker.

In **Impostazioni app** aggiungere un'app denominata `DOCKER_ENABLE_CI` con il valore `true`.

![inserire l'immagine dell'impostazione dell'app](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>Passaggio 3 - Preparare l'URL webhook

È possibile ottenere l'URL Webhook tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) ed eseguendo il comando seguente

```azurecli-interactive
az webapp deployment container -n sname1 -g rgname -e true --show-cd-url
``` 

Per l'URL del Webhook, è necessario avere l'endpoint seguente: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

È possibile ottenere `publishingusername` e `publishingpwd` scaricando il profilo di pubblicazione dell'app Web tramite il portale di Azure.

![inserire l'immagine dell'aggiunta del webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>Passaggio 4 - Aggiungere un webhook

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
* [Definizione di App Web di Azure in Linux](./app-service-linux-intro.md)
* [Registro contenitori di Azure](https://azure.microsoft.com/en-us/services/container-registry/)
* [Uso della configurazione PM2 per Node.js in App Web su Linux](app-service-linux-using-nodejs-pm2.md)
* [Uso di .NET Core in App Web di Azure in Linux](app-service-linux-using-dotnetcore.md)
* [Uso di Ruby in App Web di Azure in Linux](app-service-linux-ruby-get-started.md)
* [Come usare un'immagine Docker personalizzata per App Web di Azure in Linux](./app-service-linux-using-custom-docker-image.md)
* [Domande frequenti su App Web del Servizio app di Azure su Linux](./app-service-linux-faq.md) 
* [Gestire App Web in Linux tramite interfaccia della riga di comando di Azure 2.0](./app-service-linux-cli.md)




