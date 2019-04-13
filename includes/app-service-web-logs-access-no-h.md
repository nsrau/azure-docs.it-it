---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551218"
---
È possibile accedere i registri della console generati da all'interno del contenitore. Innanzitutto, abilitare registrazione del contenitore eseguendo il comando seguente in Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Dopo che la registrazione del contenitore è attivata, eseguire il comando seguente per visualizzare il flusso di registrazione:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Se i log di console non sono immediatamente visibili, controllare nuovamente dopo 30 secondi.

> [!NOTE]
> È anche possibile esaminare i file di log dal browser a `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Per arrestare lo streaming dei log in qualsiasi momento, digitare `Ctrl` + `C`.
