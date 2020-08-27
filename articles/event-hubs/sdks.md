---
title: Hub eventi di Azure-SDK client | Microsoft Docs
description: Questo articolo fornisce informazioni sugli SDK client per hub eventi di Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9f4eec603245f1e4ea6fa4d97b843ca6a770f2ad
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930877"
---
# <a name="azure-event-hubs---client-sdks"></a>Hub eventi di Azure-SDK client
Questo articolo fornisce le informazioni seguenti per gli SDK supportati da Hub eventi di Azure: 

- Percorso del pacchetto che è possibile usare nelle applicazioni 
- Percorso di GitHub in cui è possibile trovare codice sorgente, esempi, file Leggimi, log delle modifiche, problemi segnalati e anche generare nuovi problemi 
- Collegamenti alle esercitazioni introduttive 

## <a name="client-sdks"></a>Client SDK
La tabella seguente descrive tutti i client di runtime di hub eventi di Azure attualmente disponibili. Sebbene alcune di queste librerie includano anche la funzionalità di gestione limitata, esistono librerie specifiche dedicate alle operazioni di gestione. L'obiettivo principale di queste librerie è di **inviare e ricevere messaggi** da un hub eventi.

| Linguaggio | Pacchetto | Informazioni di riferimento | 
| -------- | ------- | --------------- | 
| . NET standard (**versione più recente** e supporta sia .NET Core che .NET Framework) | [Azure. Messaging. EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Esercitazione](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure. Messaging. EventHubs. Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Esercitazione](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET standard (**legacy** e supporta sia .NET Core che .NET Framework) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Esercitazione](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Esercitazione](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework (**obsoleto**) | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[Esercitazione](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Esercitazione](event-hubs-java-get-started-send.md)</li></ul> |
|      | [Azure-Eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(legacy)** | <ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Esercitazione](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Esercitazione](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Esercitazione](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [Hub eventi di Azure](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Esercitazione](event-hubs-node-get-started-send.md)</li></ul> |
|            | [Azure/Eventhubs-checkpointstore-BLOB](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Esercitazione](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [Azure-Event-Hub-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[Località di GitHub](https://github.com/Azure/azure-event-hubs-go)</li><li>[Esercitazione](event-hubs-go-get-started-send.md)</li></ul> |
| C | [Azure-Event-Hub-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[Località di GitHub](https://github.com/Azure/azure-event-hubs-c)</li><li>[Esercitazione](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>SDK di gestione
Nella tabella seguente sono elencate tutte le librerie specifiche per la gestione attualmente disponibili. Nessuna di queste librerie contiene operazioni di runtime ed è finalizzata esclusivamente alla **gestione delle entità di hub eventi**.

| Linguaggio | Pacchetto | Informazioni di riferimento | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[Località di GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[Esercitazione](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](./event-hubs-about.md)
* [Creare un hub eventi](event-hubs-create.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
