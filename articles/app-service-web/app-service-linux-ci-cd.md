---
title: Distribuzione continua dell&quot;hub Docker con App Web di Azure in Linux | Microsoft Docs
description: Come configurare la distribuzione continua in App Web di Azure in Linux.
keywords: Servizio app di Azure, Linux, OSS
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 68e11f07520bbe8da6812a34fe95708fc9e932ac
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="docker-hub-continuous-deployment-with-azure-web-app-on-linux"></a>Distribuzione continua dell'hub Docker con App Web di Azure in Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

In questa esercitazione si configura la distribuzione continua per un'immagine personalizzata del contenitore dall'[hub Docker](https://hub.docker.com).

## <a name="step-1---log-in-to-azure"></a>Passaggio 1: Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>Passaggio 2: Abilitare la distribuzione continua di hub Docker

Nel **[portale di Azure](https://portal.azure.com/)** fare clic sull'opzione **Servizio app** a sinistra nella pagina.

Fare clic sul nome dell'app per cui si desidera configurare la distribuzione continua dell'hub Docker.

In **Impostazioni app** aggiungere un'app denominata `DOCKER_ENABLE_CI` con il valore `true`.

![inserire l'immagine dell'impostazione dell'app](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---add-a-web-hook-to-docker-hub"></a>Passaggio 3: Aggiungere un webhook all'hub Docker

Nella pagina Hub Docker, fare clic su **Webhook**, quindi **CREATE A WEBHOOK** (CREA UN WEBHOOK).

![inserire l'immagine dell'aggiunta del webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Per l'URL del Webhook, è necessario avere l'endpoint seguente: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

![inserire l'immagine dell'aggiunta del webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

È possibile ottenere `publishingusername` e `publishingpwd` scaricando il profilo di pubblicazione dell'app Web tramite il portale di Azure.

![inserire l'immagine dell'aggiunta del webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

Quando l'immagine viene aggiornata, l'app Web viene aggiornata automaticamente con la nuova immagine.

## <a name="next-steps"></a>Passaggi successivi
* [Definizione di App Web di Azure in Linux](./app-service-linux-intro.md)
* [Creazione di app in App Web di Azure in Linux](./app-service-linux-how-to-create-web-app.md)
* [Uso della configurazione PM2 per Node.js in App Web su Linux](app-service-linux-using-nodejs-pm2.md)
* [Uso di .NET Core in App Web di Azure in Linux](app-service-linux-using-dotnetcore.md)
* [Uso di Ruby in App Web di Azure in Linux](app-service-linux-ruby-get-started.md)
* [Come usare un'immagine Docker personalizzata per App Web di Azure in Linux](./app-service-linux-using-custom-docker-image.md)
* [Domande frequenti su App Web del Servizio app di Azure su Linux](./app-service-linux-faq.md) 




