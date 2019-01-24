---
title: 'Configurare Copertura globale - ExpressRoute: Azure | Microsoft Docs'
description: Questo articolo descrive come collegare circuiti ExpressRoute tra loro per creare una rete privata tra le reti locali e abilitare il servizio Copertura globale.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: ab1098ca65ad92cffdbe1dfb24fd43fcc8f10eae
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431679"
---
# <a name="configure-expressroute-global-reach-preview"></a>Configurare Copertura globale di ExpressRoute (anteprima)
Questo articolo illustra come configurare Copertura globale di ExpressRoute con PowerShell. Per altre informazioni, vedere [Copertura globale di ExpressRoute](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Prima di iniziare
> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Prima di iniziare la configurazione, verificare quanto segue:

* La versione più recente di Azure PowerShell è installata. Per altre informazioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
* Si ha familiarità con i [flussi di lavoro](expressroute-workflows.md) di provisioning dei circuiti ExpressRoute.
* I circuiti ExpressRoute sono nello stato di provisioning eseguito.
* Il peering privato di Azure è configurato nei circuiti ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Accedere con l'account Azure
Per iniziare la configurazione, accedere al proprio account Azure. 

Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Il comando richiede le credenziali di accesso per l'account di Azure.  

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
È possibile abilitare Copertura globale di ExpressRoute tra due circuiti ExpressRoute qualsiasi, purché si trovino nelle aree supportate e siano stati creati in località di peering diverse. Se la sottoscrizione è proprietaria di entrambi i circuiti, è possibile scegliere uno dei due circuiti per l'esecuzione della configurazione nelle sezioni seguenti. 

Se i due circuiti si trovano in sottoscrizioni di Azure diverse, è necessaria l'autorizzazione di una delle due sottoscrizioni. Occorre quindi passare la chiave di autorizzazione quando si esegue il comando di configurazione nell'altra sottoscrizione di Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Abilitare la connettività tra le reti locali

Usare i comandi seguenti per ottenere il circuito 1 e il circuito 2. I due circuiti sono inclusi nella stessa sottoscrizione.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Eseguire il comando seguente sul circuito 1 e passare l'ID del peering privato del circuito 2. Durante l'esecuzione del comando si noti quanto segue:

* L'ID del peering privato ha un aspetto simile all'esempio seguente: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix* deve essere una subnet /29 IPv4, ad esempio "10.0.0.0/29". Gli indirizzi IP di questa subnet vengono usati per stabilire la connettività tra i due circuiti ExpressRoute. Evitare di usare indirizzi inclusi in questa subnet nelle reti virtuali di Azure o nelle reti locali.

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

Salvare la configurazione nel circuito 1, come segue:
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Al termine dell'operazione precedente, sarà stata stabilita la connettività tra le reti locali su entrambi i lati tramite i due circuiti ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuiti ExpressRoute in sottoscrizioni di Azure diverse

Se i due circuiti non sono inclusi nella stessa sottoscrizione di Azure, è necessaria l'autorizzazione. Nella configurazione seguente l'autorizzazione viene generata nella sottoscrizione del circuito 2 e la chiave di autorizzazione viene passata al circuito 1.

Generare una chiave di autorizzazione. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Annotare l'ID del peering privato del circuito 2, insieme alla chiave di autorizzazione.

Eseguire il comando seguente sul circuito 1. Passare l'ID del peering privato del circuito 2 e la chiave di autorizzazione.
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Salvare la configurazione nel circuito 1.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Al termine dell'operazione precedente, sarà stata stabilita la connettività tra le reti locali su entrambi i lati tramite i due circuiti ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Ottenere e verificare la configurazione

Usare il comando seguente per verificare la configurazione nel circuito in cui è stata eseguita, ad esempio il circuito 1 nell'esempio precedente.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se si esegue semplicemente *$ckt1* in PowerShell, si vedrà *CircuitConnectionStatus* nell'output. Questo cmdlet indica se è stata stabilita la connettività, con valore "Connected", o meno, con valore "Disconnected". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Disabilitare la connettività tra le reti locali

Per disabilitare la connettività, eseguire i comandi sul circuito in cui è stata eseguita la configurazione, ad esempio il circuito 1 nell'esempio precedente.

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


