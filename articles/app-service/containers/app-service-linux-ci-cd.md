---
title: CI/CD per contenitori Linux personalizzati
description: Informazioni su come configurare la distribuzione continua in un contenitore Linux personalizzato nel servizio app di Azure.Learn how to set up continuous deployment to a custom Linux container in Azure App Service. La distribuzione continua è supportata per Docker Hub e ACR.
keywords: servizio app di azure, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687626"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Distribuzione continua con l'app Web per contenitori

In questa esercitazione verrà configurata la distribuzione continua per un'immagine personalizzata del contenitore da repository gestite del [Registro Azure Container](https://azure.microsoft.com/services/container-registry/) o dall'[hub Docker](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Abilitare la distribuzione continua con il Registro Azure Container

![Screenshot del webhook di Registro Azure Container](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare l'opzione **Servizio app** a sinistra nella pagina.
3. Selezionare il nome dell'app per cui si desidera configurare la distribuzione continua.
4. Nella pagina **Impostazioni contenitore** selezionare **Contenitore singolo**
5. Selezionare **Registro Azure Container**
6. Selezionare **Distribuzione continua > On**
7. Selezionare **Salva** per abilitare la distribuzione continua.

## <a name="use-the-acr-webhook"></a>Usare il webhook di Registro Azure Container

Quando la distribuzione continua viene abilitata, è possibile visualizzare il webhook appena creato nella pagina dei webhook di Registro Azure Container.

![Screenshot del webhook di Registro Azure Container](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

Nel Registro contenitori fare clic su Webhook per visualizzare i webhook correnti.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Abilitare la distribuzione continua con l'hub Docker (facoltativo)

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare l'opzione **Servizio app** a sinistra nella pagina.
3. Selezionare il nome dell'app per cui si desidera configurare la distribuzione continua.
4. Nella pagina **Impostazioni contenitore** selezionare **Contenitore singolo**
5. Selezionare **Hub Docker**
6. Selezionare **Distribuzione continua > On**
7. Selezionare **Salva** per abilitare la distribuzione continua.

![Schermata dell'impostazione app](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Copiare l'URL webhook. Per aggiungere un webhook per l'hub Docker, vedere <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Webhook per l'hub Docker</a>.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione al Servizio app di Azure in Linux](./app-service-linux-intro.md)
* [Registro di sistema del contenitore di AzureAzure Container](https://azure.microsoft.com/services/container-registry/)
* [Creare un'app Web .NET Core nel Servizio app in Linux](quickstart-dotnetcore.md)
* [Creare un'app Web Ruby nel servizio app in Linux](quickstart-ruby.md)
* [Distribuire un'app Web Docker/Go in app Web per contenitori](quickstart-docker-go.md)
* [Domande frequenti sul Servizio app di Azure in Linux](./app-service-linux-faq.md)
* [Gestire l'app Web per i contenitori tramite l'interfaccia della riga di comando di Azure](./app-service-linux-cli.md)
