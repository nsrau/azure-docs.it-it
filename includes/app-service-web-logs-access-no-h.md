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
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905868"
---
Per accedere ai log della console generati dall'interno del codice dell'applicazione nel servizio app, attivare la registrazione diagnostica eseguendo il comando seguente nel [cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

I valori possibili per `--level` sono: `Error` ,, `Warning` `Info` e `Verbose` . Ogni livello successivo include il livello precedente. Ad esempio: `Error` include solo i messaggi di errore e `Verbose` include tutti i messaggi.

Dopo aver attivato la registrazione diagnostica, eseguire il comando seguente per visualizzare il flusso di log:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Se i log di console non sono immediatamente visibili, controllare nuovamente dopo 30 secondi.

> [!NOTE]
> È anche possibile esaminare i file di log nel browser all'indirizzo `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Per interrompere lo streaming dei log in qualsiasi momento, digitare `Ctrl`+`C`.
