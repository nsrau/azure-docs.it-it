I passaggi di questa attività riguardano una rete virtuale con i valori seguenti. In questo elenco sono indicati anche i nomi e le impostazioni aggiuntive. Questo elenco non verrà utilizzato direttamente nella procedura, ma si aggiungeranno variabili basate sui valori nell'elenco. È possibile copiare l'elenco per usarlo come riferimento, sostituendo i valori con quelli personalizzati.

Elenco di riferimento per la configurazione:
	
- Nome rete virtuale = "TestVNet"
- Spazio degli indirizzi della rete virtuale = 192.168.0.0/16
- Gruppo di risorse = "TestRG"
- Nome subnet1 = "FrontEnd" 
- Spazio degli indirizzi della subnet1 = "192.168.0.0/16"
- Nome subnet del gateway: "GatewaySubnet" Il nome della subnet del gateway deve sempre essere *GatewaySubnet*.
- Spazio degli indirizzi della subnet gateway = "192.168.200.0/26"
- Area = "East US"
- Nome del gateway = "GW"
- Nome IP del gateway = "GWIP"
- Nome della configurazione IP del gateway = "gwipconf"
-  Tipo = "ExpressRoute" Questo tipo è necessario per una configurazione ExpressRoute.
- Nome IP pubblico del gateway = "gwpip"


## Aggiungere un gateway

1. Connettersi alla sottoscrizione di Azure. 

		Login-AzureRmAccount
		Get-AzureRmSubscription 
		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. Dichiarare le variabili per questo esercizio. In questo esempio si useranno le variabili indicate di seguito. Assicurarsi di modificare l’esempio in base alle impostazioni da usare.
		
		$RG = "TestRG"
		$Location = "East US"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
		$VNetName = "TestVNet"

3. Archiviare l'oggetto rete virtuale come variabile.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. Aggiungere una subnet gateway alla rete virtuale. La subnet gateway deve esistere già e deve essere denominata "GatewaySubnet". È opportuno creare un gateway che sia /27 o più grande (/26, /25, ecc.).
			
		Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. Impostare la configurazione.

			Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. Archiviare la subnet gateway come variabile.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. Richiedere un indirizzo IP pubblico. L'indirizzo IP viene richiesto prima della creazione del gateway. Non è possibile specificare l'indirizzo IP che si desidera utilizzare. Viene allocato in modo dinamico. Questo indirizzo IP sarà utilizzato nella prossima sezione di configurazione. AllocationMethod deve essere Dynamic.

		$pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic

8. Creare la configurazione per il gateway. La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. In questo passaggio si specifica la configurazione che si userà per creare il gateway. Questo passaggio non crea effettivamente l'oggetto gateway. Per creare la configurazione del gateway, usare l'esempio seguente.

		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

9. Creare il gateway. In questo passaggio **-GatewayType** è particolarmente importante. È necessario usare il valore **ExpressRoute**. Si noti che, dopo aver eseguito questi cmdlet, la creazione del gateway può richiedere più di 20 minuti.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## Verificare che il gateway sia stato creato

Utilizzare il comando seguente per verificare che il gateway sia stato creato.

	Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## Ridimensionare un gateway

Sono disponibili tre [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md). È possibile usare il comando seguente per modificare la SKU del gateway in qualsiasi momento.

	$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
	Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## Rimuovere un gateway

Usare il comando seguente per rimuovere un gateway

	Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  

<!---HONumber=AcomDC_0601_2016-->