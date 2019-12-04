---
title: 'Azure ExpressRoute: reimpostare il peering del circuito'
description: Informazioni su come abilitare e disabilitare i peering di un circuito ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.openlocfilehash: b14b8a9a2bc43c33095bf07e7fb7ebcc2d6c1ffa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769524"
---
# <a name="reset-expressroute-circuit-peerings"></a>Reimpostare i peering del circuito ExpressRoute

Questo articolo descrive come abilitare e disabilitare i peering di un circuito ExpressRoute tramite PowerShell. Quando si disabilita un peering, la sessione BGP viene arrestata sia nella connessione primaria sia nella connessione secondaria del circuito ExpressRoute. Si perde la connettività con questo peering a Microsoft. Quando si abilita un peering, la sessione BGP viene attivata sia nella connessione primaria sia nella connessione secondaria del circuito ExpressRoute. Si riottiene la connettività con questo peering a Microsoft. È possibile abilitare e disabilitare in modo indipendente il peering Microsoft e il peering privato di Azure in un circuito ExpressRoute. Quando si configurano i peering nel circuito ExpressRoute per la prima volta, i peering sono abilitati per impostazione predefinita.

La reimpostazione dei peering ExpressRoute può essere utile in un paio di scenari.
* Esecuzione di test della progettazione e dell'implementazione del ripristino di emergenza. Ad esempio, sono presenti due circuiti ExpressRoute. È possibile disabilitare i peering di un circuito e forzare il failover del traffico di rete nell'altro circuito.
* Abilitazione del rilevamento dell'inoltro bidirezionale nel peering privato di Azure del circuito ExpressRoute. Il rilevamento dell'inoltro bidirezionale è abilitato per impostazione predefinita se il circuito ExpressRoute è stato creato dopo il 1° agosto 2018. Se il circuito è stato creato prima di questa data, il rilevamento dell'inoltro bidirezionale non è stato abilitato. È possibile abilitarlo disabilitando il peering e riabilitandolo. Tenere presente che il rilevamento dell'inoltro bidirezionale è supportato solo nel peering privato di Azure.

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Reimpostare un peering

1. Se si esegue PowerShell in locale, aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Specificare la sottoscrizione da usare.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Eseguire i comandi seguenti per recuperare il circuito ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identificare il peering che si vuole disabilitare o abilitare. *Peerings* è una matrice. Nell'esempio seguente Peerings[0] si riferisce al peering privato di Azure e Peerings[1] al peering Microsoft.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Eseguire i comandi seguenti per modificare lo stato del peering.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Lo stato del peering deve essere quello impostato. 

## <a name="next-steps"></a>Passaggi successivi
Se sono necessarie informazioni per la risoluzione di un problema relativo a ExpressRoute, vedere gli articoli seguenti:
* [Verifica della connettività ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Risoluzione dei problemi di prestazioni di rete](expressroute-troubleshooting-network-performance.md)
