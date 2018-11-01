---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 17e17b9847306de1971ebcfbb79f1d95516cba57
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133488"
---
La tabella seguente mostra le associazioni supportate nelle due principali versioni del runtime di Funzioni di Azure.

| type | 1.x | 2.x<sup>1</sup> | Trigger | Input | Output |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Archiviazione BLOB](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|  
| [Griglia di eventi](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Hub eventi](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [File esterno](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [Tabella esterna](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
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
| [Archiviazione tabelle](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|  
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|
| [Webhook](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> nella versione 2.x, devono essere registrate tutte le associazioni tranne HTTP e Timer. Vedere [Registrare le estensioni delle associazioni](../articles/azure-functions/functions-triggers-bindings.md#register-binding-extensions).

<sup>2</sup> Sperimentale &mdash; Non supportato e potrebbe venire abbandonato in futuro.
