---
title: Informazioni sui gateway di rete virtuale per ExpressRoute - Azure | Microsoft Docs
description: Informazioni sui gateway di rete virtuale per ExpressRoute. Questo articolo fornisce informazioni su SKU e tipi di gateway.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 18615cf737eedcd188fd59d2aa98482210b9333a
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991580"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Gateway di rete virtuale ExpressRoute e FastPath
Per connettere la rete virtuale di Azure e la rete locale tramite ExpressRoute, è necessario creare innanzitutto un gateway di rete virtuale. Un gateway di rete virtuale ha due scopi: exchange IP route tra le reti e instradare il traffico di rete. Questo articolo illustra i tipi di gateway, SKU del gateway e delle prestazioni stimato dallo SKU. Questo articolo illustra anche ExpressRoute [FastPath](#fastpath), una funzionalità che consente il traffico di rete dalla rete locale di ignorare il gateway di rete virtuale per migliorare le prestazioni.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gateway-types"></a>Tipi di gateway

Quando si crea un gateway di rete virtuale, è necessario specificare alcune impostazioni. Una delle impostazioni necessarie, "-GatewayType", indica se il gateway verrà usato per ExpressRoute o il traffico VPN. Ci sono due tipi di gateway:

* **Vpn**: per inviare il traffico crittografato attraverso una rete Internet pubblica si usa il gateway di tipo "VPN", detto anche gateway VPN. Le connessioni da sito a sito, da punto a sito e da rete virtuale a rete virtuale usano tutte un gateway VPN.

* **ExpressRoute**: per inviare il traffico di rete con una connessione privata si usa il tipo di gateway ExpressRoute. Questo è detto anche gateway ExpressRoute ed è il tipo di gateway usato durante la configurazione di ExpressRoute.

Ogni rete virtuale può avere un solo gateway di rete virtuale per tipo di gateway. Ad esempio, è possibile configurare un gateway di rete virtuale che usa -GatewayType Vpn e una che usa -GatewayType ExpressRoute.

## <a name="gwsku"></a>SKU del gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se si desidera aggiornare il gateway a uno SKU del gateway più potente, nella maggior parte dei casi è possibile usare il cmdlet di PowerShell 'Resize-AzVirtualNetworkGateway'. Questa tecnica funziona per gli aggiornamenti agli SKU Standard e HighPerformance. Tuttavia, per eseguire l'aggiornamento per allo SKU UltraPerformance sarà necessario ricreare il gateway. La ricreazione di un gateway comporta tempi di inattività.

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
Gateway di rete virtuale ExpressRoute è progettato per scambiare le route di rete e instradare il traffico di rete. FastPath è progettato per migliorare le prestazioni di percorso dei dati tra la rete locale e la rete virtuale. Quando abilitata, FastPath invia il traffico di rete direttamente alle macchine virtuali nella rete virtuale, ignorando il gateway. 

Sono disponibili sul FastPath [ExpressRoute Direct](expressroute-erdirect-about.md) solo. In altre parole, è possibile abilitare solo se questa funzionalità si [connettere la rete virtuale](expressroute-howto-linkvnet-arm.md) a un circuito ExpressRoute creato su una porta diretta di ExpressRoute. FastPath richiede comunque un gateway di rete virtuale deve essere creato per lo scambio delle route tra la rete virtuale e rete locale. Il gateway di rete virtuale deve essere con prestazioni extra o ErGw3AZ.

FastPath non supporta le funzionalità seguenti:
* Route definite dall'utente nella subnet del Gateway: se si applica una route definita dall'utente alla subnet del Gateway della rete virtuale il traffico di rete dalla rete locale continuerà a essere inviato al gateway di rete virtuale.
* Peering reti virtuali: se si dispone di altre reti virtuali con peering con quello che è connessa a ExpressRoute il traffico di rete dalla rete locale alle altre reti virtuali (vale a dire le cosiddette "Spoke" reti virtuali) continueranno a essere inviato alla rete virtuale gateway. La soluzione alternativa consiste nel connettere tutte le reti virtuali al circuito ExpressRoute direttamente.

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

Visualizzare [collegamento di rete virtuale al circuito ExpressRoute](expressroute-howto-linkvnet-arm.md) per altre informazioni su come abilitare FastPath. 
