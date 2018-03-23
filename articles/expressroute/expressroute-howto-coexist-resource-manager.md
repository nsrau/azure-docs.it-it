---
title: 'Configurare connessioni coesistenti ExpressRoute e VPN da sito a sito - Resource Manager: Azure| Microsoft Docs'
description: Questo articolo illustra come configurare connessioni VPN da sito a sito ed ExpressRoute coesistenti per il modello di distribuzione di Azure Resource Manager.
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: charwen,cherylmc
ms.openlocfilehash: bec984aa04efbe123b71fa9088fbd37ae53cc2f2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections"></a>Configurare connessioni coesistenti ExpressRoute e da sito a sito
> [!div class="op_single_selector"]
> * [PowerShell - Gestione risorse](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - Classico](expressroute-howto-coexist-classic.md)
> 
> 

La configurazione di connessioni coesistenti di tipo VPN da sito a sito ed ExpressRoute offre diversi vantaggi. È possibile configurare una VPN da sito a sito come percorso di failover sicuro per ExpressRoute oppure usare VPN da sito a sito per connettersi a siti che non sono connessi tramite ExpressRoute. In questo articolo vengono illustrati i passaggi per configurare entrambi questi scenari. Questo articolo si applica al modello di distribuzione di Resource Manager e usa PowerShell. Questa configurazione non è disponibile nel portale di Azure.

