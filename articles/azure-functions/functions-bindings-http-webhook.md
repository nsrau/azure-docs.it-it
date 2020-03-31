---
title: Trigger e associazioni HTTP di Funzioni di Azure
description: Informazioni su come usare trigger e associazioni HTTP in Funzioni di Azure.Learn to use HTTP triggers and bindings in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462106"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Panoramica dei trigger e delle associazioni HTTP di Funzioni di AzureAzure Functions HTTP triggers and bindings overview

Funzioni di Azure possono essere richiamate tramite richieste HTTP per compilare API senza server e rispondere [ai webhook](https://en.wikipedia.org/wiki/Webhook).

| Azione | Type |
|---------|---------|
| Eseguire una funzione da una richiesta HTTPRun a function from an HTTP request | [Grilletto](./functions-bindings-http-webhook-trigger.md) |
| Restituire una risposta HTTP da una funzioneReturn an HTTP response from a function |[Associazione di output](./functions-bindings-http-webhook-output.md) |

Il codice in questo articolo è la sintassi di .NET Core per impostazione predefinita, usata in Funzioni versione 2.x e successive. Per informazioni sulla sintassi 1.x, consultare i [modelli delle funzioni 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Funzioni 1.x applicazioni hanno automaticamente un riferimento il [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet pacchetto, versione 2.x.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione da una richiesta HTTPRun a function from an HTTP request](./functions-bindings-http-webhook-trigger.md)
- [Restituire una risposta HTTP da una funzioneReturn an HTTP response from a function](./functions-bindings-http-webhook-output.md)
