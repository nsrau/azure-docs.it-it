---
title: Come configurare il routing per un circuito ExpressRoute | Microsoft Docs
description: Questo articolo descrive come creare ed eseguire il provisioning di un circuito ExpressRoute per il peering privato, il peering pubblico e il peering Microsoft. Questo articolo mostra anche come controllare lo stato e aggiornare o eliminare i peering per un circuito.
documentationcenter: na
services: expressroute
author: ganesr
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: ganesr
translationtype: Human Translation
ms.sourcegitcommit: ec79ec654505f3e082f9ff106ce24801eae66faf
ms.openlocfilehash: c33270d17cd32e32fac926f62441f0787c056a3e
ms.lasthandoff: 03/02/2017


---
# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Creare e modificare il routing per un circuito ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-routing-arm.md)
> * [Classico - PowerShell](expressroute-howto-routing-classic.md)
> * [Video - Peering privato](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Peering pubblico](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Peering Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

Questo articolo descrive le procedure per creare e gestire la configurazione di routing per un circuito ExpressRoute usando PowerShell e il modello di distribuzione Azure Resource Manager.  La procedura seguente mostra anche come controllare lo stato e aggiornare, eliminare o effettuare il deprovisioning dei peering per un circuito ExpressRoute. 

**Informazioni sui modelli di distribuzione di Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione
* Sarà necessario scaricare la versione più recente dei moduli di Azure PowerShell, la versione 1.0 o quelle successive. 
* Prima di iniziare la configurazione, assicurarsi di aver letto le pagine relative ai [prerequisiti](expressroute-prerequisites.md), ai [requisiti per il routing](expressroute-routing.md) e ai [flussi di lavoro](expressroute-workflows.md).
* È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. Per poter eseguire i cmdlet descritti di seguito deve essere stato effettuato il provisioning del circuito ExpressRoute e lo stato del circuito deve essere abilitato.

Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito un IPVPN, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente.

> [!IMPORTANT]
> Attualmente non vengono annunciati peering configurati da provider di servizi tramite il portale di gestione del servizio. L'abilitazione di questa funzionalità sarà presto disponibile. Rivolgersi al provider di servizi prima di configurare peering BGP.
> 
> 

Per un circuito ExpressRoute è possibile configurare uno, due o tutti e tre i peering (peering privato di Azure, peering pubblico di Azure e peering Microsoft). È possibile configurare i peering nell'ordine desiderato. assicurandosi, tuttavia, di completare la configurazione di un peering per volta. 

## <a name="azure-private-peering"></a>Peering privato di Azure
Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering privato di Azure per un circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Per creare un peering privato di Azure
1. Importare il modulo PowerShell per ExpressRoute.
   
     È necessario installare da [PowerShell Gallery](http://www.powershellgallery.com/) il programma di installazione di PowerShell più recente e importare i moduli di Gestione risorse di Azure nella sessione di PowerShell per iniziare a usare i cmdlet di ExpressRoute. È necessario eseguire PowerShell come amministratore.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Importare tutti i moduli AzureRM.* nell'intervallo noto delle versioni semantiche
   
        Import-AzureRM
   
    È possibile anche importare un solo modulo nell'intervallo noto delle versioni semantiche 
   
        Import-Module AzureRM.Network 
   
    Accedere al proprio account.
   
        Login-AzureRmAccount
   
    Selezionare la sottoscrizione desiderata per creare il circuito ExpressRoute
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Creare un circuito ExpressRoute.
   
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e chiedere al provider di connettività di effettuarne il provisioning. 
   
    Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura. 
3. Verificare che sia stato effettuato il provisioning del circuito ExpressRoute.
   
    In primo luogo, è necessario verificare se è stato effettuato il provisioning del circuito ExpressRoute e se il circuito è abilitato. Vedere l'esempio seguente.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    La risposta sarà simile a quella riportata nel seguente esempio:
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
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
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []
4. Configurare il peering privato di Azure per il circuito.
   
    Prima di procedere con i passaggi successivi, verificare che siano presenti gli elementi seguenti:
   
   * Una subnet /30 per il collegamento primario. Non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
   * Una subnet /30 per il collegamento secondario. Non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte. È possibile usare il numero AS privato per questo peering. Assicurarsi di non usare il numero 65515.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo**.
     
    Per configurare il peering privato di Azure per il circuito, eseguire il cmdlet seguente.
     
         Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200
     
         Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
    Se si sceglie di usare un hash MD5, eseguire il cmdlet seguente.
     
         Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
     
         Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
     > [!IMPORTANT]
     > Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Per visualizzare i dettagli relativi al peering privato di Azure
Per ottenere i dettagli di configurazione, usare il cmdlet seguente

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt    


### <a name="to-update-azure-private-peering-configuration"></a>Per aggiornare la configurazione del peering privato di Azure
Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente. Nell'esempio seguente il valore dell'ID VLAN del circuito viene aggiornato da 100 a 500.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Per eliminare un peering privato di Azure
Per rimuovere la configurazione di peering, eseguire il cmdlet seguente.

> [!WARNING]
> Assicurarsi che tutte le reti virtuali siano scollegate dal circuito ExpressRoute prima di eseguire questo cmdlet. 
> 
> 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Peering pubblico di Azure
Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering pubblico di Azure per un circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Per creare un peering pubblico di Azure
1. Importare il modulo PowerShell per ExpressRoute.
   
     È necessario installare da [PowerShell Gallery](http://www.powershellgallery.com/) il programma di installazione di PowerShell più recente e importare i moduli di Gestione risorse di Azure nella sessione di PowerShell per iniziare a usare i cmdlet di ExpressRoute. È necessario eseguire PowerShell come amministratore.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Importare tutti i moduli AzureRM.* nell'intervallo noto delle versioni semantiche
   
        Import-AzureRM
   
    È possibile anche importare un solo modulo nell'intervallo noto delle versioni semantiche 
   
        Import-Module AzureRM.Network 
   
    Accedere al proprio account.
   
        Login-AzureRmAccount
   
    Selezionare la sottoscrizione desiderata per creare il circuito ExpressRoute
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Creare un circuito ExpressRoute.
   
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e chiedere al provider di connettività di effettuarne il provisioning. 
   
    Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura.
3. Verificare che sia stato eseguito il provisioning del circuito ExpressRoute.
   
    In primo luogo, è necessario verificare se è stato effettuato il provisioning del circuito ExpressRoute e se il circuito è abilitato. Vedere l'esempio seguente.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    La risposta sarà simile a quella riportata nel seguente esempio:
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
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
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []    
4. Configurare il peering pubblico di Azure per il circuito.
   
    Prima di procedere, verificare che siano presenti gli elementi seguenti.
   
   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo**.
     
     Per configurare il peering pubblico di Azure per il circuito, è possibile eseguire il cmdlet seguente
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
     Se si sceglie di usare un hash MD5, eseguire il cmdlet seguente
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    > [!IMPORTANT]
    > Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.


### <a name="to-view-azure-public-peering-details"></a>Per visualizzare i dettagli relativi al peering pubblico di Azure
Per ottenere i dettagli di configurazione, usare il cmdlet seguente

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### <a name="to-update-azure-public-peering-configuration"></a>Per aggiornare la configurazione del peering pubblico di Azure
Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Nell'esempio precedente il valore dell'ID VLAN è stato aggiornato da 200 a 600.

### <a name="to-delete-azure-public-peering"></a>Per eliminare un peering pubblico di Azure
Per rimuovere la configurazione di peering, eseguire il cmdlet seguente

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Peering Microsoft
Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering Microsoft per un circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Per creare il peering Microsoft
1. Importare il modulo PowerShell per ExpressRoute.
   
     È necessario installare da [PowerShell Gallery](http://www.powershellgallery.com/) il programma di installazione di PowerShell più recente e importare i moduli di Gestione risorse di Azure nella sessione di PowerShell per iniziare a usare i cmdlet di ExpressRoute. È necessario eseguire PowerShell come amministratore.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Importare tutti i moduli AzureRM.* nell'intervallo noto delle versioni semantiche
   
        Import-AzureRM
   
    È possibile anche importare un solo modulo nell'intervallo noto delle versioni semantiche 
   
        Import-Module AzureRM.Network 
   
    Accedere al proprio account.
   
        Login-AzureRmAccount
   
    Selezionare la sottoscrizione desiderata per creare il circuito ExpressRoute
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Creare un circuito ExpressRoute.
   
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-arm.md) e chiedere al provider di connettività di effettuarne il provisioning. 
   
    Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura.
3. Verificare che sia stato eseguito il provisioning del circuito ExpressRoute.
   
    In primo luogo, è necessario verificare se è stato effettuato il provisioning del circuito ExpressRoute e se il circuito è abilitato. Vedere l'esempio seguente.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    La risposta sarà simile a quella riportata nel seguente esempio:
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
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
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []    
4. Configurare il peering Microsoft per il circuito.
   
    Prima di procedere, verificare quanto segue:
   
   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Advertised prefixes: è necessario fornire un elenco di tutti i prefissi che si intende pubblicizzare nella sessione BGP. Sono accettati solo prefissi di indirizzi IP pubblici. Per inviare un set di prefissi, è possibile creare un elenco con valori delimitati da virgole. Questi prefissi devono essere intestati all'utente in un registro RIR o IRR.
   * Customer ASN: se si annunciano prefissi registrati al numero AS di peering, è possibile specificare il numero AS a cui sono registrati. **Facoltativo**.
   * Routing Registry Name: è possibile specificare il registro RIR/IRR in cui sono registrati il numero AS e i prefissi.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo.**
     
     Per configurare il peering Microsoft per il circuito, è possibile eseguire il cmdlet seguente
     
       Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"
     
       Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-get-microsoft-peering-details"></a>Ottenere i dettagli del peering Microsoft
Per ottenere i dettagli di configurazione, usare il cmdlet seguente.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Per aggiornare la configurazione del peering Microsoft
Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente.

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-microsoft-peering"></a>Per eliminare il peering Microsoft
Per rimuovere la configurazione di peering, eseguire il cmdlet seguente.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Passaggi successivi
Successivamente, [collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Per ulteriori informazioni sui flussi di lavoro ExpressRoute, vedere [Flussi di lavoro ExpressRoute](expressroute-workflows.md).
* Per altre informazioni sul peering del circuito, vedere l'articolo relativo ai [circuiti ExpressRoute e domini di routing](expressroute-circuit-peerings.md)
* Per ulteriori informazioni sull’uso delle reti virtuali, vedere [Panoramica sulla rete virtuale](../virtual-network/virtual-networks-overview.md).


