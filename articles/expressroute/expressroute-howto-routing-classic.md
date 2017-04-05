---
title: 'Come configurare il routing (peering) per un circuito ExpressRoute: Azure: versione classica | Documentazione Microsoft'
description: Questo articolo descrive come creare ed eseguire il provisioning di un circuito ExpressRoute per il peering privato, il peering pubblico e il peering Microsoft. Questo articolo mostra anche come controllare lo stato e aggiornare o eliminare i peering per un circuito.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: a4bd39d2-373a-467a-8b06-36cfcc1027d2
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 6315e0fda231f2bfd3a92cf03cea7cd558bfda37
ms.lasthandoff: 03/24/2017


---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Creare e modificare il peering per un circuito ExpressRoute (versione classica)
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-routing-arm.md)
> * [Classico - PowerShell](expressroute-howto-routing-classic.md)
> * [Video - Peering privato](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Peering pubblico](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Peering Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

Questo articolo descrive le procedure per creare e gestire la configurazione di routing per un circuito ExpressRoute usando PowerShell e il modello di distribuzione classico. La procedura seguente mostra anche come controllare lo stato e aggiornare, eliminare o effettuare il deprovisioning dei peering per un circuito ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Informazioni sui modelli di distribuzione di Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione
* È necessaria la versione più recente dei cmdlet di PowerShell per Gestione dei servizi di Azure. Per altre informazioni, vedere [Introduzione ai cmdlet di Azure PowerShell](/powershell/azureps-cmdlets-docs).  
* Prima di iniziare la configurazione, assicurarsi di aver letto le pagine relative ai [prerequisiti](expressroute-prerequisites.md), ai [requisiti per il routing](expressroute-routing.md) e ai [flussi di lavoro](expressroute-workflows.md).
* È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. Per poter eseguire i cmdlet descritti di seguito deve essere stato effettuato il provisioning del circuito ExpressRoute e lo stato del circuito deve essere abilitato.

> [!IMPORTANT]
> Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito un IPVPN, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente.
> 
> 

Per un circuito ExpressRoute è possibile configurare uno, due o tutti e tre i peering (peering privato di Azure, peering pubblico di Azure e peering Microsoft). È possibile configurare i peering nell'ordine desiderato. assicurandosi, tuttavia, di completare la configurazione di un peering per volta.


### <a name="log-in-to-your-azure-account-and-select-a-subscription"></a>Accedere all'account Azure e selezionare una sottoscrizione
1. Aprire la console di PowerShell con diritti elevati e connettersi all'account. Per eseguire la connessione, usare gli esempi che seguono:

        Login-AzureRmAccount

2. Controllare le sottoscrizioni per l'account.

        Get-AzureRmSubscription

3. Se sono disponibili più sottoscrizioni, selezionare la sottoscrizione da usare.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. Successivamente, utilizzare il cmdlet seguente per aggiungere la sottoscrizione di Azure a PowerShell per il modello di distribuzione classico.

        Add-AzureAccount


## <a name="azure-private-peering"></a>Peering privato di Azure
Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering privato di Azure per un circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Per creare un peering privato di Azure
1. **Importare il modulo PowerShell per ExpressRoute.**
   
    Per iniziare a usare i cmdlet per ExpressRoute, è necessario importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell. Per importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell, eseguire i comandi seguenti.  
   
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
2. **Creare un circuito ExpressRoute.**
   
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura. 
3. **Verificare che sia stato effettuato il provisioning del circuito ExpressRoute.**
   
    In primo luogo, è necessario verificare se è stato effettuato il provisioning del circuito ExpressRoute e se il circuito è abilitato. Vedere l'esempio seguente.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Assicurarsi che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. In caso contrario, rivolgersi al provider di connettività affinché imposti gli stati necessari per il circuito.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Configurare il peering privato di Azure per il circuito.**
   
    Prima di procedere con i passaggi successivi, verificare che siano presenti gli elementi seguenti:
   
   * Una subnet /30 per il collegamento primario. Non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
   * Una subnet /30 per il collegamento secondario. Non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte. È possibile usare il numero AS privato per questo peering. Assicurarsi di non usare il numero 65515.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo**.
     
    Per configurare il peering privato di Azure per il circuito, eseguire il cmdlet seguente.
     
          New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
     
    Se si sceglie di usare un hash MD5, eseguire il cmdlet seguente.
     
          New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Per visualizzare i dettagli relativi al peering privato di Azure
Per ottenere i dettagli di configurazione, usare il cmdlet seguente

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Per aggiornare la configurazione del peering privato di Azure
Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente. Nell'esempio seguente il valore dell'ID VLAN del circuito viene aggiornato da 100 a 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Per eliminare un peering privato di Azure
Per rimuovere la configurazione di peering, eseguire il cmdlet seguente.

> [!WARNING]
> Assicurarsi che tutte le reti virtuali siano scollegate dal circuito ExpressRoute prima di eseguire questo cmdlet. 
> 
> 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Peering pubblico di Azure
Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering pubblico di Azure per un circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Per creare un peering pubblico di Azure
1. **Importare il modulo PowerShell per ExpressRoute.**
   
    Per iniziare a usare i cmdlet per ExpressRoute, è necessario importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell. Per importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell, eseguire i comandi seguenti. 
   
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
2. **Creare un circuito ExpressRoute**
   
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura.
3. **Verificare che sia stato eseguito il provisioning del circuito ExpressRoute**
   
    In primo luogo, è necessario verificare se è stato effettuato il provisioning del circuito ExpressRoute e se il circuito è abilitato. Vedere l'esempio seguente.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Assicurarsi che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. In caso contrario, rivolgersi al provider di connettività affinché imposti gli stati necessari per il circuito.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Configurare il peering pubblico di Azure per il circuito**
   
    Prima di procedere, verificare che siano presenti gli elementi seguenti.
   
   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo**.
     
    Per configurare il peering pubblico di Azure per il circuito, è possibile eseguire il cmdlet seguente
     
          New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
     
    Se si sceglie di usare un hash MD5, eseguire il cmdlet seguente
     
          New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.
     > 
     > 

### <a name="to-view-azure-public-peering-details"></a>Per visualizzare i dettagli relativi al peering pubblico di Azure
Per ottenere i dettagli di configurazione, usare il cmdlet seguente

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Per aggiornare la configurazione del peering pubblico di Azure
Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

Nell'esempio precedente il valore dell'ID VLAN è stato aggiornato da 200 a 600.

### <a name="to-delete-azure-public-peering"></a>Per eliminare un peering pubblico di Azure
Per rimuovere la configurazione di peering, eseguire il cmdlet seguente

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Peering Microsoft
Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering Microsoft per un circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Per creare il peering Microsoft
1. **Importare il modulo PowerShell per ExpressRoute.**
   
    Per iniziare a usare i cmdlet per ExpressRoute, è necessario importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell. Per importare i moduli di Azure ed ExpressRoute nella sessione di PowerShell, eseguire i comandi seguenti.  
   
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
2. **Creare un circuito ExpressRoute**
   
    Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura.
3. **Verificare che sia stato eseguito il provisioning del circuito ExpressRoute**
   
    In primo luogo, è necessario verificare se è stato eseguito il provisioning del circuito ExpressRoute e se il circuito è abilitato.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Assicurarsi che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. In caso contrario, rivolgersi al provider di connettività affinché imposti gli stati necessari per il circuito.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Configurare il peering Microsoft per il circuito**
   
    Prima di procedere, verificare quanto segue:
   
   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Un ID VLAN valido su cui stabilire questo peering. Assicurarsi che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Advertised prefixes: è necessario fornire un elenco di tutti i prefissi che si intende pubblicizzare nella sessione BGP. Sono accettati solo prefissi di indirizzi IP pubblici. Per inviare un set di prefissi, è possibile creare un elenco con valori delimitati da virgole. Questi prefissi devono essere intestati all'utente in un registro RIR o IRR.
   * Customer ASN: se si annunciano prefissi registrati al numero AS di peering, è possibile specificare il numero AS a cui sono registrati. **Facoltativo**.
   * Routing Registry Name: è possibile specificare il registro RIR/IRR in cui sono registrati il numero AS e i prefissi.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo.**
     
    Per configurare il peering Microsoft per il circuito, eseguire il cmdlet seguente
     
          New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-view-microsoft-peering-details"></a>Per visualizzare i dettagli del peering Microsoft
Per ottenere i dettagli di configurazione, usare il cmdlet seguente.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Per aggiornare la configurazione del peering Microsoft
Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente.

    Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Per eliminare il peering Microsoft
Per rimuovere la configurazione di peering, eseguire il cmdlet seguente.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Passaggi successivi
Successivamente, [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

* Per ulteriori informazioni sui flussi di lavoro, vedere [Flussi di lavoro ExpressRoute](expressroute-workflows.md).
* Per altre informazioni sul peering del circuito, vedere l'articolo relativo ai [circuiti ExpressRoute e domini di routing](expressroute-circuit-peerings.md)


