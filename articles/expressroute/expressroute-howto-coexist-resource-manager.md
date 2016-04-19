<properties
   pageTitle="Configurare connessioni VPN da sito a sito ed ExpressRoute coesistenti per il modello di distribuzione di Azure Resource Manager | Microsoft Azure"
   description="Questo articolo illustra come configurare connessioni VPN da sito a sito ed ExpressRoute coesistenti per il modello di distribuzione di Azure Resource Manager."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/06/2016"
   ms.author="charleywen"/>

# Configurare connessioni coesistenti da sito a sito ed ExpressRoute per il modello di distribuzione di Azure Resource Manager

> [AZURE.SELECTOR]
- [PowerShell - Gestione risorse](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - Classico](expressroute-howto-coexist-classic.md)

La possibilità di configurare una VPN da sito a sito ed ExpressRoute offre diversi vantaggi. È possibile configurare una VPN da sito a sito come percorso di failover sicuro per ExressRoute oppure usare VPN da sito a sito per connettersi a siti che non sono connessi tramite ExpressRoute. In questo articolo verranno illustrati i passaggi per configurare entrambi questi scenari. Questo articolo si applica al modello di distribuzione di Azure Resource Manager. Questa configurazione non è disponibile nel portale di Azure.


**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

>[AZURE.IMPORTANT] Per eseguire le istruzioni riportate di seguito devono essere presenti circuiti ExpressRoute preconfigurati. Assicurarsi di aver seguito le guide per la [creazione di un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e per la [configurazione del routing](expressroute-howto-routing-arm.md) prima di seguire questa procedura.

## Limiti e limitazioni

- **Il routing di transito non è supportato:** Azure non consente il routing tra la rete locale connessa tramite VPN da sito a sito e la rete locale connessa tramite ExpressRoute.
- **Non è possibile abilitare il tunneling forzato sul gateway VPN da sito a sito:** è possibile solo "forzare" tutto il traffico associato a Internet verso la rete locale tramite ExpressRoute. 
- **Solo gateway standard o ad alte prestazioni:** è necessario usare un gateway standard o ad alte prestazioni sia per il gateway ExpressRoute che per il gateway VPN da sito a sito. Per informazioni sugli SKU del gateway, vedere [SKU del gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Solo gateway VPN basato su route:** è necessario usare un gateway VPN basato su route. Per informazioni sul gateway VPN basato su route, vedere [Informazioni sui gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Requisito di route statica:** se la rete locale è connessa sia a ExpressRoute che a una VPN da sito a sito, per il routing della connessione VPN da sito a sito alla rete Internet pubblica è necessario che nella rete locale sia configurata una route statica.
- **Il gateway ExpressRoute deve essere configurato per primo:** è necessario creare il gateway ExpressRoute prima di aggiungere il gateway VPN da sito a sito.


## Progetti di configurazione

### Configurare una VPN da sito a sito come percorso di failover per ExpressRoute

È possibile configurare una connessione VPN da sito a sito come backup per ExpressRoute. Questo si applica solo alle reti virtuali collegate al percorso di peering privato di Azure. Non esiste alcuna soluzione di failover basato su VPN per i servizi accessibili tramite i peering pubblico di Azure e Microsoft. Il circuito ExpressRoute è sempre il collegamento principale. Il flusso dei dati attraverserà il percorso VPN da sito a sito solo se il circuito ExpressRoute ha esito negativo.

![Coesistenza](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### Configurare una VPN da sito a sito per la connessione a siti non connessi tramite ExpressRoute

È possibile configurare una rete in cui alcuni siti si connettono direttamente ad Azure tramite VPN da sito a sito e altri si connettono tramite ExpressRoute.

![Coesistenza](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] Non è possibile configurare una rete virtuale come router di transito.

## Selezione dei passaggi da usare

Esistono due diverse serie di procedure disponibili per configurare le connessioni che possono coesistere. La procedura di configurazione scelta dipende dalla disponibilità o meno di una rete virtuale esistente a cui stabilire la connessione oppure dall'esigenza di creare una nuova rete virtuale.


- Non è disponibile una rete virtuale ed è necessario crearne una.
	
	Se non è disponibile una rete virtuale, questa procedura consente la creazione di una nuova rete virtuale e di nuove connessioni ExpressRoute e VPN da sito a sito usando il modello di distribuzione di Azure Resource Manager. Per la configurazione, eseguire i passaggi nella sezione [Per creare una nuova rete virtuale con connessioni coesistenti](#new).

- È già disponibile una rete virtuale con modello di distribuzione di Azure Resource Manager.

	E’ possibile che esista già una rete virtuale con una connessione VPN da sito a sito o una connessione ExpressRoute. La sezione [Per configurare connessioni coesistenti per una rete virtuale esistente](#add) illustra come eliminare il gateway e quindi come creare nuove connessioni ExpressRoute e VPN da sito a sito. Durante la creazione di nuove connessioni, è necessario completare i passaggi in un ordine molto specifico. Non usare le istruzioni in altri articoli per creare gateway e connessioni.

	In questa procedura, la creazione di connessioni che possono coesistere richiederà l’eliminazione del gateway e la configurazione di nuovi gateway. Mentre si elimina e si ricrea il gateway e le connessioni, si avrà un tempo di inattività per le connessioni cross-premise, ma non sarà necessario eseguire la migrazione delle macchine virtuali o dei servizi a una nuova rete virtuale. Le macchine virtuali e i servizi saranno comunque in grado di comunicare tramite il servizio di bilanciamento del carico mentre si configura il gateway, se sono configurati in questo senso.


## <a name="new"></a>Per creare una nuova rete virtuale con connessioni coesistenti

Questa procedura illustra come creare una rete virtuale e connessioni da sito a sito ed ExpressRoute coesistenti.
	
1. È necessario installare l'ultima versione dei cmdlet di Azure PowerShell. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Si noti che i cmdlet usati per questa configurazione possono essere leggermente diversi da quelli con cui si ha familiarità. Assicurarsi di usare i cmdlet specificati in queste istruzioni.

2. Accedere al proprio account e configurare l'ambiente.
	
		login-AzureRmAccount
		Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
		$location = "Central US"
		$resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. Creare una rete virtuale con una subnet del gateway. Per altre informazioni sulla configurazione della rete virtuale, vedere l'articolo relativo alla [configurazione di una rete virtuale di Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).

	>[AZURE.IMPORTANT] La subnet del gateway deve avere un prefisso /27 o più breve, ad esempio /26 o /25.
	
	Creare una nuova rete virtuale.

		$vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

	Aggiungere le subnet.

		Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
		Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

	Salvare la configurazione di rete virtuale.

		$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Creare un gateway ExpressRoute. Per altre informazioni sulla configurazione di gateway ExpressRoute, vedere il relativo [articolo](expressroute-howto-add-gateway-resource-manager.md). Il valore di GatewaySKU deve essere *Standard* o *HighPerformance*.

		$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
		$gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
		$gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Collegare il gateway ExpressRoute al circuito ExpressRoute. Dopo aver completato questo passaggio, viene stabilita la connessione tra la rete locale e Azure tramite ExpressRoute. Per altre informazioni sull'operazione di collegamento, vedere [Collegamento di reti virtuali a circuiti ExpressRoute](expressroute-howto-linkvnet-arm.md).

		$ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
		New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. Creare quindi il gateway VPN da sito a sito. Per altre informazioni sulla configurazione di gateway VPN, vedere l'articolo relativo alla [configurazione di una connessione tra reti virtuali](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md). Il valore di GatewaySKU deve essere *Standard* o *HighPerformance*. Il valore di VpnType deve *RouteBased*.

		$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
		$gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
		New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

7. Creare un'entità gateway VPN del sito locale. Questo comando non configura il gateway VPN locale. Consente invece di fornire le impostazioni del gateway locale, ad esempio l'indirizzo IP pubblico e lo spazio indirizzi locale, in modo che il gateway VPN di Azure possa connettersi.
	>[AZURE.NOTE] Se la rete locale include più route, è possibile passarle tutte come una matrice. $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")

		$localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix '10.100.0.0/16'

8. Configurare il dispositivo VPN locale per la connessione al nuovo gateway VPN di Azure. Per altre informazioni sulla configurazione del dispositivo VPN, vedere l'articolo relativo alla [configurazione del dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Collegare il gateway VPN da sito a sito in Azure al gateway locale.

		$azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
		New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>Per configurare connessioni coesistenti per una rete virtuale esistente

Se si ha una rete virtuale esistente, connessa tramite una connessione ExpressRoute o VPN da sito a sito, per abilitare entrambe le connessioni in modo da connettersi alla rete virtuale esistente, è necessario eliminare prima di tutto il gateway esistente. Ciò significa che le sedi locali perderanno la connessione alla rete virtuale tramite il gateway mentre si lavora a questa configurazione.

**Prima di iniziare la configurazione:** verificare che nella rete virtuale siano rimasti indirizzi IP sufficienti per aumentare le dimensioni della subnet del gateway. Si noti che sarà necessario eliminare il gateway e ricrearlo anche se si dispone di indirizzi IP sufficienti. Questo avviene perché il gateway deve essere ricreato per supportare le connessioni coesistenti.

1. È necessario installare l'ultima versione dei cmdlet di Azure PowerShell. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Si noti che i cmdlet usati per questa configurazione possono essere leggermente diversi da quelli con cui si ha familiarità. Assicurarsi di usare i cmdlet specificati in queste istruzioni. 

2. Eliminare il gateway ExpressRoute o VPN da sito a sito esistente.

		Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. Eliminare la subnet del gateway.
		
		$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
		Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. Aggiungere una subnet del gateway di /27 o superiore.

		$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
		Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

	Salvare la configurazione di rete virtuale.

		$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. A questo punto, si avrà una rete virtuale senza gateway. Per creare nuovi gateway e completare le connessioni, è possibile procedere con il [passaggio 4: Creare un gateway ExpressRoute](#gw), nel set di passaggi precedente.

## Per aggiungere una configurazione da punto a sito al gateway VPN
Per aggiungere una configurazione da punto a sito al gateway VPN in una configurazione di coesistenza, è possibile seguire questa procedura.

1. Aggiungere un pool di indirizzi client VPN. 

		$azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
		Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. Caricare il certificato radice VPN in Azure per il gateway VPN. In questo esempio si presuppone che il certificato radice sia archiviato nel computer locale in cui vengono eseguiti i cmdlet di PowerShell riportati di seguito.

		$p2sCertFullName = "RootErVpnCoexP2S.cer"
		$p2sCertMatchName = "RootErVpnCoexP2S"
		$p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
		if ($p2sCertToUpload.count -eq 1){
		    write-host "cert found"
		} else {
		    write-host "cert not found"
		    exit
		} 
		$p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Per altre informazioni sulle VPN da punto a sito, vedere l'articolo relativo alla [configurazione di una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0413_2016-->