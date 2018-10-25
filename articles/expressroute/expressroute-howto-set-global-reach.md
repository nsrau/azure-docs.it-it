---
title: Configurare la copertura globale di Azure ExpressRoute | Microsoft Docs
description: Questo articolo descrive come collegare circuiti ExpressRoute tra loro per creare una rete privata tra le reti locali e abilitare il servizio Copertura globale.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: cherylmc
ms.openlocfilehash: 4006626f9768289e75ccd61a1ef0bad5389f0a7a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071045"
---
# <a name="configure-expressroute-global-reach-preview"></a>Configurare Copertura globale di ExpressRoute (anteprima)
Questo articolo illustra come configurare Copertura globale di ExpressRoute con PowerShell. Per altre informazioni, vedere [Copertura globale di ExpressRoute](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Prima di iniziare
> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Prima di avviare la configurazione, è necessario soddisfare i requisiti seguenti.

* Installare la versione più recente di Azure PowerShell. Vedere [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Installare e configurare Azure PowerShell).
* Avere familiarità con i [flussi di lavoro](expressroute-workflows.md) di provisioning dei circuiti ExpressRoute.
* Assicurarsi che i circuiti ExpressRoute siano in stato Provisioning eseguito.
* Assicurarsi che il peering privato di Azure sia configurato nei circuiti ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Accedere all'account Azure
Per avviare la configurazione, è necessario accedere all'account Azure. 

Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Il comando richiede le credenziali di accesso per l'account Azure.  

```powershell
Connect-AzureRmAccount
```

Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

Specificare la sottoscrizione da usare.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identificare i circuiti ExpressRoute per la configurazione
È possibile abilitare Copertura globale di ExpressRoute tra due circuiti ExpressRoute qualsiasi, purché si trovino nelle aree supportate e vengano creati in località di peering diverse. Se la sottoscrizione è proprietaria di entrambi i circuiti, è possibile scegliere uno dei due circuiti per l'esecuzione della configurazione nelle sezioni seguenti. Se i due circuiti si trovano in diverse sottoscrizioni di Azure, sarà necessaria l'autorizzazione da una sottoscrizione di Azure e si dovrà passare la chiave di autorizzazione quando si esegue il comando di configurazione nell'altra sottoscrizione di Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Abilitare la connettività tra le reti locali

Usare i comandi seguenti per ottenere il circuito 1 e il circuito 2. I due circuiti sono inclusi nella stessa sottoscrizione.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Eseguire il comando seguente sul circuito 1 e passare l'ID del peering privato del circuito 2.

> [!NOTE]
> L'ID del peering privato ha l'aspetto seguente */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering*
> 
>

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

> [!IMPORTANT]
> *-AddressPrefix* deve essere una subnet /29 IPv4, ad esempio "10.0.0.0/29". Si useranno gli indirizzi IP in questa subnet per stabilire la connettività tra i due circuiti ExpressRoute. Evitare di usare indirizzi inclusi in questa subnet nelle reti virtuali di Azure o nelle reti locali.
> 



Salvare la configurazione nel circuito 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Al termine dell'operazione precedente, sarà stata stabilita la connettività tra le reti locali su entrambi i lati tramite i due circuiti ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuiti ExpressRoute in sottoscrizioni di Azure diverse

Se i due circuiti non sono inclusi nella stessa sottoscrizione di Azure, sarà necessaria l'autorizzazione. Nella configurazione seguente l'autorizzazione viene generata nella sottoscrizione del circuito 2 e la chiave di autorizzazione viene passata al circuito 1.

Generare una chiave di autorizzazione. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Annotare l'ID del peering privato del circuito 2, insieme alla chiave di autorizzazione.

Eseguire il comando seguente sul circuito 1. Passare l'ID del peering privato e la chiave di autorizzazione del circuito 2 
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Salvare la configurazione nel circuito 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Al termine dell'operazione precedente, sarà stata stabilita la connettività tra le reti locali su entrambi i lati tramite i due circuiti ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Ottenere e verificare la configurazione

Eseguire questo comando per verificare la configurazione nel circuito in cui è stata eseguita, ad esempio circuito 1 nell'esempio precedente.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se si esegue semplicemente *$ckt1* in PowerShell, si noterà *CircuitConnectionStatus* nell'output. Questo cmdlet indicherà se è stata stabilita la connettività, con valore "Connected", o meno, con valore "Disconnected". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Disabilitare la connettività tra le reti locali

Per disabilitare la connettività, eseguire i comandi sul circuito in cui è stata eseguita la configurazione, ad esempio circuito 1 nell'esempio precedente.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

È possibile eseguire l'operazione Get per verificare lo stato. 

Al termine dell'operazione precedente, non sarà più presente connettività tra le reti locali tramite i circuiti ExpressRoute. 


## <a name="next-steps"></a>Passaggi successivi
1. [Altre informazioni su Copertura globale di ExpressRoute](expressroute-global-reach.md)
2. [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Collegare un circuito ExpressRoute a una rete virtuale di Azure](expressroute-howto-linkvnet-arm.md)


