<properties 
   pageTitle="Controllare il routing e usare dispositivi virtuali di rete mediante PowerShell nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come controllare il routing in reti virtuali mediante PowerShell nel modello di distribuzione classica"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />  

#Controllare il routing e usare dispositivi virtuali di rete (distribuzione classica) mediante PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]
In questo articolo viene illustrato il modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

I comandi di esempio di Azure PowerShell riportati di seguito prevedono un ambiente semplice già creato in base allo scenario precedente. Se si desidera eseguire i comandi illustrati in questo documento, creare l'ambiente descritto nella pagina relativa alla [creazione di una rete virtuale (classica) mediante PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Creare la route definita dall'utente per la subnet front-end
Per creare la tabella di route e la route necessarie per la subnet front-end in base allo scenario precedente, attenersi alla procedura seguente.

3. Eseguire il cmdlet **`New-AzureRouteTable`** per creare una tabella di route per la subnet front-end.

		New-AzureRouteTable -Name UDR-FrontEnd `
			-Location uswest `
			-Label "Route table for front end subnet"

	Output:

		Name         Location   Label                          
		----         --------   -----                          
		UDR-FrontEnd West US    Route table for front end subnet

4. Eseguire il cmdlet **`Set-AzureRoute`** per creare una route nella tabella della route creata in precedenza per inviare tutto il traffico destinato alla subnet back-end (192.168.2.0/24) alla macchina virtuale **FW1** (192.168.0.4).
	
		Get-AzureRouteTable UDR-FrontEnd `
			|Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

	Output:

		Name     : UDR-FrontEnd
		Location : West US
		Label    : Route table for frontend subnet
		Routes   : 
		           Name                 Address Prefix    Next hop type        Next hop IP address
		           ----                 --------------    -------------        -------------------
		           RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Eseguire il cmdlet **`Set-AzureSubnetRouteTable`** per associare la tabella di route creata in precedenza alla subnet **FrontEnd**.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd
 
## Creare la route definita dall'utente per la subnet back-end
Per creare la tabella di route e la route necessarie per la subnet back-end in base allo scenario precedente, attenersi alla procedura seguente.

3. Eseguire il cmdlet **`New-AzureRouteTable`** per creare una tabella di route per la subnet back-end.

		New-AzureRouteTable -Name UDR-BackEnd `
			-Location uswest `
			-Label "Route table for back end subnet"

4. Eseguire il cmdlet **`Set-AzureRoute`** per creare una route nella tabella della route creata in precedenza per inviare tutto il traffico destinato alla subnet front-end (192.168.1.0/24) alla macchina virtuale **FW1** (192.168.0.4).

		Get-AzureRouteTable UDR-BackEnd `
			|Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

5. Eseguire il cmdlet **`Set-AzureSubnetRouteTable`** per associare la tabella di route creata in precedenza alla subnet **BackEnd**.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName BackEnd `
			-RouteTableName UDR-BackEnd

## Abilitare l'inoltro dell'indirizzo IP sulla VM FW1
Per abilitare l'inoltro dell'indirizzo IP sulla macchina virtuale FW1, attenersi alla procedura seguente.

1. Eseguire il cmdlet **`Get-AzureIPForwarding`** per verificare lo stato dell'inoltro dell'indirizzo IP

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Get-AzureIPForwarding

	Output:

		Disabled

2. Eseguire il comando **`Set-AzureIPForwarding`** per abilitare l'inoltro dell'indirizzo IP per la macchina virtuale *FW1*.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Set-AzureIPForwarding -Enable

<!---HONumber=AcomDC_0810_2016-->