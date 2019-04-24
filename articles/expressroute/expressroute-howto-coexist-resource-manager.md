---
title: 'Configurare connessioni coesistenti ExpressRoute e VPN da sito a sito: PowerShell: Azure | Microsoft Docs'
description: Come configurare connessioni VPN da sito a sito ed ExpressRoute coesistenti per il modello di distribuzione di Azure Resource Manager tramite PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 4a1f9556413df7ad8954171d2b446419d3bc2975
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60366589"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>Configurare connessioni coesistenti da sito a sito ed ExpressRoute usando PowerShell
> [!div class="op_single_selector"]
> * [PowerShell - Gestione risorse](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - Classico](expressroute-howto-coexist-classic.md)
> 
> 

Questo articolo illustra come configurare connessioni ExpressRoute e VPN da sito a sito coesistenti. La possibilità di configurare una VPN da sito a sito ed ExpressRoute offre diversi vantaggi. È possibile configurare una VPN da sito a sito come percorso di failover sicuro per ExpressRoute oppure usare VPN da sito a sito per connettersi a siti che non sono connessi tramite ExpressRoute. In questo articolo verranno illustrati i passaggi per configurare entrambi questi scenari. Questo articolo si applica al modello di distribuzione di Azure Resource Manager.

La configurazione di connessioni coesistenti di tipo VPN da sito a sito ed ExpressRoute offre diversi vantaggi:

* È possibile configurare una VPN da sito a sito come percorso di failover protetto per ExpressRoute. 
* In alternativa, è possibile usare la VPN da sito a sito per connettersi ai siti che non sono connessi tramite ExpressRoute. 

Questo articolo illustra i passaggi necessari per configurare entrambi questi scenari. Questo articolo si applica al modello di distribuzione di Resource Manager e usa PowerShell. È anche possibile configurare tali scenari tramite il portale di Azure, nonostante la documentazione non sia ancora disponibile. È possibile configurare per primo uno dei due gateway. In genere, l'aggiunta di un nuovo gateway o di una connessione gateway non comporterà tempi di inattività.

