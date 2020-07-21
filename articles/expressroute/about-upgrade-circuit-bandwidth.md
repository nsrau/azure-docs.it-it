---
title: Informazioni sull'aggiornamento della larghezza di banda del circuito | Azure ExpressRoute
description: Questo articolo illustra le procedure consigliate per l'aggiornamento della larghezza di banda del circuito ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: cherylmc
ms.openlocfilehash: a8f5aaa7b2a054aa31198779414387cebf0f0fbd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537038"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>Informazioni sull'aggiornamento della larghezza di banda del circuito ExpressRoute

ExpressRoute consente la connettività privata e dedicata alla rete globale di Microsoft. La connettività è facilitata dalla rete di un partner ExpressRoute o da una connessione diretta ai dispositivi Microsoft Enterprise Edge (MSEE). Una volta configurata e testata la connettività fisica, è possibile abilitare la connettività di livello 2 e di livello 3 creando un circuito ExpressRoute e configurando il peering.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>Larghezza di banda del circuito di aggiornamento

Per aggiornare la larghezza di banda del circuito, il partner ExpressRoute Direct o ExpressRoute deve avere una [larghezza di banda disponibile sufficiente](#considerations) per la riuscita dell'aggiornamento.

Se la capacità è disponibile, è possibile aggiornare il circuito usando i metodi seguenti:

* [Azure portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Interfaccia della riga di comando di Azure](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>Considerazioni sulla capacità

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>Larghezza di banda del partner ExpressRoute insufficiente

Se il partner ExpressRoute non dispone di capacità sufficiente, è necessario creare un nuovo circuito, configurato per la larghezza di banda desiderata. Per mantenere la connettività, non eliminare il circuito precedente finché non viene eseguito il provisioning del circuito appena creato, il peering è stato configurato e, per quanto riguarda il peering privato, è stato effettuato il provisioning dell'oggetto connessione al gateway di rete virtuale ExpressRoute.

Se il partner ExpressRoute non dispone di sufficiente capacità disponibile, è necessario richiedere capacità aggiuntiva nella località di peering desiderata. Una volta eseguito il provisioning della nuova capacità, è possibile usare i passaggi contenuti negli articoli della sezione relativa alla [larghezza di banda del circuito di aggiornamento](#upgrade) per creare un nuovo circuito, configurare la connettività ed eliminare il circuito precedente.


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>Larghezza di banda ExpressRoute diretta insufficiente

Se la capacità di ExpressRoute Direct non è sufficiente, è possibile eliminare i circuiti associati alla risorsa ExpressRoute diretta che non sono più necessari o creare una nuova risorsa ExpressRoute diretta. Per informazioni sulla gestione della risorsa ExpressRoute Direct, vedere [How to configure ExpressRoute Direct](how-to-expressroute-direct-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare e modificare un circuito](expressroute-howto-circuit-portal-resource-manager.md)
* [Creare e modificare configurazioni di peering](expressroute-howto-routing-portal-resource-manager.md)
* [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
