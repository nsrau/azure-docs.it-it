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
ms.openlocfilehash: 313daf1c105caa8569ed43e59d9e18f184599214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hub attività in Funzioni permanenti (Funzioni di Azure)

Un *hub attività* per [Funzioni permanenti](durable-functions-overview.md) è un contenitore logico per le orchestrazioni e le attività nel contesto di un singolo account di archiviazione di Azure. Nello stesso hub attività possono essere presenti più funzioni e app per le funzioni e spesso l'hub attività funge anche da contenitore di applicazioni.

Non occorre creare gli hub attività in modo esplicito. Vengono inizializzati automaticamente dal runtime, usando un nome dichiarato nel file *host.json*. Ogni hub attività ha un proprio set di code di archiviazione, tabelle e BLOB all'interno di un singolo account di archiviazione. Tutte le app per le funzioni eseguite in uno specifico hub attività condividono le stesse risorse di archiviazione. Le funzioni attività e di orchestrazione possono interagire tra loro solo quando appartengono allo stesso hub attività.

## <a name="configuring-a-task-hub-in-hostjson"></a>Configurazione di un hub di attività in host.json

Il nome di un hub attività può essere configurato nel file *host.json* di un'app per le funzioni.

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

I nomi degli hub attività devono iniziare con una lettera e contenere solo lettere e numeri. Se non specificato, il nome dell'hub attività predefinito per un'app per le funzioni è **DurableFunctionsHub**.

> [!NOTE]
> Se si hanno più app per le funzioni che condividono un account di archiviazione, è consigliabile configurare un nome di hub attività diverso per ognuna di esse. Ciò garantisce che ogni app per le funzioni sia correttamente isolata dalle altre.

## <a name="azure-storage-resources"></a>Risorse di archiviazione di Azure

Un hub di attività è costituito da svariate risorse di Archiviazione di Azure:

* Una o più code di controllo.
* Una coda di elementi di lavoro.
* Una tabella di cronologia.
* Un contenitore di archiviazione che contiene uno o più BLOB del lease.

Tutte le risorse vengono create automaticamente nell'account di Archiviazione di Azure predefinito quando vengono eseguite o sono pianificate per l'esecuzione funzioni attività o di orchestrazione. L'articolo [Prestazioni e scalabilità](durable-functions-perf-and-scale.md) illustra l'uso di queste risorse.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come gestire il controllo delle versioni](durable-functions-versioning.md)

