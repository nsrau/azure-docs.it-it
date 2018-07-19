---
title: Distribuzione continua da un registro contenitori Docker con l'app Web per contenitori - Azure | Microsoft Docs
description: Come configurare la distribuzione continua da un registro contenitori Docker con l'app Web per contenitori.
keywords: servizio app di azure, linux, docker, acr,oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 0f2d4626308eed376b71f1b3df2f9e43f1b2a4f7
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130966"
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

Prendere nota dell'URI del webhook. Sarà necessario nella sezione successiva.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

È possibile ottenere `publishingusername` e `publishingpwd` scaricando il profilo di pubblicazione dell'app Web tramite il portale di Azure.

![Schermata di aggiunta webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Aggiunta di un webhook

Per aggiungere un webhook, seguire la procedura descritta in queste guide:

- Uso dei webhook del [Registro contenitori di Azure](../../container-registry/container-registry-webhook.md)
- [Webhooks per l'hub Docker](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione al Servizio app di Azure in Linux](./app-service-linux-intro.md)
* [Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/)
* [Creare un'app Web .NET nel Servizio app in Linux](quickstart-dotnetcore.md)
* [Creare un'app Web Ruby nel servizio app in Linux](quickstart-ruby.md)
* [Distribuire un'app Web Docker/Go in app Web per contenitori](quickstart-docker-go.md)
* [Domande frequenti sul Servizio app di Azure in Linux](./app-service-linux-faq.md)
* [Gestire l'app Web per contenitori tramite l'interfaccia della riga di comando di Azure](./app-service-linux-cli.md)
