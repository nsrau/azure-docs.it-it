---
title: Panoramica delle API di Inoltro di Azure | Documentazione Microsoft
description: Panoramica delle API di Inoltro di Azure disponibili
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: edc730aa383b07dc308da8a160203faf3636208b
ms.lasthandoff: 03/27/2017

---

# <a name="available-relay-apis"></a>API di Inoltro disponibili

## <a name="runtime-apis"></a>API di runtime

Di seguito è riportato un elenco di tutti i client di runtime di Inoltro attualmente disponibili.

Per altre informazioni sullo stato corrente di tutte le librerie di runtime, vedere [informazioni aggiuntive](#additional-information).

| Linguaggio/Piattaforma | Funzionalità disponibile | Pacchetto client | Repository |
| --- | --- | --- | --- |
| .NET Standard | Connessioni ibride | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Inoltro WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/D |
| Nodo | Connessioni ibride | [hyco-ws](https://www.npmjs.com/package/hyco-ws)<br/>[hyco-websocket](https://www.npmjs.com/package/hyco-websocket) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informazioni aggiuntive

#### <a name="net"></a>.NET
I runtime in un ecosistema .NET sono vari, pertanto sono presenti più librerie .NET per gli Hub eventi. La libreria .NET Standard può essere eseguita usando .NET Core o .NET Framework, mentre la libreria .NET Framework può essere eseguita solo in un ambiente .NET Framework. Per altre informazioni su .NET Framework, vedere le [versioni del framework](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Inoltro di Azure, visitare i collegamenti seguenti:
* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)
* [Domande frequenti sul servizio di inoltro](relay-faq.md)
