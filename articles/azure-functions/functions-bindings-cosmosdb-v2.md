---
title: Associazioni di Azure Cosmos DB per Funzioni 2.x
description: Informazioni su come usare trigger e associazioni di Azure Cosmos DB in Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605767"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Trigger e associazioni di Azure Cosmos DB per la Panoramica di funzioni di Azure 2. x

> [!div class="op_single_selector" title1="Selezionare la versione del runtime di funzioni di Azure in uso: "]
> * [Versione 1](functions-bindings-cosmosdb.md)
> * [Versione 2](functions-bindings-cosmosdb-v2.md)

Questo set di articoli illustra come usare le associazioni di [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) in funzioni di Azure 2. x. Funzioni di Azure supporta i trigger e le associazioni di input e output per Azure Cosmos DB.

| Azione | Type |
|---------|---------|
| Esegue una funzione quando viene creato o modificato un documento di Azure Cosmos DB | [Trigger](./functions-bindings-cosmosdb-v2-trigger.md) |
| Leggi un documento Azure Cosmos DB | [Binding di input](./functions-bindings-cosmosdb-v2-input.md) |
| Salvare le modifiche apportate a un documento Azure Cosmos DB  |[Binding di output](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Questo riferimento è per le [funzioni di Azure versione 2. x](functions-versions.md).  Per informazioni su come usare le associazioni in Funzioni 1.x, vedere [Associazioni di Azure Cosmos DB per Funzioni di Azure 1.x](functions-bindings-cosmosdb.md).
>
> Questa associazione è stata originariamente denominata DocumentDB. In Funzioni versione 2.x, i trigger, le associazioni e il pacchetto sono tutti denominati Cosmos DB.

## <a name="supported-apis"></a>API supportate

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2. x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre il bundle di estensione viene usato per tutti gli altri tipi di applicazioni.

| Linguaggio                                        | Aggiungi da...                                   | Osservazioni 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure] è consigliata per l'uso con Visual Studio Code. |
| Script C# (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Estensione degli strumenti di Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Le app di funzioni 1. x hanno automaticamente un riferimento al pacchetto NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2. x.

## <a name="next-steps"></a>Passaggi successivi

- [Esegui una funzione quando viene creato o modificato un documento Azure Cosmos DB (trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Leggere un documento di Azure Cosmos DB (associazione di input)](./functions-bindings-cosmosdb-v2-input.md)
- [Salvare le modifiche apportate a un documento Azure Cosmos DB (associazione di output)](./functions-bindings-cosmosdb-v2-output.md)