> [!IMPORTANT]
> Per eseguire le istruzioni riportate di seguito devono essere presenti circuiti ExpressRoute preconfigurati. Verificare di aver seguito le guide per la [creazione di un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e per la [configurazione del routing](expressroute-howto-routing-arm.md) prima di eseguire questa procedura.
> 
> 

## <a name="limits-and-limitations"></a>Limiti e limitazioni
* **L'instradamento del transito non è supportato.** Con Azure non è possibile attivare il routing tra la rete locale connessa tramite la VPN da sito a sito e la rete locale connessa tramite ExpressRoute.
* **Il gateway SKU Basic non è supportato.** È necessario usare un gateway SKU non Basic sia per il [gateway ExpressRoute](expressroute-about-virtual-network-gateways.md) che per il [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **È supportato solo il gateway VPN basato su route.** È necessario usare un [gateway VPN basato su route](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Deve essere configurata una route statica per il gateway VPN.** Se la rete locale è connessa sia a ExpressRoute che a una VPN da sito a sito, per il routing della connessione VPN da sito a sito alla rete Internet pubblica è necessario che nella rete locale sia configurata una route statica.
* **Il gateway ExpressRoute deve essere prima configurato e collegato a un circuito.** È necessario prima creare il gateway ExpressRoute e collegarlo a un circuito prima di aggiungere il gateway VPN da sito a sito.

## <a name="configuration-designs"></a>Progetti di configurazione
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurare una VPN da sito a sito come percorso di failover per ExpressRoute
È possibile configurare una connessione VPN da sito a sito come backup per ExpressRoute. Questo si applica solo alle reti virtuali collegate al percorso di peering privato di Azure. Non esiste alcuna soluzione di failover basato su VPN per i servizi accessibili tramite i peering pubblico di Azure e Microsoft. Il circuito ExpressRoute è sempre il collegamento principale. Il flusso dei dati attraversa il percorso VPN da sito a sito solo se il circuito ExpressRoute ha esito negativo.

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
  
    E’ possibile che esista già una rete virtuale con una connessione VPN da sito a sito o una connessione ExpressRoute. La sezione [Per configurare connessioni coesistenti per una rete virtuale esistente](#add) illustra come eliminare il gateway e quindi come creare nuove connessioni ExpressRoute e VPN da sito a sito. Durante la creazione di nuove connessioni, è necessario completare i passaggi in un ordine specifico. Non usare le istruzioni in altri articoli per creare gateway e connessioni.
  
    In questa procedura, la creazione di connessioni che possono coesistere richiede l'eliminazione del gateway e la configurazione di nuovi gateway. Mentre si elimina e si ricrea il gateway e le connessioni, si avrà un tempo di inattività per le connessioni cross-premise, ma non sarà necessario eseguire la migrazione delle macchine virtuali o dei servizi a una nuova rete virtuale. Le macchine virtuali e i servizi saranno comunque in grado di comunicare tramite il servizio di bilanciamento del carico mentre si configura il gateway, se sono configurati in questo senso.

## <a name="new"></a>Per creare una nuova rete virtuale con connessioni coesistenti
Questa procedura illustra come creare una rete virtuale e connessioni da sito a sito ed ExpressRoute coesistenti.

1. Installare la versione più recente dei cmdlet di Azure PowerShell. Per informazioni sull'installazione dei cmdlet, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). I cmdlet usati per questa configurazione possono essere leggermente diversi da quelli con cui si ha familiarità. Assicurarsi di usare i cmdlet specificati in queste istruzioni.
2. Accedere al proprio account e configurare l'ambiente.

  ```powershell
  login-AzureRmAccount
  Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
  $location = "Central US"
  $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
  $VNetASN = 65010
  ```
3. Creare una rete virtuale con una subnet del gateway. Per altre informazioni sulle creazione di una rete virtuale, vedere [Creare una rete virtuale](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Per altre informazioni sulle creazione di subnet, vedere [Creare una subnet](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)
   
   > [!IMPORTANT]
   > La subnet del gateway deve avere un prefisso /27 o più breve, ad esempio /26 o /25.
   > 
   > 
   
    Creare una nuova rete virtuale.

  ```powershell
  $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
  ```
   
    Aggiungere le subnet.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    Salvare la configurazione di rete virtuale.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
4. <a name="gw"></a>Creare un gateway ExpressRoute. Per altre informazioni sulla configurazione di gateway ExpressRoute, vedere il relativo [articolo](expressroute-howto-add-gateway-resource-manager.md). Il valore di GatewaySKU deve essere *Standard*, *HighPerformance* o *UltraPerformance*.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
  ```
5. Collegare il gateway ExpressRoute al circuito ExpressRoute. Dopo aver completato questo passaggio, viene stabilita la connessione tra la rete locale e Azure tramite ExpressRoute. Per altre informazioni sull'operazione di collegamento, vedere [Collegamento di reti virtuali a circuiti ExpressRoute](expressroute-howto-linkvnet-arm.md).

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
  New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
  ```
6. <a name="vpngw"></a>Creare quindi il gateway VPN da sito a sito. Per altre informazioni sulla configurazione del gateway VPN, vedere [Configurare rete virtuale con una connessione da sito a sito](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). Il valore di GatewaySKU deve essere *Standard*, *HighPerformance* o *UltraPerformance*. Il valore di VpnType deve essere *RouteBased*.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"
  ```
   
    Il gateway VPN di Azure supporta il protocollo di routing BGP. È possibile specificare il codice ASN (Numero AS) per la rete virtuale aggiungendo il parametro -Asn nel comando seguente. Se non si specifica questo parametro, il numero AS predefinito sarà 65515.

  ```powershell
  $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -Asn $VNetASN
  ```
   
    È possibile trovare l'IP del peering BGP e il numero AS usati da Azure per il gateway VPN in $azureVpn.BgpSettings.BgpPeeringAddress e in $azureVpn.BgpSettings.Asn. Per altre informazioni, vedere [Configurare BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) per il gateway VPN di VPN.
7. Creare un'entità gateway VPN del sito locale. Questo comando non configura il gateway VPN locale. Consente invece di fornire le impostazioni del gateway locale, ad esempio l'indirizzo IP pubblico e lo spazio indirizzi locale, in modo che il gateway VPN di Azure possa connettersi.
   
    Se il dispositivo VPN locale supporta solo il routing statico, è possibile configurare le route statiche nel modo seguente:

  ```powershell
  $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
  ```
   
    Se il dispositivo VPN locale supporta BGP e si vuole abilitare il routing dinamico, è necessario conoscere l'IP del peering BGP e il numero AS usati dal dispositivo VPN locale.

  ```powershell
  $localVPNPublicIP = "<Public IP>"
  $localBGPPeeringIP = "<Private IP for the BGP session>"
  $localBGPASN = "<ASN>"
  $localAddressPrefix = $localBGPPeeringIP + "/32"
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
  ```
8. Configurare il dispositivo VPN locale per la connessione al nuovo gateway VPN di Azure. Per altre informazioni sulla configurazione del dispositivo VPN, vedere l'articolo relativo alla [configurazione del dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
9. Collegare il gateway VPN da sito a sito in Azure al gateway locale.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
  ```

## <a name="add"></a>Per configurare connessioni coesistenti per una rete virtuale esistente
Se esiste già una rete virtuale, controllare le dimensioni della subnet del gateway. Se la subnet del gateway è pari a/29 o /28, è necessario eliminare prima di tutto il gateway di rete virtuale e aumentare le dimensioni della subnet del gateway. I passaggi descritti in questa sezione illustrano come eseguire questa operazione.

Se la subnet del gateway è /27 o superiore e la rete virtuale è connessa tramite ExpressRoute, è possibile ignorare i passaggi seguenti e andare al ["Passaggio 6: Creare un gateway VPN da sito a sito"](#vpngw) nella sezione precedente. 

> [!NOTE]
> Quando si elimina il gateway esistente, gli ambienti locali perderanno la connessione alla rete virtuale mentre si lavora a questa configurazione. 
> 
> 

1. È necessario installare l'ultima versione dei cmdlet di Azure PowerShell. Per altre informazioni sull'installazione dei cmdlet, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). I cmdlet usati per questa configurazione possono essere leggermente diversi da quelli con cui si ha familiarità. Assicurarsi di usare i cmdlet specificati in queste istruzioni. 
2. Eliminare il gateway ExpressRoute o VPN da sito a sito esistente.

  ```powershell 
  Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
  ```
3. Eliminare la subnet del gateway.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
  ```
4. Aggiungere una subnet del gateway di /27 o superiore.
   
   > [!NOTE]
   > Se non sono rimasti indirizzi IP sufficienti nella rete virtuale per aumentare le dimensioni della subnet del gateway, è necessario aggiungere altro spazio di indirizzi IP.
   > 
   > 

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    Salvare la configurazione di rete virtuale.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
5. A questo punto, si ha una rete virtuale senza gateway. Per creare nuovi gateway e completare le connessioni, è possibile procedere con il [Passaggio 4: Creare un gateway ExpressRoute](#gw), disponibile nel set di passaggi precedente.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Per aggiungere una configurazione da punto a sito al gateway VPN
Per aggiungere una configurazione da punto a sito al gateway VPN in una configurazione di coesistenza, è possibile seguire questa procedura.

1. Aggiungere un pool di indirizzi client VPN.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
  ```
2. Caricare il certificato radice VPN in Azure per il gateway VPN. In questo esempio si presuppone che il certificato radice sia archiviato nel computer locale in cui vengono eseguiti i cmdlet di PowerShell riportati di seguito.

  ```powershell
  $p2sCertFullName = "RootErVpnCoexP2S.cer" 
  $p2sCertMatchName = "RootErVpnCoexP2S" 
  $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
  if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
  $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
  ```

Per altre informazioni sulle VPN da punto a sito, vedere [Configurare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
