---
title: Panoramica delle API di Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica delle API disponibili (Runtime e gestione) per l'uso del servizio Hub eventi di Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 08e10996f633d35ffbf946b61937a0e9a76f7227
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162226"
---
# <a name="available-event-hubs-apis"></a>API di Hub eventi disponibili

Questo articolo descrive il set di client API disponibili che è possibile usare per gestire le risorse di Hub eventi.

## <a name="runtime-apis"></a>API di runtime

La sezione seguente illustra tutti i client di runtime di Hub eventi di Azure attualmente disponibili. Sebbene alcune di queste librerie includano anche la funzionalità di gestione limitata, esistono [librerie specifiche](#management-apis) dedicate alle operazioni di gestione. L'obiettivo principale di queste librerie è di inviare e ricevere messaggi da un hub eventi.

Per altre informazioni sullo stato attuale di ogni libreria di runtime, vedere [Informazioni aggiuntive](#additional-information).

| Linguaggio/Piattaforma | Pacchetto client | Pacchetto EventProcessorHost | Archivio |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/D |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Nodo | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/D | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | N/D | N/D | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Informazioni aggiuntive

#### <a name="net"></a>.NET

L'ecosistema .NET include molteplici runtime. Per Hub eventi sono pertanto disponibili più librerie .NET. La libreria .NET Standard può essere eseguita usando .NET Core o .NET Framework, mentre la libreria .NET Framework può essere eseguita solo in un ambiente .NET Framework. Per altre informazioni sulle versioni di .NET Framework, vedere le [versioni del framework](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Nodo

La [libreria JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) è attualmente in fase di anteprima e viene gestita come un progetto lato da dipendenti Microsoft e collaboratori esterni. Tutti i contributi compreso il codice sorgente sono graditi e verranno presi in esame.

## <a name="management-apis"></a>API di gestione

Nella tabella seguente sono elencate tutte le librerie specifiche per la gestione attualmente disponibili. Nessuna di loro include operazioni di runtime e le librerie hanno il solo scopo di gestire le entità di Hub eventi.

| Linguaggio/Piattaforma | Pacchetto di gestione | Archivio |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Creare un hub eventi](event-hubs-create.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
