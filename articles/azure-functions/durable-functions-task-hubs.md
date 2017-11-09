---
title: "Hub attività in Funzioni permanenti - Azure"
description: "Informazioni sugli hub attività nell'estensione Funzioni permanenti per Funzioni di Azure. Informazioni su come configurare gli hub attività."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: b241bad7b0060551eba5e78efbb1b729bf5d0098
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hub attività in Funzioni permanenti (Funzioni di Azure)

Un *hub attività* in [Funzioni permanenti](durable-functions-overview.md) è un contenitore logico per le risorse di Archiviazione di Microsoft Azure che vengono usate per le orchestrazioni. Le funzioni attività e di orchestrazione possono interagire tra loro solo quando appartengono allo stesso hub attività.

Ogni app per le funzioni dispone di un hub attività distinto. Se più app per le funzioni condividono un account di archiviazione, tale account include più hub attività. Nel diagramma seguente è illustrato un hub attività per app per le funzioni in account di archiviazione condivisi e dedicati.

![Diagramma che illustra gli account di archiviazione condivisi e dedicati.](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Risorse di archiviazione di Azure

Un hub di attività è costituito dalle risorse di archiviazione seguenti: 

* Una o più code di controllo.
* Una coda di elementi di lavoro.
* Una tabella di cronologia.
* Un contenitore di archiviazione che contiene uno o più BLOB del lease.

Tutte le risorse vengono create automaticamente nell'account di Archiviazione di Azure predefinito quando vengono eseguite o sono pianificate per l'esecuzione funzioni attività o di orchestrazione. Nell'articolo [Prestazioni e scalabilità](durable-functions-perf-and-scale.md) viene descritto l'uso di queste risorse.

## <a name="task-hub-names"></a>Nomi degli hub attività

Gli hub attività sono identificati mediante un nome dichiarato nel file *host.json* file, come illustrato nell'esempio seguente:

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

I nomi degli hub attività devono iniziare con una lettera e contenere solo lettere e numeri. Se non specificato, il nome predefinito è **DurableFunctionsHub**.

> [!NOTE]
> Il nome è ciò che distingue un hub attività da un altro quando sono presenti più hub attività in un account di archiviazione condiviso. Se si dispone di più app per le funzioni che condividono lo stesso account di archiviazione condiviso, è necessario configurare nomi diversi per ogni hub attività nei file *host.json*.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come gestire il controllo delle versioni](durable-functions-versioning.md)
