---
title: Informazioni su Azure ExpressRoute FastPath
description: Informazioni su Azure ExpressRoute FastPath per inviare il traffico di rete ignorando il gateway
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: c03be46207e7698d0557729c546488412c0cc5dd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437107"
---
# <a name="about-expressroute-fastpath"></a>Informazioni su ExpressRoute FastPath

Il gateway di rete virtuale ExpressRoute è progettato per scambiare le route di rete e instradare il traffico di rete. FastPath è progettato per migliorare le prestazioni del percorso dati tra la rete locale e la rete virtuale. Se abilitata, FastPath invia il traffico di rete direttamente alle macchine virtuali nella rete virtuale, ignorando il gateway.

## <a name="requirements"></a>Requisiti

### <a name="circuits"></a>Circuiti

FastPath è disponibile in tutti i circuiti ExpressRoute.

### <a name="gateways"></a>Gateway

FastPath richiede ancora la creazione di un gateway di rete virtuale per scambiare le route tra la rete virtuale e la rete locale. Per altre informazioni sui gateway di rete virtuale e ExpressRoute, incluse le informazioni sulle prestazioni e gli SKU del gateway, vedere [gateway di rete virtuale ExpressRoute](expressroute-about-virtual-network-gateways.md).

Per configurare FastPath, il gateway di rete virtuale deve essere uno dei seguenti:

* Prestazioni extra
* ErGw3AZ

#### <a name="aggthroughput"></a>Prestazioni stimate in base allo SKU del gateway
La tabella seguente illustra i tipi di gateway e le prestazioni stimate. La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Le prestazioni dell'applicazione dipendono da vari fattori, ad esempio la latenza end-to-end e il numero di flussi di traffico avviati dall'applicazione. I numeri nella tabella rappresentano il limite massimo che l'applicazione può raggiungere in teoria in un ambiente ideale.
>
>

## <a name="supported-features"></a>Caratteristiche supportate

Sebbene FastPath supporti la maggior parte delle configurazioni, non supporta le funzionalità seguenti:

* UDR sulla subnet del gateway: se si applica un UDR alla subnet del gateway della rete virtuale, il traffico di rete proveniente dalla rete locale continuerà a essere inviato al gateway di rete virtuale.

* Peering VNet: se si dispone di altre reti virtuali con peering con quello connesso a ExpressRoute, il traffico di rete dalla rete locale alle altre reti virtuali (ad esempio, il cosiddetto "spoke" reti virtuali) continuerà a essere inviato alla rete virtuale Gateway. La soluzione alternativa consiste nel connettere direttamente tutte le reti virtuali al circuito ExpressRoute.

* Load Balancer di base: se si distribuisce un servizio di bilanciamento del carico interno di base nella rete virtuale o il servizio Azure PaaS distribuito nella rete virtuale usa un servizio di bilanciamento del carico interno di base, il traffico di rete dalla rete locale agli indirizzi IP virtuali ospitati nel Il servizio di bilanciamento del carico di base verrà inviato al gateway di rete virtuale. La soluzione consiste nell'aggiornare il servizio di bilanciamento del carico di base a un servizio di [bilanciamento del carico standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

* Collegamento privato: se ci si connette a un [endpoint privato](../private-link/private-link-overview.md) nella rete virtuale dalla rete locale, la connessione passerà attraverso il gateway di rete virtuale.
 
## <a name="next-steps"></a>Passaggi successivi

Per abilitare FastPath, vedere [collegare una rete virtuale a ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
