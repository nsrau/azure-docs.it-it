---
title: Azure Event Grid bindings for Azure Functions
description: Informazioni su come gestire gli eventi di Griglia di eventi in Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461080"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Event Grid bindings for Azure Functions

Questo riferimento illustra come gestire gli eventi [di Griglia di eventi](../event-grid/overview.md) in Funzioni di Azure.This reference explains how to handle Event Grid events in Azure Functions. Per informazioni dettagliate su come gestire i messaggi della griglia di eventi in un endpoint HTTP, vedere [Ricezione di eventi a un endpoint HTTP.](../event-grid/receive-events.md)

Griglia di eventi è un servizio di Azure che consente di inviare richieste HTTP per la notifica degli eventi che si verificano negli *editori*. Un editore è il servizio o la risorsa da cui ha origine l'evento. Ad esempio, un account di archiviazione BLOB di Azure è un editore, mentre [un'eliminazione o un caricamento di BLOB è un evento](../storage/blobs/storage-blob-event-overview.md). Alcuni [servizi di Azure includono il supporto incorporato per la pubblicazione di eventi in Griglia di eventi](../event-grid/overview.md#event-sources).

I *gestori* di eventi ricevono ed elaborano gli eventi. Funzioni di Azure è uno dei vari [servizi di Azure con supporto incorporato per la gestione degli eventi di Griglia di eventi](../event-grid/overview.md#event-handlers). In questo riferimento si apprenderà a utilizzare un trigger Griglia di eventi per richiamare una funzione quando un evento viene ricevuto da Griglia di eventi e a utilizzare l'associazione di output per inviare eventi a un [argomento personalizzato Griglia di eventi.](../event-grid/post-to-custom-topic.md)

Se si preferisce, è possibile utilizzare un trigger HTTP per gestire gli eventi della griglia di eventi; vedere [Ricezione di eventi a un endpoint HTTP](../event-grid/receive-events.md). Attualmente, non è possibile usare un trigger Griglia di eventi per un'app Funzioni di Azure quando l'evento viene recapitato nello [schema CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). È necessario usare invece un trigger HTTP.

| Azione | Type |
|---------|---------|
| Eseguire una funzione quando viene inviato un evento Griglia di eventiRun a function when an Event Grid event is dispatched | [Grilletto](./functions-bindings-event-grid-trigger.md) |
| Invia un evento Grid di eventi |[Associazione di output](./functions-bindings-event-grid-output.md) |

Il codice in questo riferimento per impostazione predefinita è la sintassi .NET Core, utilizzata in Funzioni versione 2.x e successive. Per informazioni sulla sintassi 1.x, consultare i [modelli delle funzioni 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Funzioni 1.x applicazioni hanno automaticamente un riferimento il [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet pacchetto, versione 2.x.

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire una funzione quando viene inviato un evento Griglia di eventiRun a function when an Event Grid event is dispatched](./functions-bindings-event-grid-trigger.md)
* [Inviare un evento Griglia di eventi](./functions-bindings-event-grid-trigger.md)
