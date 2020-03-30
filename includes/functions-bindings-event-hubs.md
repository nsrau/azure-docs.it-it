---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 47e9e37676a4afa9ec29393bf970c368f3f9e5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586130"
---
## <a name="add-to-your-functions-app"></a>Aggiungi all'app Funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2.x e successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene utilizzato per le librerie di classi .NET mentre il pacchetto di estensione viene utilizzato per tutti gli altri tipi di applicazione.

| Linguaggio                                        | Aggiungi per...                                   | Osservazioni 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet], versione 3.x | |
| Script di C, Java, JavaScript, Python, PowerShell | Registrazione del pacchetto di [estensione]          | L'estensione Strumenti di Azure è consigliata per l'uso con il codice di Visual Studio.The [Azure Tools extension] is recommended to use with Visual Studio Code. |
| Script di C '(solo online nel portale di Azure)C's Script (online-only in Azure portal)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di associazione esistenti senza dover ripubblicare l'app per le funzioni, vedere [Aggiornare le estensioni.] |

[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[pacchetto di estensione]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Aggiornare le estensioni]: ../articles/azure-functions/install-update-binding-extensions-manual.md
[Estensione Strumenti di AzureAzure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Funzioni 1.x applicazioni hanno automaticamente un riferimento il [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet pacchetto, versione 2.x.