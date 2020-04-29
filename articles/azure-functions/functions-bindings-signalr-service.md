---
title: Associazioni del servizio SignalR per Funzioni di Azure
description: Informazioni su come usare le associazioni del servizio SignalR con Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523037"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Associazioni del servizio SignalR per Funzioni di Azure

Questo set di articoli illustra come eseguire l'autenticazione e inviare messaggi in tempo reale ai client connessi al [servizio Azure SignalR](https://azure.microsoft.com/services/signalr-service/) usando le associazioni del servizio SignalR in funzioni di Azure. Funzioni di Azure supporta le associazioni di input e output per il servizio SignalR.

| Action | Tipo |
|---------|---------|
| Restituisce l'URL dell'endpoint del servizio e il token di accesso | [Binding di input](./functions-bindings-signalr-service-input.md) |
| Inviare messaggi del servizio SignalR |[Binding di output](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2. x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre il bundle di estensione viene usato per tutti gli altri tipi di applicazioni.

| Linguaggio                                        | Aggiungi da...                                   | Osservazioni 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure] è consigliata per l'uso con Visual Studio Code. |
| Script C# (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Estensione degli strumenti di Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Per informazioni dettagliate su come configurare e usare insieme il servizio SignalR e funzioni di Azure, vedere [sviluppo e configurazione di funzioni di Azure con il servizio Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Libreria Annotations (solo Java)

Per usare le annotazioni del servizio SignalR nelle funzioni Java, è necessario aggiungere una dipendenza all'artefatto di *Azure-Functions-Java-Library-SignalR* (versione 1,0 o successiva) al file *POM. XML* .

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Passaggi successivi

- [Restituire l'URL dell'endpoint del servizio e il token di accesso (associazione di input)](./functions-bindings-signalr-service-input.md)
- [Inviare messaggi del servizio SignalR (associazione di output)](./functions-bindings-signalr-service-output.md) 
