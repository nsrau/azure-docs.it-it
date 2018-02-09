---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: d5dfe6ab6d53d450f7c75d376d630558ee03f698
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
Creare un'[app Web](../articles/app-service/app-service-web-overview.md) nel piano di servizio app `myAppServicePlan`

In Cloud Shell è possibile usare il comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Nell'esempio seguente sostituire *\<nome_app>* con un nome app univoco globale. I caratteri validi sono `a-z`, `0-9` e `-`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

Al termine della creazione dell'app Web, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

È stata creata un'app Web vuota, con la distribuzione Git abilitata.

> [!NOTE]
> L'URL dell'elemento Git remoto è riportato nella proprietà `deploymentLocalGitUrl`, con il formato `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Salvare questo URL, perché è necessario in un secondo momento.
>

Passare all'app Web appena creata.

```
http://<app_name>.azurewebsites.net
```

Ecco l'aspetto che avrà la nuova app Web:
