---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 193968b5acf8288cb3f602c90374535fc2046b01
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941713"
---
Questa tabella mostra le associazioni supportate nelle versioni principali del runtime di funzioni di Azure:


| Type | 1.x | 2. x e versioni successive<sup>1</sup> | Trigger | Input | Output |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Archiviazione BLOB](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Griglia di eventi](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |
| [Hub eventi](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [Webhook & HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Hub IoT](../articles/azure-functions/functions-bindings-event-iot.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>Tabelle di Excel](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>File di OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Indirizzo e-mail Outlook](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Eventi<br/>Microsoft Graph](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>Token di autenticazione](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [App per dispositivi mobili](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Hub di notifica](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Archiviazione code](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Bus di servizio](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Archiviazione tabelle](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> a partire dal runtime della versione 2. x, è necessario registrare tutte le associazioni eccetto http e timer. Vedere [Registrare le estensioni delle associazioni](../articles/azure-functions/functions-bindings-register.md).
