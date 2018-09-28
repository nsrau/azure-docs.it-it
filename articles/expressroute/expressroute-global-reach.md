---
title: Copertura globale di Azure ExpressRoute | Microsoft Docs
description: Questo articolo descrive come collegare circuiti ExpressRoute tra loro per creare una rete privata tra le reti locali e abilitare il servizio Copertura globale.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 90f34da10e0193e31a0f70187463799013f52be8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966816"
---
# <a name="link-expressroute-circuits-to-enable-expressroute-global-reach-preview"></a>Collegare circuiti ExpressRoute per abilitare il servizio Copertura globale (anteprima)

ExpressRoute è uno strumento privato e resiliente per connettere le reti locali a Microsoft Cloud. È possibile accedere a molti servizi cloud Microsoft, come Azure, Office 365 e Dynamics 365, dal data center privato o dalla rete aziendale. Ad esempio, una delle succursali potrebbe essere a San Francisco con un circuito ExpressRoute nella Silicon Valley e un'altra succursale potrebbe trovarsi a Baltimora con un circuito ExpressRoute a Washington DC. 

Entrambe le succursali possono avere connettività ad alta velocità a risorse di Azure nelle aree Stati Uniti orientali e Stati Uniti occidentali. Tuttavia, non possono scambiare dati direttamente tra loro. In altri termini, 10.0.1.0/24 può scambiare dati con 10.0.3.0/24 e 10.0.4.0/24, ma NON con 10.0.2.0/24.

![Senza][1]

Con **Copertura globale di ExpressRoute** è possibile collegare circuiti ExpressRoute tra loro per creare una rete dati privata tra le reti locali. Aggiungendo Copertura globale di ExpressRoute nell'esempio precedente, la sede di San Francisco (10.0.1.0/24) può scambiare dati direttamente con la sede di Baltimora (10.0.2.0/24) attraverso i circuiti ExpressRoute esistenti e tramite la rete globale di Microsoft. 

![Con][2]

Il servizio Copertura globale di ExpressRoute è attualmente supportato nei paesi seguenti:

* Stati Uniti
* Regno Unito 
* Giappone

I circuiti ExpressRoute devono essere creati in [località di peering ExpressRoute](expressroute-locations.md) nei paesi indicati sopra. Per abilitare Copertura globale di ExpressRoute tra [diverse aree geopolitiche](expressroute-locations.md), i circuiti devono essere SKU Premium.


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

## <a name="to-enable-connectivity-between-your-on-premises-networks"></a>Per abilitare la connettività tra le reti locali

Usare i comandi seguenti per ottenere il circuito 1 e il circuito 2. I due circuiti sono inclusi nella stessa sottoscrizione.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Eseguire il comando seguente sul circuito 1 e passare l'ID del peering privato del circuito 2.

L'ID del peering privato è simile al seguente:

`/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering`


```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

*-AddressPrefix* deve essere una subnet /29 IPv4, ad esempio "10.0.0.0/29". Si useranno gli indirizzi IP in questa subnet per stabilire la connettività tra i due circuiti ExpressRoute. Evitare di usare indirizzi inclusi in questa subnet nelle reti virtuali di Azure o nelle reti locali. 


Salvare la configurazione nel circuito 1.

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

Salvare la configurazione nel circuito 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Al termine dell'operazione precedente, sarà stata stabilita la connettività tra le reti locali su entrambi i lati tramite i due circuiti ExpressRoute.

## <a name="to-get-and-verify-the-configuration"></a>Per ottenere e verificare la configurazione

Eseguire il comando seguente per verificare la configurazione nel circuito in cui è stata eseguita. Questo esempio usa il circuito 1 dell'esempio precedente.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se si esegue semplicemente *$ckt1* in PowerShell, si noterà *CircuitConnectionStatus* nell'output. Questo cmdlet indicherà se è stata stabilita la connettività, con valore "Connected", o meno, con valore "Disconnected". 

## <a name="to-disable-connectivity-between-your-on-premises-networks"></a>Per disabilitare la connettività tra le reti locali

Per disabilitare la connettività, eseguire i comandi sul circuito in cui è stata eseguita la configurazione. Questo esempio usa il circuito 1 degli esempi precedenti.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

È possibile eseguire l'operazione Get per verificare lo stato. 

Al termine dell'operazione precedente, non sarà più presente connettività tra le reti locali tramite i circuiti ExpressRoute. 

## <a name="next-steps"></a>Passaggi successivi
1. [Altre informazioni su Copertura globale di ExpressRoute](expressroute-faqs.md#globalreach)
2. [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Collegare un circuito ExpressRoute a una rete virtuale di Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Diagramma senza Copertura globale"
[2]: ./media/expressroute-global-reach/2.png "Diagramma con Copertura globale"