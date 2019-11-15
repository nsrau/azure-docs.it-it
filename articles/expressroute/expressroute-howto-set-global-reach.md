---
title: 'Azure ExpressRoute: configurare Copertura globale'
description: Questo articolo descrive come collegare circuiti ExpressRoute tra loro per creare una rete privata tra le reti locali e abilitare il servizio Copertura globale.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 76de7a8854a58deb924cbbe3177ad5a7b5fd57a2
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083455"
---
# <a name="configure-expressroute-global-reach"></a>Configurare Copertura globale di ExpressRoute

Questo articolo illustra come configurare Copertura globale di ExpressRoute con PowerShell. Per altre informazioni, vedere [Copertura globale di ExpressRoute](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare quanto segue:

* Si conoscono i [flussi di lavoro del](expressroute-workflows.md)provisioning del circuito ExpressRoute.
* I circuiti ExpressRoute sono in stato di provisioning.
* Il peering privato di Azure viene configurato nei circuiti ExpressRoute.
* Se si vuole eseguire PowerShell localmente, verificare che nel computer sia installata la versione più recente di Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identificazione circuiti

1. Per avviare la configurazione, accedere al proprio account Azure e selezionare la sottoscrizione che si vuole usare.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identificare i circuiti ExpressRoute che si vuole usare. È possibile abilitare ExpressRoute Copertura globale tra due circuiti ExpressRoute diversi, purché si trovino nei paesi/aree geografiche supportate e siano stati creati in percorsi di peering diversi. 

   * Se la sottoscrizione è proprietaria di entrambi i circuiti, è possibile scegliere uno dei due circuiti per l'esecuzione della configurazione nelle sezioni seguenti.
   * Se i due circuiti si trovano in sottoscrizioni di Azure diverse, è necessaria l'autorizzazione di una delle due sottoscrizioni. Occorre quindi passare la chiave di autorizzazione quando si esegue il comando di configurazione nell'altra sottoscrizione di Azure.

## <a name="enable-connectivity"></a>Abilitare la connettività

Abilitare la connettività tra le reti locali. Sono disponibili set distinti di istruzioni per i circuiti che si trovano nella stessa sottoscrizione di Azure e circuiti che sono sottoscrizioni diverse.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Circuiti ExpressRoute nella stessa sottoscrizione di Azure

1. Usare i comandi seguenti per ottenere il circuito 1 e il circuito 2. I due circuiti sono inclusi nella stessa sottoscrizione.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Eseguire il comando seguente sul circuito 1 e passare l'ID del peering privato del circuito 2. Durante l'esecuzione del comando si noti quanto segue:

   * L'ID del peering privato ha un aspetto simile all'esempio seguente: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* deve essere una subnet /29 IPv4, ad esempio "10.0.0.0/29". Gli indirizzi IP di questa subnet vengono usati per stabilire la connettività tra i due circuiti ExpressRoute. Evitare di usare indirizzi inclusi in questa subnet nelle reti virtuali di Azure o nelle reti locali.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Salvare la configurazione nel circuito 1, come segue:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Al termine dell'operazione precedente, si disporrà di connettività tra le reti locali su entrambi i lati attraverso i due circuiti ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuiti ExpressRoute in sottoscrizioni di Azure diverse

Se i due circuiti non sono inclusi nella stessa sottoscrizione di Azure, è necessaria l'autorizzazione. Nella configurazione seguente l'autorizzazione viene generata nella sottoscrizione del circuito 2 e la chiave di autorizzazione viene passata al circuito 1.

1. Generare una chiave di autorizzazione.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Annotare l'ID del peering privato del circuito 2, insieme alla chiave di autorizzazione.
2. Eseguire il comando seguente sul circuito 1. Passare l'ID del peering privato del circuito 2 e la chiave di autorizzazione.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Salvare la configurazione nel circuito 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Al termine dell'operazione precedente, si disporrà di connettività tra le reti locali su entrambi i lati attraverso i due circuiti ExpressRoute.

## <a name="verify-the-configuration"></a>Verificare la configurazione

Usare il comando seguente per verificare la configurazione nel circuito in cui è stata eseguita, ad esempio il circuito 1 nell'esempio precedente.
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se si esegue semplicemente *$ckt1* in PowerShell, si vedrà *CircuitConnectionStatus* nell'output. Questo cmdlet indica se è stata stabilita la connettività, con valore "Connected", o meno, con valore "Disconnected". 

## <a name="disable-connectivity"></a>Disabilita connettività

Per disabilitare la connettività tra le reti locali, eseguire i comandi nel circuito in cui è stata eseguita la configurazione (ad esempio, il circuito 1 nell'esempio precedente).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

È possibile eseguire l'operazione Get per verificare lo stato.

Al termine dell'operazione precedente, non sarà più presente connettività tra le reti locali tramite i circuiti ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi
1. [Altre informazioni su Copertura globale di ExpressRoute](expressroute-global-reach.md)
2. [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Collegare un circuito ExpressRoute a una rete virtuale di Azure](expressroute-howto-linkvnet-arm.md)
