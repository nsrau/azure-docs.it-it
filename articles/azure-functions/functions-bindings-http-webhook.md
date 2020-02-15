---
title: Trigger e associazioni HTTP di Funzioni di Azure
description: Informazioni su come usare trigger e associazioni HTTP in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: ebf0728184a5fc104e3e1e7d8a199fec328dbdc0
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210175"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Cenni preliminari sulle associazioni e sui trigger HTTP di funzioni di Azure

Funzioni di Azure può essere richiamato tramite richieste HTTP per compilare API senza server e rispondere ai [webhook](https://en.wikipedia.org/wiki/Webhook).

| Azione | Type |
|---------|---------|
| Eseguire una funzione da una richiesta HTTP | [Trigger](./functions-bindings-http-webhook-trigger.md) |
| Restituisce una risposta HTTP da una funzione |[Binding di output](./functions-bindings-http-webhook-output.md) |

Per impostazione predefinita, il codice in questo articolo è la sintassi di .NET Core, usata nelle funzioni versione 2. x e successive. Per informazioni sulla sintassi 1.x, consultare i [modelli delle funzioni 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2. x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre l'estensione aggrega tutti gli altri tipi di applicazioni.

| Lingua:                                        | Aggiungi da...                                   | Note 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 3. x | |
| C#Script, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) è consigliata per l'uso con Visual Studio Code. |
| C#Script (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Le app di funzioni 1. x hanno automaticamente un riferimento al pacchetto NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2. x.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione da una richiesta HTTP](./functions-bindings-http-webhook-trigger.md)
- [Restituisce una risposta HTTP da una funzione](./functions-bindings-http-webhook-output.md)
