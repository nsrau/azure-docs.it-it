---
title: Informazioni sui gateway di rete virtuale per ExpressRoute - Azure | Microsoft Docs
description: Informazioni sui gateway di rete virtuale per ExpressRoute. Questo articolo fornisce informazioni su SKU e tipi di gateway.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894392"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Informazioni sui gateway di rete virtuale ExpressRoute

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

## <a name="gwsub"></a>Subnet del gateway

Prima di creare un gateway ExpressRoute, è necessario creare una subnet del gateway. La subnet del gateway contiene gli indirizzi IP usati dalle VM e dai servizi del gateway di rete virtuale. Quando si crea il gateway di rete virtuale, le VM del gateway vengono distribuite nella subnet del gateway e configurate con le impostazioni richieste del gateway ExpressRoute. Non distribuire mai nient'altro (ad esempio, altre VM) alla subnet del gateway. Per poter funzionare correttamente, la subnet del gateway deve essere denominata "GatewaySubnet". Denominando la subnet del gateway 'GatewaySubnet', Azure riconosce questa subnet come quella in cui distribuire i servizi e le VM del gateway di rete virtuale.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando si crea la subnet del gateway, si specifica il numero di indirizzi IP inclusi nella subnet. Gli indirizzi IP inclusi nella subnet del gateway sono allocati alle VM del gateway e ai servizi del gateway. Alcune configurazioni richiedono più indirizzi IP di altre. 

Quando si pianificano le dimensioni della subnet del gateway, fare riferimento alla documentazione per la configurazione che si prevede di creare. Ad esempio, la configurazione della coesistenza del gateway ExpressRoute/VPN richiede una subnet del gateway di dimensioni maggiori rispetto alla maggior parte delle altre configurazioni. È anche consigliabile verificare che la subnet del gateway contenga una quantità di indirizzi IP sufficiente per supportare possibili future configurazioni aggiuntive. Sebbene sia possibile creare una subnet del gateway con dimensioni pari a/29, è consigliabile creare una subnet del gateway di/27 o superiore (/27,/26 e così via) se si dispone dello spazio degli indirizzi disponibile. La maggior parte delle configurazioni verrà adattata.

L'esempio seguente di PowerShell Resource Manager illustra una subnet del gateway denominata GatewaySubnet. La notazione CIDR specifica /27. Questa dimensione ammette un numero di indirizzi IP sufficiente per la maggior parte delle configurazioni attualmente esistenti.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

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

Per ulteriori informazioni su FastPath, inclusi i requisiti e le limitazioni, vedere [informazioni su FastPath](about-fastpath.md).

## <a name="resources"></a>API REST e cmdlet PowerShell
Per altre risorse tecniche e requisiti di sintassi specifici quando si usano le API REST e i cmdlet PowerShell per le configurazioni di gateway di rete virtuale, vedere le pagine seguenti:

| **Classico** | **Gestione risorse** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle configurazioni di connessione disponibili, vedere [Panoramica di ExpressRoute](expressroute-introduction.md).

Per altre informazioni sulla creazione di gateway ExpressRoute, vedere [creare un gateway di rete virtuale per ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Per altre informazioni sulla configurazione dei gateway con ridondanza della zona, vedere [creare un gateway di rete virtuale con ridondanza della zona](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Per ulteriori informazioni su FastPath, vedere [informazioni su FastPath](about-fastpath.md).