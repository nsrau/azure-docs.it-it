---
title: Trigger e associazioni di archiviazione code di Azure per la Panoramica di funzioni di Azure
description: Informazioni su come usare il trigger di archiviazione code di Azure e l'associazione di output in Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87055796"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Trigger e associazioni di archiviazione code di Azure per la Panoramica di funzioni di Azure

Funzioni di Azure può essere eseguito quando vengono creati nuovi messaggi di archiviazione code di Azure e possono scrivere messaggi di Accodamento all'interno di una funzione.

| Operazione | Tipo |
|---------|---------|
| Eseguire una funzione come modifiche ai dati di archiviazione di Accodamento | [Trigger](./functions-bindings-storage-queue-trigger.md) |
| Scrivi messaggi di archiviazione code |[Binding di output](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2.x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre il bundle di estensione viene usato per tutti gli altri tipi di applicazioni.

| Linguaggio                                        | Aggiungi da...                                   | Osservazioni 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) è consigliata per l'uso con Visual Studio Code. |
| Script C# (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Le app di funzioni 1. x hanno automaticamente un riferimento al pacchetto NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione come modifiche ai dati di archiviazione di Accodamento (trigger)](./functions-bindings-storage-queue-trigger.md)
- [Scrivi messaggi di archiviazione code (associazione di output)](./functions-bindings-storage-queue-output.md)
