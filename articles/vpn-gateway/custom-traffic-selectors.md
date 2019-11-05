---
title: Selettori di traffico personalizzati per le connessioni del gateway VPN di Azure | Microsoft Docs
description: Informazioni su come usare i selettori di traffico personalizzati nelle connessioni gateway VPN
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512158"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>Selettori di traffico personalizzati per le connessioni del gateway VPN

È possibile impostare selettori di traffico personalizzati nelle connessioni del gateway VPN. La possibilità di impostare selettori di traffico consente di limitare gli spazi di indirizzi da entrambi i lati dei tunnel VPN su cui si vuole inviare il traffico. I selettori di traffico personalizzati sono particolarmente utili quando si dispone di uno spazio di indirizzi VNet di grandi dimensioni, ma si vuole usare una delle subnet per la negoziazione IPsec/IKE.

I selettori di traffico personalizzati possono essere aggiunti durante la creazione di una nuova connessione oppure possono essere aggiornati per una connessione esistente. Il parametro `TrafficSelectorPolicies` è costituito da una matrice di selettori di traffico. Ogni selettore di traffico include una raccolta di intervalli di indirizzi locali e remoti in formato CIDR.

## <a name="add-custom-traffic-selectors"></a>Aggiungi selettori di traffico personalizzati

Gli esempi seguenti illustrano come è possibile creare selettori di traffico personalizzati per una connessione del gateway di rete virtuale usando i comandi di PowerShell. Per informazioni su come creare una connessione del gateway di rete virtuale, vedere [configurare una connessione da sito a sito](vpn-gateway-create-site-to-site-rm-powershell.md).

1. Impostare i valori per il parametro *trafficselectorpolicies* in $trafficselectorpolicy. Quando si imposta questa impostazione, si noti che il valore *New-AzTrafficSelectorPolicy* accetta intervalli di indirizzi locali e remoti in una matrice.

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. Creare una nuova connessione gateway di rete virtuale. Modificare i parametri del valore per i requisiti.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

È anche possibile aggiornare i selettori di traffico personalizzati per una connessione del gateway di rete virtuale esistente usando ' set-AzVirtualNetworkGatewayConnection '. Per i valori di PowerShell, vedere [connessione del gateway di rete virtuale](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui gateway VPN, vedere [informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).