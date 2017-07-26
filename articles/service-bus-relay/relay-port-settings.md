---
title: Impostazioni delle porte di Inoltro di Azure | Documentazione Microsoft
description: Informazioni dettagliate sui valori di porta di Inoltro di Azure.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 5906495c565dad583e74a43b2e5eed57e0c68df1
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---

# <a name="azure-relay-port-settings"></a>Impostazioni delle porte di inoltro di Azure

La tabella seguente descrive la configurazione necessaria per i valori della porta del servizio Inoltro di Azure.

## <a name="hybrid-connections"></a>Connessioni ibride
Connessioni ibride si avvale di WebSocket come meccanismo di trasporto di base che usa solo **HTTPS**. 

## <a name="wcf-relays"></a>Inoltri WCF
  
|Associazione|Sicurezza trasporto|Porta|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (client)|Sì|HTTPS| 
| |" |No|HTTP|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (servizio)|È possibile usare il|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (client)|Sì|9351/HTTPS|  
||" |No|9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (servizio)|È possibile usare il|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (client/servizio)|È possibile usare il|5671/9352/HTTP (9352/9353 in caso di implementazione ibrida)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (client)|Sì|9351/HTTPS|  
||" |No|9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (servizio)|È possibile usare il|9351/HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (client)|Sì|HTTPS|  
||" |No|HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (servizio)|È possibile usare il|9351/HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (client)|Sì|HTTPS|  
||" |No|HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (servizio)|È possibile usare il|9351/HTTP|

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul servizio di inoltro di Azure, vedere i collegamenti seguenti:
* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)
* [Domande frequenti sul servizio di inoltro](relay-faq.md)
