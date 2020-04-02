---
title: Impostazioni delle porte di Inoltro di Azure | Documentazione Microsoft
description: Questo articolo include una tabella che descrive la configurazione necessaria per i valori di porta per l'inoltro di Azure.This article includes a table that describes the required configuration for port values for Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 532e3c297c42ea16b1f462a01699fc2fd71c6cce
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529122"
---
# <a name="azure-relay-port-settings"></a>Impostazioni delle porte di inoltro di Azure

La tabella seguente descrive la configurazione necessaria per i valori della porta del servizio Inoltro di Azure.

## <a name="hybrid-connections"></a>connessioni ibride

Connessioni ibride utilizza WebSockets sulla porta 443 con TLS come meccanismo di trasporto sottostante, che utilizza solo **HTTPS.** 

## <a name="wcf-relays"></a>Inoltri WCF
  
|Associazione|Sicurezza trasporto|Porta|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (client)|Sì|HTTPS| 
|" |No|HTTP|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (servizio)|Prima o dopo|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (client)|Sì|9351/HTTPS|  
|" |No|9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (servizio)|Prima o dopo|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (client/servizio)|Prima o dopo|5671/9352/HTTP (9352/9353 in caso di implementazione ibrida)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (client)|Sì|9351/HTTPS|  
|" |No|9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (servizio)|Prima o dopo|9351/HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (client)|Sì|HTTPS|  
|" |No|HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (servizio)|Prima o dopo|9351/HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (client)|Sì|HTTPS|  
|" |No|HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (servizio)|Prima o dopo|9351/HTTP|

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul servizio di inoltro di Azure, vedere i collegamenti seguenti:
* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)
* [Domande frequenti sul servizio di inoltro](relay-faq.md)