---
title: 'Configurare il tunneling forzato per connessioni di Azure da sito a sito: Azure Resource Manager | Microsoft Docs'
description: Come reindirizzare o forzare tutto il traffico associato a Internet verso il percorso locale.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: 349f2de9432395860ecb33ea31c59b1cd82170c6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416497"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurare il tunneling forzato tramite il modello di distribuzione Azure Resource Manager

Il tunneling forzato consente di reindirizzare o "forzare" tutto il traffico associato a Internet verso la posizione locale tramite un tunnel VPN da sito a sito per l'ispezione e il controllo. Si tratta di un requisito di sicurezza critico per la maggior parte dei criteri IT aziendali. Senza il tunneling forzato, il traffico verso Internet dalle macchine virtuali in Azure raggiunge sempre direttamente Internet attraversando l'infrastruttura di rete di Azure, senza che si possa analizzare o controllare il traffico. L'accesso a Internet non autorizzato potrebbe provocare la divulgazione di informazioni o altri tipi di violazioni della sicurezza.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Questo articolo illustra la configurazione del tunneling forzato per le reti virtuali create usando il modello di distribuzione di Gestione risorse. Il tunneling forzato può essere configurato tramite PowerShell e non tramite il portale. Se si intende configurare il tunneling forzato per il modello di distribuzione classica, selezionare l'articolo relativo alla distribuzione classica nell'elenco a discesa seguente:

> [!div class="op_single_selector"]
> * [PowerShell - Classico](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell - Gestione risorse](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Informazioni sul tunneling forzato

Nel diagramma seguente viene illustrato il funzionamento del tunneling forzato. 

![Tunneling forzato](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

Nell'esempio riportato in precedenza, il tunneling della subnet front-end non viene forzato. I carichi di lavoro nella subnet front-end possono continuare ad accettare e a rispondere alle richieste dei clienti direttamente da Internet. Il tunneling delle subnet di livello intermedio e back-end viene forzato. Tutte le connessioni in uscita da queste due subnet a Internet verranno forzate o reindirizzate verso un sito locale tramite uno dei tunnel VPN S2S.

Ciò consente di limitare e ispezionare l'accesso a Internet dalle macchine virtuali o dai servizi cloud in Azure, pur continuando ad abilitare l'architettura dei servizi multilivello richiesta. È anche possibile applicare il tunneling forzato a tutte le reti virtuali se non sono presenti carichi di lavoro con connessione Internet nelle reti virtuali.

## <a name="requirements-and-considerations"></a>Problemi e considerazioni

Il tunneling forzato in Azure viene configurato tramite route di rete virtuale definite dall'utente. Il reindirizzamento del traffico a un sito locale viene espresso come route predefinita al gateway VPN di Azure. Per altre informazioni sulle route definite dall'utente e sulle reti virtuali, vedere [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).

* Ciascuna subnet della rete virtuale dispone di una tabella di routing di sistema integrata. La tabella di routing di sistema include i tre gruppi di route seguenti:
  
  * **Route della rete virtuale locale:** direttamente alle macchine virtuali di destinazione nella stessa rete virtuale.
  * **Route in locale:** al gateway VPN di Azure.
  * **Route predefinita:** direttamente a Internet. I pacchetti destinati agli indirizzi IP privati che non rientrano nelle due route precedenti vengono eliminati.
* Questa procedura usa le route definite dall'utente per creare una tabella di routing per aggiungere una route predefinita, quindi associare la tabella di routing alle subnet della rete virtuale per abilitare il tunneling forzato in tali subnet.
* Il tunneling forzato deve essere associato a una rete virtuale con un gateway VPN basato su route. È necessario impostare un "sito predefinito" tra i siti locali cross-premise connessi alla rete virtuale. È anche necessario configurare il dispositivo VPN locale usando 0.0.0.0/0 come selettori di traffico. 
* Il tunneling forzato ExpressRoute non viene configurato mediante questo meccanismo, ma è abilitato annunciando una route predefinita tramite le sessioni di peering BGP ExpressRoute. Per altre informazioni, vedere la [Documentazione su ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Panoramica della configurazione

La procedura seguente consente di creare un gruppo di risorse e una rete virtuale. Si passerà quindi alla creazione di un gateway VPN e alla configurazione del tunneling forzato. In questa procedura, la rete virtuale "MultiTier-VNet" include tre subnet: "Frontend", "Midtier" e "Backend", con quattro connessioni cross-premise: "DefaultSiteHQ" e tre rami.

La procedura illustrata consente di impostare 'DefaultSiteHQ' come connessione predefinita del sito per il tunneling forzato e di configurare le subnet 'Midtier' e 'Backend' per l'uso del tunneling forzato.

## <a name="before"></a>Prima di iniziare

Installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) .

> [!IMPORTANT]
> È consigliabile installare la versione più recente dei cmdlet di PowerShell. In caso contrario, si potrebbero ricevere errori di convalida durante l'esecuzione di alcuni cmdlet.
>
>

### <a name="to-log-in"></a>Per accedere

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>È possibile configurare il tunneling forzato?

> [!NOTE]
> Potrebbero essere visualizzati avvisi indicanti che il tipo di oggetto di output del cmdlet verrà modificato in una versione futura. Si tratta del comportamento previsto ed è possibile ignorare questi avvisi.
>
>


1. Creare un gruppo di risorse.

  ```powershell
  New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
  ```
2. Creare una rete virtuale e specificare le subnet.

  ```powershell 
  $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
  $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
  $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
  $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
  $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
  ```
3. Creare i gateway di rete locale.

  ```powershell
  $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
  $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
  $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
  $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
  ```
4. Creare la tabella di route e la regola di route.

  ```powershell
  New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzRouteTable -RouteTable $rt
  ```
5. Associare la tabella di route alle subnet Midtier e Backend.

  ```powershell
  $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzVirtualNetwork -VirtualNetwork $vnet
  ```
6. Creare il gateway di rete virtuale. Questa procedura può richiedere 45 minuti o più perché include le operazioni di creazione e configurazione del gateway. Se vengono visualizzati errori ValidateSet riguardo al valore GatewaySKU, verificare di avere installato la [versione più recente dei cmdlet di PowerShell](#before). La versione più recente dei cmdlet di PowerShell contiene i nuovi valori convalidati per gli SKU del gateway più recenti.

  ```powershell
  $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
  ```
7. Assegnare un sito predefinito al gateway di rete virtuale. **-GatewayDefaultSite** è il parametro di cmdlet che consente il funzionamento della configurazione del routing forzata. Assicurarsi pertanto di configurare questa impostazione in modo corretto. 

  ```powershell
  $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
  $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
  ```
8. Stabilire le connessioni VPN da sito a sito.

  ```powershell
  $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
  $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
  $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
  $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
  New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
  Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
  ```
