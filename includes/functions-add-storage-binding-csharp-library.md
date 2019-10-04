---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592779"
---
In un progetto di libreria di classi C# i binding vengono definiti come attributi di binding nel metodo della funzione. Il file *function.json* viene quindi generato automaticamente in base a questi attributi.

Aprire il file di progetto *HttpTrigger.cs* e aggiungere l'istruzione `using` seguente:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Aggiungere il parametro seguente alla definizione del metodo `Run`:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

Il parametro `msg` è un tipo `ICollector<T>`, che rappresenta una raccolta di messaggi scritti in un binding di output al completamento della funzione. In questo caso, l'output è una coda di archiviazione denominata `outqueue`. La stringa di connessione per l'account di archiviazione è impostata da `StorageAccountAttribute`. Questo attributo indica l'impostazione che contiene la stringa di connessione dell'account di archiviazione e può essere applicato a livello di classe, metodo o parametro. In questo caso, è possibile omettere `StorageAccountAttribute`, perché si usa già l'account di archiviazione predefinito.

La definizione del metodo Run dovrà ora essere come indicato di seguito:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
