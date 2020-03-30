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
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Trigger e associazioni di database Cosmos di Azure per la panoramica di Funzioni di Azure 2.xAzure Cosmos DB trigger and bindings for Azure Functions 2.x overview

> [!div class="op_single_selector" title1="Selezionare la versione del runtime di Funzioni di Azure in uso: "]
> * [Versione 1](functions-bindings-cosmosdb.md)
> * [Versione 2](functions-bindings-cosmosdb-v2.md)

Questo set di articoli illustra come usare le associazioni del database Cosmos di Azure in Funzioni di Azure 2.x.This set of articles explains how to work with [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) bindings in Azure Functions 2.x. Funzioni di Azure supporta i trigger e le associazioni di input e output per Azure Cosmos DB.

| Azione | Type |
|---------|---------|
| Eseguire una funzione quando viene creato o modificato un documento di Azure Cosmos DBRun a function when an Azure Cosmos DB document is created or modified | [Grilletto](./functions-bindings-cosmosdb-v2-trigger.md) |
| Leggere un documento di Azure Cosmos DBRead an Azure Cosmos DB document | [Associazione di input](./functions-bindings-cosmosdb-v2-input.md) |
| Salvare le modifiche apportate a un documento di Azure Cosmos DBSave changes to an Azure Cosmos DB document  |[Associazione di output](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Questo riferimento è per Funzioni di [Azure versione 2.x](functions-versions.md).  Per informazioni su come usare le associazioni in Funzioni 1.x, vedere [Associazioni di Azure Cosmos DB per Funzioni di Azure 1.x](functions-bindings-cosmosdb.md).
>
> Questa associazione è stata originariamente denominata DocumentDB. In Funzioni versione 2.x, i trigger, le associazioni e il pacchetto sono tutti denominati Cosmos DB.

## <a name="supported-apis"></a>API supportate

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Aggiungi all'app Funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2.x e successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene utilizzato per le librerie di classi .NET mentre il pacchetto di estensione viene utilizzato per tutti gli altri tipi di applicazione.

| Linguaggio                                        | Aggiungi per...                                   | Osservazioni 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet], versione 3.x | |
| Script di C, Java, JavaScript, Python, PowerShell | Registrazione del pacchetto di [estensione]          | L'estensione Strumenti di Azure è consigliata per l'uso con il codice di Visual Studio.The [Azure Tools extension] is recommended to use with Visual Studio Code. |
| Script di C '(solo online nel portale di Azure)C's Script (online-only in Azure portal)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di associazione esistenti senza dover ripubblicare l'app per le funzioni, vedere [Aggiornare le estensioni.] |

[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[pacchetto di estensione]: ./functions-bindings-register.md#extension-bundles
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Estensione Strumenti di AzureAzure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Funzioni 1.x applicazioni hanno automaticamente un riferimento il [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet pacchetto, versione 2.x.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione quando viene creato o modificato un documento di Azure Cosmos DB (Trigger)Run a function when an Azure Cosmos DB document is created or modified (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Leggere un documento di Azure Cosmos DB (associazione di input)Read an Azure Cosmos DB document (Input binding)](./functions-bindings-cosmosdb-v2-input.md)
- [Salvare le modifiche apportate a un documento di Azure Cosmos DB (associazione di output)Save changes to an Azure Cosmos DB document (Output binding)](./functions-bindings-cosmosdb-v2-output.md)
