---
title: Panoramica delle API di Inoltro di Azure | Documentazione Microsoft
description: Panoramica delle API di Inoltro di Azure disponibili
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: fc6db8aba887b186961da9b12e7c5f32afa4355b
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="available-relay-apis"></a>API di Inoltro disponibili

## <a name="runtime-apis"></a>API di runtime

La tabella seguente elenca tutti i client di runtime di Inoltro attualmente disponibili.

La sezione [Informazioni aggiuntive](#additional-information) contiene altre informazioni sullo stato di ogni libreria di runtime.

| Linguaggio/Piattaforma | Funzionalità disponibile | Pacchetto client | Repository |
| --- | --- | --- | --- |
| .NET Standard | connessioni ibride | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Inoltro WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/D |
| Nodo | connessioni ibride | [`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informazioni aggiuntive

#### <a name="net"></a>.NET

I runtime in un ecosistema .NET sono vari, pertanto sono presenti più librerie .NET per gli Hub eventi. La libreria .NET Standard può essere eseguita usando .NET Core o .NET Framework, mentre la libreria .NET Framework può essere eseguita solo in un ambiente .NET Framework. Per altre informazioni su .NET Framework, vedere le [versioni del framework](/dotnet/articles/standard/frameworks#framework-versions).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio di inoltro di Azure, vedere i collegamenti seguenti:
* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)
* [Domande frequenti sul servizio di inoltro](relay-faq.md)