---
title: includere file
description: includere file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e2c5794e5ce6e23b60bff513562f69c9333d6e34
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743806"
---
È possibile accedere ai log della console generati dall'interno del contenitore.

Prima di tutto attivare la registrazione del contenitore eseguendo questo comando:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Sostituire `<app-name>` e `<resource-group-name>` con i valori appropriati per l'app Web.

Dopo che la registrazione del contenitore è attivata, eseguire il comando seguente per visualizzare il flusso di registrazione:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Se i log di console non sono immediatamente visibili, controllare nuovamente dopo 30 secondi.

Per interrompere lo streaming dei log in qualsiasi momento, premere **CTRL**+**C** .

È anche possibile ispezionare i file di log in un browser all'indirizzo `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
