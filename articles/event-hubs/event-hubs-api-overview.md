---
title: Panoramica delle API di Hub eventi di Azure | Microsoft Docs
description: Panoramica delle API di Hub eventi di Azure disponibili
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: 5a360462288e5df6e0ede5f11adabba158a9dd57

---

# <a name="available-event-hubs-apis"></a>API di Hub eventi disponibili

## <a name="runtime-apis"></a>API di runtime

Di seguito è riportato un elenco di tutti i client di runtime di Hub eventi attualmente disponibili. Sebbene alcune di queste librerie includano anche la funzionalità di gestione limitata, esistono [librerie specifiche](#management-apis) dedicate alle operazioni di gestione. L'obiettivo principale di queste librerie è di inviare e ricevere messaggi da un Hub eventi.

Sono disponibili [altre informazioni](#additional-information) per trovare più dettagli sullo stato corrente di tutte le librerie di runtime.

| Linguaggio/Piattaforma | Pacchetto client | Pacchetto EventProcessorHost | Repository |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/D |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Nodo | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/D | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | N/D | N/D | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Informazioni aggiuntive

#### <a name="net"></a>.NET
I runtime in un ecosistema .NET sono vari, pertanto sono presenti più librerie .NET per gli Hub eventi. La libreria .NET Standard può essere eseguita usando .NET Core o .NET Framework, mentre la libreria .NET Framework può essere eseguita solo in un ambiente .NET Framework. Per ulteriori informazioni su .NET Framework, vedere [Versioni di framework.](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions)

#### <a name="node"></a>Nodo

La libreria Node.js è attualmente in anteprima e viene gestita come progetto a parte da dipendenti Microsoft e collaboratori esterni. Tutti i contributi compreso il codice sorgente sono graditi e verranno presi in esame.

## <a name="management-apis"></a>API di gestione

Di seguito è riportato un elenco di tutte le librerie di gestione specifiche attualmente disponibili. Nessuna di loro include operazioni di runtime e le librerie hanno il solo scopo di gestire le entità di Hub eventi.

| Linguaggio/Piattaforma | Pacchetto di gestione | Repository |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


