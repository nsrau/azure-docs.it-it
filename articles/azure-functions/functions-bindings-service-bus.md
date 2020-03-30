---
title: Associazioni del bus di servizio di Azure per Funzioni di Azure
description: Informazioni su come inviare trigger e associazioni del bus di servizio di Azure in Funzioni di Azure.Learn to send Azure Service Bus triggers and bindings in Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277414"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Associazioni del bus di servizio di Azure per Funzioni di Azure

Funzioni di Azure si integra con [il bus di servizio](https://azure.microsoft.com/services/service-bus) di Azure tramite trigger e [associazioni.](./functions-triggers-bindings.md) L'integrazione con il bus di servizio consente di creare funzioni che reagiscono e inviano messaggi di coda o di argomento.

| Azione | Type |
|---------|---------|
| Eseguire una funzione quando viene creato un messaggio di coda o argomento del bus di servizioRun a function when a Service Bus queue or topic message is created | [Grilletto](./functions-bindings-service-bus-trigger.md) |
| Inviare messaggi del bus di servizio di AzureSend Azure Service Bus messages |[Associazione di output](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Aggiungi all'app Funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2.x e successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene utilizzato per le librerie di classi .NET mentre il pacchetto di estensione viene utilizzato per tutti gli altri tipi di applicazione.

| Linguaggio                                        | Aggiungi per...                                   | Osservazioni 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet], versione 4.x | |
| Script di C, Java, JavaScript, Python, PowerShell | Registrazione del pacchetto di [estensione]          | L'estensione Strumenti di Azure è consigliata per l'uso con il codice di Visual Studio.The [Azure Tools extension] is recommended to use with Visual Studio Code. |
| Script di C '(solo online nel portale di Azure)C's Script (online-only in Azure portal)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di associazione esistenti senza dover ripubblicare l'app per le funzioni, vedere [Aggiornare le estensioni.] |

[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[pacchetto di estensione]: ./functions-bindings-register.md#extension-bundles
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Estensione Strumenti di AzureAzure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Funzioni 1.x applicazioni hanno automaticamente un riferimento il [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet pacchetto, versione 2.x.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione quando viene creata una coda del bus di servizio o un messaggio di argomento (Trigger)Run a function when a Service Bus queue or topic message is created (Trigger)](./functions-bindings-service-bus-trigger.md)
- [Inviare messaggi del bus di servizio di Azure da Funzioni di Azure (associazione di output)Send Azure Service Bus messages from Azure Functions (Output binding)](./functions-bindings-service-bus-output.md)
