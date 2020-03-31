---
title: Azure Blob storage trigger and bindings for Azure Functions
description: Informazioni su come usare il trigger di archiviazione BLOB di Azure e le associazioni in Funzioni di Azure.Learn to use the Azure Blob storage trigger and bindings in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277232"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Panoramica delle associazioni di archiviazione BLOB di Azure per Funzioni di AzureAzure Blob storage bindings for Azure Functions overview

Funzioni di Azure si integra con [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/) tramite trigger e [associazioni.](./functions-triggers-bindings.md) L'integrazione con l'archiviazione BLOB consente di compilare funzioni che reagiscono alle modifiche nei dati BLOB e ai valori di lettura e scrittura.

| Azione | Type |
|---------|---------|
| Eseguire una funzione come modifiche ai dati di archiviazione BLOBRun a function as blob storage data changes | [Grilletto](./functions-bindings-storage-blob-trigger.md) |
| Leggere i dati di archiviazione BLOB in una funzioneRead blob storage data in a function | [Associazione di input](./functions-bindings-storage-blob-input.md) |
| Consentire a una funzione di scrivere dati di archiviazione BLOBAllow a function to write blob storage data |[Associazione di output](./functions-bindings-storage-blob-output.md) |

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

- [Eseguire una funzione quando vengono modificati i dati di archiviazione BLOBRun a function when blob storage data changes](./functions-bindings-storage-blob-trigger.md)
- [Leggere i dati di archiviazione BLOB durante l'esecuzione di una funzioneRead blob storage data when a function runs](./functions-bindings-storage-blob-input.md)
- [Scrivere i dati di archiviazione BLOB da una funzioneWrite blob storage data from a function](./functions-bindings-storage-blob-output.md)
