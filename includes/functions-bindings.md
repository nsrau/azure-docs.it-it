---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 957009ec9833df2dd0f89c3745c7f3df5a3705b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403609"
---
La tabella seguente mostra le associazioni supportate nelle due principali versioni del runtime di Funzioni di Azure.

| Type | 1.x | 2.x<sup>1</sup> | Trigger | Input | Output |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Archiviazione BLOB](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Griglia di eventi](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |
| [Hub eventi](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP e Webhook](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>Tabelle di Excel](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>File di OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Indirizzo e-mail Outlook](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft Graph<br/>Eventi](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>Token di autenticazione](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [App per dispositivi mobili](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Hub di notifica di Azure](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Archiviazione code](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Bus di servizio](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Archiviazione tabelle](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> nella versione 2.x, devono essere registrate tutte le associazioni tranne HTTP e Timer. Vedere [Registrare le estensioni delle associazioni](../articles/azure-functions/functions-bindings-register.md).