>[!NOTE]
>Se si vuole creare una VPN da sito a sito su un circuito ExpressRoute, vedere [questo articolo](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Limiti e limitazioni
* **L'instradamento del transito non è supportato.** Con Azure non è possibile attivare il routing tra la rete locale connessa tramite la VPN da sito a sito e la rete locale connessa tramite ExpressRoute.
* **Il gateway SKU Basic non è supportato.** È necessario usare un gateway SKU non Basic sia per il [gateway ExpressRoute](expressroute-about-virtual-network-gateways.md) che per il [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **È supportato solo il gateway VPN basato su route.** È necessario usare un [gateway VPN basato su route](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Deve essere configurata una route statica per il gateway VPN.** Se la rete locale è connessa sia a ExpressRoute che a una VPN da sito a sito, per il routing della connessione VPN da sito a sito alla rete Internet pubblica è necessario che nella rete locale sia configurata una route statica.
* **Il gateway VPN viene automaticamente impostato su ASN 65515, se non altrimenti specificato.** Il gateway VPN di Azure supporta il protocollo di routing BGP. È possibile specificare il codice ASN (AS Number) per la rete virtuale aggiungendo il parametro -Asn. Se non si specifica questo parametro, il codice ASN predefinito è 65515. È possibile usare qualsiasi codice ASN per la configurazione, ma se si seleziona un valore diverso da 65515, è necessario reimpostare il gateway per rendere effettiva la modifica.

## <a name="configuration-designs"></a>Progetti di configurazione
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurare una VPN da sito a sito come percorso di failover per ExpressRoute
È possibile configurare una connessione VPN da sito a sito come backup per ExpressRoute. Questa connessione si applica solo alle reti virtuali collegate al percorso di peering privato di Azure. Non esiste alcuna soluzione di failover basato su VPN per i servizi accessibili tramite i peering di Microsoft Azure. Il circuito ExpressRoute è sempre il collegamento principale. Il flusso dei dati attraversa il percorso VPN da sito a sito solo se il circuito ExpressRoute ha esito negativo. Per evitare routing asimmetrici, la configurazione della rete locale dovrebbe anche preferire il circuito ExpressRoute rispetto alla connessione VPN da sito a sito. A tal fine, impostare una preferenza locale prioritaria per le route ricevute tramite il circuito ExpressRoute. 

> [!NOTE]
> Mentre il circuito ExpressRoute viene preferito sulla VPN da sito a sito quando entrambe le route sono uguali, Azure userà la corrispondenza di prefisso più lunga per scegliere la route verso la destinazione del pacchetto.
> 
> 

![Coesistenza](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurare una VPN da sito a sito per la connessione a siti non connessi tramite ExpressRoute
È possibile configurare una rete in cui alcuni siti si connettono direttamente ad Azure tramite VPN da sito a sito e altri si connettono tramite ExpressRoute. 

![Coesistenza](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Non è possibile configurare una rete virtuale come router di transito.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Selezione dei passaggi da usare
È possibile scegliere tra due set diversi di procedure. La procedura di configurazione scelta dipende dalla disponibilità o meno di una rete virtuale esistente a cui stabilire la connessione oppure dall'esigenza di creare una nuova rete virtuale.

* Non è disponibile una rete virtuale ed è necessario crearne una.
  
    Se non è disponibile una rete virtuale, questa procedura consente la creazione di una nuova rete virtuale e di nuove connessioni ExpressRoute e VPN da sito a sito usando il modello di distribuzione di Resource Manager. Per configurare una rete virtuale, seguire la procedura in [Per creare una nuova rete virtuale con connessioni coesistenti](#new).
* È già disponibile una rete virtuale con modello di distribuzione di Azure Resource Manager.
  
    E’ possibile che esista già una rete virtuale con una connessione VPN da sito a sito o una connessione ExpressRoute. In questo scenario se la subnet mask del gateway è /28 o maggiore (/28, /29, e così via), è necessario eliminare il gateway esistente. La sezione [Per configurare connessioni coesistenti per una rete virtuale esistente](#add) illustra come eliminare il gateway e quindi come creare nuove connessioni ExpressRoute e VPN da sito a sito.
  
    Eliminare e ricreare il gateway può causare tempi di inattività delle connessioni cross-premise. Le macchine virtuali e i servizi saranno comunque in grado di comunicare tramite il servizio di bilanciamento del carico mentre si configura il gateway, se sono configurati in questo senso.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="new"></a>Per creare una nuova rete virtuale con connessioni coesistenti
Questa procedura illustra come creare una rete virtuale e connessioni da sito a sito ed ExpressRoute coesistenti. I cmdlet usati per questa configurazione possono essere leggermente diversi da quelli con cui si ha familiarità. Assicurarsi di usare i cmdlet specificati in queste istruzioni.

1. Accedere e selezionare la sottoscrizione.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Impostare le variabili.

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. Creare una rete virtuale con una subnet del gateway. Per altre informazioni sulle creazione di una rete virtuale, vedere [Creare una rete virtuale](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Per altre informazioni sulle creazione di subnet, vedere [Creare una subnet](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)
   
   > [!IMPORTANT]
   > La subnet del gateway deve avere un prefisso /27 o più breve, ad esempio /26 o /25.
   > 
   > 
   
    Creare una nuova rete virtuale.

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    Aggiungere le subnet.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Salvare la configurazione di rete virtuale.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>Creare quindi il gateway VPN da sito a sito. Per altre informazioni sulla configurazione del gateway VPN, vedere [Configurare rete virtuale con una connessione da sito a sito](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). GatewaySku è supportato soltanto nei gateway VPN *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* e *HighPerformance*. Le configurazioni con coesistenza di gateway VPN ed ExpressRoute non sono supportate nello SKU di livello Basic. Il valore di VpnType deve essere *RouteBased*.

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    Il gateway VPN di Azure supporta il protocollo di routing BGP. È possibile specificare il codice ASN (Numero AS) per la rete virtuale aggiungendo il parametro -Asn nel comando seguente. Se non si specifica questo parametro, il numero AS predefinito sarà 65515.

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    È possibile trovare l'IP del peering BGP e il numero AS usati da Azure per il gateway VPN in $azureVpn.BgpSettings.BgpPeeringAddress e in $azureVpn.BgpSettings.Asn. Per altre informazioni, vedere [Configurare BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) per il gateway VPN di VPN.
5. Creare un'entità gateway VPN del sito locale. Questo comando non configura il gateway VPN locale. Consente invece di fornire le impostazioni del gateway locale, ad esempio l'indirizzo IP pubblico e lo spazio indirizzi locale, in modo che il gateway VPN di Azure possa connettersi.
   
    Se il dispositivo VPN locale supporta solo il routing statico, è possibile configurare le route statiche nel modo seguente:

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    Se il dispositivo VPN locale supporta BGP e si vuole abilitare il routing dinamico, è necessario conoscere l'IP del peering BGP e il numero AS usati dal dispositivo VPN locale.

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. Configurare il dispositivo VPN locale per la connessione al nuovo gateway VPN di Azure. Per altre informazioni sulla configurazione del dispositivo VPN, vedere l'articolo relativo alla [configurazione del dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

7. Collegare il gateway VPN da sito a sito in Azure al gateway locale.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. Se si effettua la connessione a un circuito ExpressRoute esistente, ignorare i passaggi 8 e 9 e andare al passaggio 10. Configurare circuiti ExpressRoute. Per altre informazioni sulla configurazione del circuito ExpressRoute, vedere [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md).


9. Configurare il peering privato di Azure tramite il circuito ExpressRoute. Per altre informazioni sulla configurazione del peering privato di Azure tramite il circuito ExpressRoute, vedere l'articolo sulla [configurazione del peering](expressroute-howto-routing-arm.md).

10. <a name="gw"></a>Creare un gateway ExpressRoute. Per altre informazioni sulla configurazione di gateway ExpressRoute, vedere il relativo [articolo](expressroute-howto-add-gateway-resource-manager.md). Il valore di GatewaySKU deve essere *Standard*, *HighPerformance* o *UltraPerformance*.

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. Collegare il gateway ExpressRoute al circuito ExpressRoute. Dopo aver completato questo passaggio, viene stabilita la connessione tra la rete locale e Azure tramite ExpressRoute. Per altre informazioni sull'operazione di collegamento, vedere [Collegamento di reti virtuali a circuiti ExpressRoute](expressroute-howto-linkvnet-arm.md).

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="add"></a>Per configurare connessioni coesistenti per una rete virtuale esistente
Se è presente una rete virtuale che include un solo gateway di rete virtuale (ad esempio, un gateway VPN da sito a sito) e si vuole aggiungere un altro gateway di un tipo diverso (ad esempio, un gateway ExpressRoute), controllare le dimensioni della subnet del gateway. Se la subnet del gateway ha dimensioni pari a /27 o superiori, è possibile ignorare i passaggi seguenti e seguire invece i passaggi della sezione precedente per aggiungere un gateway VPN da sito a sito o un gateway ExpressRoute. Se la subnet del gateway è /29 o /28, è necessario eliminare prima di tutto il gateway di rete virtuale e aumentare le dimensioni della subnet del gateway. I passaggi descritti in questa sezione illustrano come eseguire questa operazione.

I cmdlet usati per questa configurazione possono essere leggermente diversi da quelli con cui si ha familiarità. Assicurarsi di usare i cmdlet specificati in queste istruzioni.

1. Eliminare il gateway ExpressRoute o VPN da sito a sito esistente.

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. Eliminare la subnet del gateway.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. Aggiungere una subnet del gateway di /27 o superiore.
   
   > [!NOTE]
   > Se non sono rimasti indirizzi IP sufficienti nella rete virtuale per aumentare le dimensioni della subnet del gateway, è necessario aggiungere altro spazio di indirizzi IP.
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Salvare la configurazione di rete virtuale.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. A questo punto, la rete virtuale è priva di gateway. Per creare nuovi gateway e configurare le connessioni, seguire i passaggi della sezione precedente.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Per aggiungere una configurazione da punto a sito al gateway VPN

Per aggiungere una configurazione da punto a sito al gateway VPN in una configurazione di coesistenza, è possibile seguire questa procedura. Per caricare il certificato radice VPN, è necessario installare PowerShell in locale nel computer o usare il portale di Azure.

1. Aggiungere un pool di indirizzi client VPN.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. Caricare il certificato radice VPN in Azure per il gateway VPN. In questo esempio si presuppone che il certificato radice sia archiviato nel computer locale in cui vengono eseguiti i seguenti cmdlet di PowerShell e che si esegue PowerShell in locale. È anche possibile caricare il certificato nel portale di Azure.

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```

Per altre informazioni sulle VPN da punto a sito, vedere [Configurare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
