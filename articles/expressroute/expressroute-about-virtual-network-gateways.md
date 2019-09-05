---
title: Informazioni sui gateway di rete virtuale per ExpressRoute - Azure | Microsoft Docs
description: Informazioni sui gateway di rete virtuale per ExpressRoute. Questo articolo fornisce informazioni su SKU e tipi di gateway.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 5b74e387c6bee58acbbb7bae320a9bc72a4dda1c
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376285"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Gateway di rete virtuale ExpressRoute e FastPath
Per connettere la rete virtuale di Azure e la rete locale tramite ExpressRoute, è prima di tutto necessario creare un gateway di rete virtuale. Un gateway di rete virtuale svolge due finalità: le route IP di Exchange tra le reti e il traffico di rete instradato. Questo articolo illustra i tipi di gateway, gli SKU del gateway e le prestazioni stimate in base allo SKU. Questo articolo illustra anche ExpressRoute [FastPath](#fastpath), una funzionalità che consente al traffico di rete dalla rete locale di ignorare il gateway di rete virtuale per migliorare le prestazioni.

## <a name="gateway-types"></a>Tipi di gateway

Quando si crea un gateway di rete virtuale, è necessario specificare alcune impostazioni. Una delle impostazioni necessarie, "-GatewayType", indica se il gateway verrà usato per ExpressRoute o il traffico VPN. Ci sono due tipi di gateway:

* **Vpn**: per inviare il traffico crittografato attraverso una rete Internet pubblica si usa il gateway di tipo "VPN", detto anche gateway VPN. Le connessioni da sito a sito, da punto a sito e da rete virtuale a rete virtuale usano tutte un gateway VPN.

* **ExpressRoute**: per inviare il traffico di rete con una connessione privata si usa il tipo di gateway ExpressRoute. Questo è detto anche gateway ExpressRoute ed è il tipo di gateway usato durante la configurazione di ExpressRoute.

Ogni rete virtuale può avere un solo gateway di rete virtuale per tipo di gateway. Ad esempio, è possibile configurare un gateway di rete virtuale che usa -GatewayType Vpn e una che usa -GatewayType ExpressRoute.

## <a name="gwsku"></a>SKU del gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se si vuole aggiornare il gateway a uno SKU del gateway più potente, nella maggior parte dei casi è possibile usare il cmdlet di PowerShell ' Resize-AzVirtualNetworkGateway '. Questa tecnica funziona per gli aggiornamenti agli SKU Standard e HighPerformance. Tuttavia, per eseguire l'aggiornamento per allo SKU UltraPerformance sarà necessario ricreare il gateway. La ricreazione di un gateway comporta tempi di inattività.

### <a name="aggthroughput"></a>Prestazioni stimate in base allo SKU del gateway
La tabella seguente illustra i tipi di gateway e le prestazioni stimate. La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Le prestazioni dell'applicazione dipendono da vari fattori, ad esempio la latenza end-to-end e il numero di flussi di traffico avviati dall'applicazione. I numeri nella tabella rappresentano il limite massimo che l'applicazione può raggiungere in teoria in un ambiente ideale.
>
>

### <a name="zrgw"></a>SKU gateway con ridondanza della zona

È possibile distribuire gateway ExpressRoute anche in zone di disponibilità di Azure. Questo le separa in modo fisico e logico in diverse zone di disponibilità, proteggendo la connettività di rete locale ad Azure da errori a livello di zona.

![Gateway ExpressRoute con ridondanza della zona](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

I gateway con ridondanza della zona usano nuovi SKU gateway specifici per il gateway ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

I nuovi SKU gateway supportano anche altre opzioni di distribuzione per soddisfare meglio le esigenze. Quando si crea un gateway di rete virtuale usando i nuovi SKU gateway, è anche possibile distribuire il gateway in una zona specifica. In questo caso si parla di gateway a livello di zona. Quando si distribuisce un gateway a livello di zona, tutte le istanze del gateway vengono distribuite nella stessa zona di disponibilità.

## <a name="fastpath"></a>FastPath
Il gateway di rete virtuale ExpressRoute è progettato per scambiare le route di rete e instradare il traffico di rete. FastPath è progettato per migliorare le prestazioni del percorso dati tra la rete locale e la rete virtuale. Se abilitata, FastPath invia il traffico di rete direttamente alle macchine virtuali nella rete virtuale, ignorando il gateway. 

FastPath è disponibile solo in [ExpressRoute Direct](expressroute-erdirect-about.md) . In altre parole, è possibile abilitare questa funzionalità solo se si [connette la rete virtuale](expressroute-howto-linkvnet-arm.md) a un circuito ExpressRoute creato in una porta ExpressRoute Direct. FastPath richiede ancora la creazione di un gateway di rete virtuale per scambiare le route tra la rete virtuale e la rete locale. Il gateway di rete virtuale deve essere ultra performance o ErGw3AZ.

FastPath non supporta le funzionalità seguenti:
* UDR sulla subnet del gateway: se si applica un UDR alla subnet del gateway della rete virtuale, il traffico di rete proveniente dalla rete locale continuerà a essere inviato al gateway di rete virtuale.
* Peering VNet: se si dispone di altre reti virtuali con peering con quello connesso a ExpressRoute, il traffico di rete dalla rete locale alle altre reti virtuali (ad esempio, il cosiddetto "spoke" reti virtuali) continuerà a essere inviato alla rete virtuale Gateway. La soluzione alternativa consiste nel connettere direttamente tutte le reti virtuali al circuito ExpressRoute.
* Basic Load Balander: se si distribuisce un servizio di bilanciamento del carico interno di base nella rete virtuale o il servizio Azure PaaS distribuito nella rete virtuale usa un servizio di bilanciamento del carico interno di base, il traffico di rete dalla rete locale agli indirizzi IP virtuali ospitati nel Il servizio di bilanciamento del carico di base verrà inviato al gateway di rete virtuale. La soluzione consiste nell'aggiornare il servizio di bilanciamento del carico di base a un servizio di [bilanciamento del carico standard](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview). 
 
## <a name="resources"></a>API REST e cmdlet PowerShell
Per altre risorse tecniche e requisiti di sintassi specifici quando si usano le API REST e i cmdlet PowerShell per le configurazioni di gateway di rete virtuale, vedere le pagine seguenti:

| **Classico** | **Gestione risorse** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle configurazioni delle connessioni disponibili, vedere [Panoramica tecnica relativa a ExpressRoute](expressroute-introduction.md) .

Per altre informazioni sulla creazione di gateway ExpressRoute, vedere [Create a virtual network gateway for ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) (Creare un gateway di rete virtuale per ExpressRoute).

Per altre informazioni sulla configurazione di gateway con ridondanza della zona, vedere [Creare un gateway di rete virtuale con ridondanza della zona](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Per ulteriori informazioni su come abilitare FastPath, vedere [collegare una rete virtuale a ExpressRoute](expressroute-howto-linkvnet-arm.md) . 
