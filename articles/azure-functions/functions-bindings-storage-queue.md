---
title: Panoramica del trigger e delle associazioni di Archiviazione coda di Azure per Funzioni di AzureAzure Queue storage trigger and bindings for Azure Functions overview
description: Informazioni su come usare il trigger di archiviazione code di Azure e l'associazione di output in Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277310"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Panoramica del trigger e delle associazioni di Archiviazione coda di Azure per Funzioni di AzureAzure Queue storage trigger and bindings for Azure Functions overview

Funzioni di Azure possono essere eseguite quando vengono creati nuovi messaggi di archiviazione della coda di Azure e possono scrivere i messaggi della coda all'interno di una funzione.

| Azione | Type |
|---------|---------|
| Eseguire una funzione quando vengono modificate le modifiche ai dati di archiviazione della codaRun a function as queue storage data changes | [Grilletto](./functions-bindings-storage-queue-trigger.md) |
| Scrivere i messaggi di archiviazione della coda |[Associazione di output](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Aggiungi all'app Funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2.x e successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene utilizzato per le librerie di classi .NET mentre il pacchetto di estensione viene utilizzato per tutti gli altri tipi di applicazione.

| Linguaggio                                        | Aggiungi per...                                   | Osservazioni 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet], versione 3.x | |
| Script di C, Java, JavaScript, Python, PowerShell | Registrazione del pacchetto di [estensione]          | L'estensione Strumenti di Azure è consigliata per l'uso con il codice di Visual Studio.The [Azure Tools extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) is recommended to use with Visual Studio Code. |
| Script di C '(solo online nel portale di Azure)C's Script (online-only in Azure portal)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di associazione esistenti senza dover ripubblicare l'app per le funzioni, vedere [Aggiornare le estensioni.] |

[core tools]: ./functions-run-local.md
[pacchetto di estensione]: ./functions-bindings-register.md#extension-bundles
[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Funzioni 1.x applicazioni hanno automaticamente un riferimento il [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet pacchetto, versione 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione come modifiche ai dati di archiviazione della coda (Trigger)Run a function as queue storage data changes (Trigger)](./functions-bindings-storage-queue-trigger.md)
- [Scrivere i messaggi di archiviazione della coda (associazione di output)Write queue storage messages (Output binding)](./functions-bindings-storage-queue-output.md)
