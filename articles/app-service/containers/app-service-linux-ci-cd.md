---
title: Distribuzione continua da un registro contenitori Docker con l'app Web per contenitori - Azure | Microsoft Docs
description: Come configurare la distribuzione continua da un registro contenitori Docker con l'app Web per contenitori.
keywords: servizio app di azure, linux, docker, acr,oss
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: ac35dbd041de50ab8aae1a0fb4c00fe3917a7297
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Distribuzione continua con l'app Web per contenitori

In questa esercitazione verrà configurata la distribuzione continua per un'immagine personalizzata del contenitore da repository gestite del [Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/) o dall'[hub Docker](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Abilitare la funzionalità di distribuzione continua

Per abilitare la funzionalità di distribuzione continua, usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) ed eseguire il comando seguente:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

Nel [portale di Azure](https://portal.azure.com/), selezionare l'opzione **Servizio app** a sinistra nella pagina.

Selezionare il nome dell'app per cui si desidera configurare la distribuzione continua dell'hub Docker.

Nella pagina **Contenitore Docker**, selezionare **On** (Attiva), quindi premere **Salva** per abilitare la distribuzione continua.

![Schermata dell'impostazione app](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Preparare l'URL webhook

Ottenere l'URL webhook tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) ed eseguire il comando seguente:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Per l'URL webhook è necessario disporre dell'endpoint seguente: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

È possibile ottenere `publishingusername` e `publishingpwd` scaricando il profilo di pubblicazione dell'app Web tramite il portale di Azure.

![Schermata di aggiunta webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Aggiunta di un webhook

### <a name="azure-container-registry"></a>Registro di sistema del contenitore di Azure

1. Nella pagina del portale del registro di sistema, selezionare **Webhooks**.
2. Per creare un nuovo webhook, selezionare **Aggiungi**. 
3. Nel riquadro **Crea webhook**, assegnare un nome al webhook. Per l'uni del webhook, specificare l'URL ottenuto nella sezione precedente.

Assicurarsi di definire come ambito il repository che contiene l'immagine del contenitore.

![Schermata del webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Quando l'immagine viene aggiornata, l'app Web viene aggiornata automaticamente con la nuova immagine.

### <a name="docker-hub"></a>Hub Docker

Nella pagina Hub Docker, selezionare **Webhooks**, quindi **CREATE A WEBHOOK** (CREA UN WEBHOOK).

![Schermata di aggiunta webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Per l'URL del webhook, fornire l'URL ottenuto in precedenza.

![Schermata di aggiunta webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Quando l'immagine viene aggiornata, l'app Web viene aggiornata automaticamente con la nuova immagine.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione al Servizio app di Azure in Linux](./app-service-linux-intro.md)
* [Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/)
* [Creare un'app Web .NET nel Servizio app in Linux](quickstart-dotnetcore.md)
* [Creare un'app Web Ruby nel servizio app in Linux](quickstart-ruby.md)
* [Distribuire un'app Web Docker/Go in app Web per contenitori](quickstart-docker-go.md)
* [Domande frequenti sul Servizio app di Azure in Linux](./app-service-linux-faq.md)
* [Gestire l'app Web per contenitori tramite l'interfaccia della riga di comando di Azure](./app-service-linux-cli.md)
