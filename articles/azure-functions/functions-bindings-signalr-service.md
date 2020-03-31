---
title: Associazioni del servizio SignalR per Funzioni di Azure
description: Informazioni su come usare le associazioni del servizio SignalR con Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523037"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Associazioni del servizio SignalR per Funzioni di Azure

Questo set di articoli illustra come autenticare e inviare messaggi in tempo reale ai client connessi al servizio SignalR di Azure usando le associazioni del servizio SignalR in Funzioni di Azure.This set of articles explains how to authenticate and send real-time messages to clients connected to [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) by using SignalR Service bindings in Azure Functions. Funzioni di Azure supporta le associazioni di input e output per il servizio SignalR.

| Azione | Type |
|---------|---------|
| Restituire l'URL dell'endpoint del servizio e il token di accessoReturn the service endpoint URL and access token | [Associazione di input](./functions-bindings-signalr-service-input.md) |
| Invia messaggi del servizio SignalR |[Associazione di output](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Aggiungi all'app Funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2.x e successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene utilizzato per le librerie di classi .NET mentre il pacchetto di estensione viene utilizzato per tutti gli altri tipi di applicazione.

| Linguaggio                                        | Aggiungi per...                                   | Osservazioni 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet], versione 3.x | |
| Script di C, Java, JavaScript, Python, PowerShell | Registrazione del pacchetto di [estensione]          | L'estensione Strumenti di Azure è consigliata per l'uso con il codice di Visual Studio.The [Azure Tools extension] is recommended to use with Visual Studio Code. |
| Script di C '(solo online nel portale di Azure)C's Script (online-only in Azure portal)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di associazione esistenti senza dover ripubblicare l'app per le funzioni, vedere [Aggiornare le estensioni.] |

[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[pacchetto di estensione]: ./functions-bindings-register.md#extension-bundles
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Estensione Strumenti di AzureAzure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Per informazioni dettagliate su come configurare e usare insieme il servizio SignalR e le funzioni di Azure, vedere Sviluppo e configurazione di Funzioni di Azure con il servizio SignalR di [Azure.](../azure-signalr/signalr-concept-serverless-development-config.md)

### <a name="annotations-library-java-only"></a>Libreria annotazioni (solo Java)

Per usare le annotazioni SignalR Service nelle funzioni Java, è necessario aggiungere una dipendenza dall'elemento *azure-functions-java-library-signalr* (versione 1.0 o successiva) al file *pom.xml.*

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Passaggi successivi

- [Restituire l'URL dell'endpoint del servizio e il token di accesso (associazione di input)Return the service endpoint URL and access token (Input binding)](./functions-bindings-signalr-service-input.md)
- [Invia messaggi del servizio SignalR (associazione di uscita)](./functions-bindings-signalr-service-output.md) 
