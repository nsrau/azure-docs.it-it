<properties
   pageTitle="Creare una rete virtuale con una connessione VPN da sito a sito utilizzando Gestione risorse di Azure e PowerShell | Microsoft Azure"
   description="Questo articolo illustra la creazione di una rete virtuale mediante il modello di Gestione risorse e la connessione alla rete locale mediante una connessione del gateway VPN da sito a sito. Include passaggi aggiuntivi per modificare i prefissi di indirizzo IP per i siti locali esistenti."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/30/2015"
   ms.author="cherylmc"/>

# Creare una rete virtuale con una connessione VPN da sito a sito mediante PowerShell

> [AZURE.SELECTOR]
- [Azure Classic Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Questo articolo illustra la creazione di una rete virtuale e di una connessione VPN da sito a sito alla rete locale mediante il modello di distribuzione di Gestione risorse di Azure. È possibile selezionare l'articolo per il modello di distribuzione e lo strumento di distribuzione usando le schede riportate sopra.

>[AZURE.NOTE]Prima di creare una rete virtuale, è importante comprendere che Azure attualmente funziona con due modelli di distribuzione: Gestione delle risorse e Classico. Prima di iniziare la configurazione, assicurarsi di comprendere i modelli di distribuzione e gli strumenti. Per informazioni sui modelli di distribuzione, vedere [Modelli di distribuzione Azure](../azure-classic-rm.md).

## Prima di iniziare

Prima di iniziare la configurazione, verificare che ci siano le condizioni seguenti:

- Un dispositivo VPN compatibile e un utente che sia in grado di configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non si ha familiarità con la configurazione del dispositivo VPN o con gli intervalli di indirizzi IP disponibili nella configurazione di rete locale, sarà necessario coordinarsi con qualcuno in grado di fornire tali dettagli.

- Un indirizzo IP pubblico esterno per il dispositivo VPN. L’indirizzo IP non può trovarsi dietro un NAT.
	
- Una sottoscrizione di Azure. Se non si dispone già di una sottoscrizione di Azure, è possibile attivare i [benefici della sottoscrizione MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

- Cmdlet di Azure PowerShell (1.0 o versione successiva). È possibile scaricare e installare questa versione dalla sezione Windows PowerShell della [pagina di download](http://azure.microsoft.com/downloads/).
	

## 1\. Eseguire la connessione alla sottoscrizione 


Verificare di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

		    Login-AzureRmAccount

Controllare le sottoscrizioni per l'account.

		    Get-AzureRmSubscription 

Specificare la sottoscrizione da usare.

		    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


## 2\. Creare una rete virtuale e una subnet del gateway

- Se è già presente una rete virtuale con una subnet del gateway, è possibile procedere direttamente al **Passaggio 3 - Aggiungere il sito locale**. 
- Se è già presente una rete virtuale e si vuole aggiungere una subnet del gateway alla rete virtuale, vedere [Aggiungere una subnet del gateway a una rete virtuale](#gatewaysubnet).

### Per creare una rete virtuale e una subnet del gateway

Utilizzare l'esempio riportato di seguito per creare una rete virtuale e una subnet del gateway. Sostituire i valori per il proprio.

Creare prima un gruppo di risorse:

	
		New-AzureRmResourceGroup -Name testrg -Location 'West US'

Successivamente, creare la rete virtuale. Verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale.

Nell'esempio seguente viene creata una rete virtuale denominata *testvnet* e due subnet, una chiamata *GatewaySubnet* e l'altra denominata *Subnet1*. È importante creare una subnet denominata specificamente *GatewaySubnet*. Se si assegnano altri nomi, la configurazione della connessione avrà esito negativo.

		$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Per aggiungere una subnet del gateway a una rete virtuale (facoltativo)

Questo passaggio è obbligatorio solo se è necessario aggiungere una subnet del gateway a una rete virtuale.

Se si dispone già di una rete virtuale esistente e si desidera aggiungere una subnet del gateway, è possibile creare quest'ultima tramite l'esempio seguente. Assicurarsi di assegnare alla subnet del gateway il nome 'GatewaySubnet'. Se si assegna un altro nome, si creerà una subnet, ma non verrà visualizzata da Azure come una subnet del gateway.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

A questo punto, impostare la configurazione.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. Aggiungere il sito locale

In una rete virtuale in genere il *sito locale* fa riferimento alla posizione locale. Si assegnerà un nome al sito con cui Azure può fare riferimento a esso.

Verrà inoltre specificato il prefisso dello spazio dell'indirizzo per il sito locale. Azure utilizzerà il prefisso dell'indirizzo IP che viene specificato per identificare il tipo di traffico da inviare al sito locale. Ciò significa che è necessario specificare ogni prefisso di indirizzo che si desidera associare al sito locale. Se la rete locale viene modificata, è possibile aggiornare facilmente questi prefissi.

Quando si usano gli esempi di PowerShell, tenere presente quanto segue:
	
- *GatewayIPAddress* corrisponde all'indirizzo IP del dispositivo VPN locale. Il dispositivo VPN non può trovarsi dietro un NAT. 
- *AddressPrefix* corrisponde allo spazio degli indirizzi locale.

Per aggiungere un sito locale con un solo prefisso di indirizzo:

		New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Per aggiungere un sito locale con più prefissi di indirizzo:

		New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Per modificare i prefissi di indirizzo IP per il sito locale

Talvolta è possibile modificare i prefissi del sito locale. I passaggi necessari per modificare i prefissi di indirizzo IP dipendono dal fatto che sia stata creata o meno una connessione di gateway VPN. Vedere [Modificare i prefissi di indirizzo IP per un sito locale](#to-modify-ip-address-prefixes-for-a-local-site).


## 4\. Richiedere un indirizzo IP pubblico per il gateway

Successivamente, si richiederà un indirizzo IP pubblico da allocare per il gateway VPN di rete virtuale di Azure. Questo non è lo stesso indirizzo IP assegnato al dispositivo VPN, ma viene assegnato al gateway VPN di Azure stesso. Non è possibile specificare l'indirizzo IP che si desidera usare; questo viene allocato in modo dinamico al gateway. Si utilizzerà questo indirizzo IP quando si configura il dispositivo VPN locale per la connessione al gateway.

Utilizzare l'esempio di PowerShell riportato di seguito. Il metodo di allocazione per questo indirizzo deve essere dinamico.

		$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\. Creare la configurazione di indirizzamento IP gateway

La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Per creare la configurazione del gateway, usare l'esempio seguente.


		$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Creare il gateway

In questo passaggio si creerà il gateway di rete virtuale. Si noti che la creazione di un gateway richiede alcuni minuti.

Usare i valori seguenti:

- Il tipo di gateway è *VPN*.
- Il tipo di VPN può essere basato su route* (definito Gateway dinamico in alcuni documenti) o *basato su criteri* (definito Gateway statico in alcuni documenti). Per altre informazioni sui tipi di gateway VPN, vedere [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\. Configurare il dispositivo VPN

A questo punto, è necessario l'indirizzo IP pubblico del gateway della rete virtuale per la configurazione del dispositivo VPN locale. Utilizzare il produttore del dispositivo per le informazioni di configurazione specifiche. Per altre informazioni, vedere anche [Dispositivi VPN](http://go.microsoft.com/fwlink/p/?linkid=615099).

Per trovare l'indirizzo IP pubblico del gateway di rete virtuale, utilizzare l'esempio seguente:

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Creare la connessione VPN

Successivamente, verrà creata la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN. Assicurarsi di sostituire i valori per il proprio. La chiave condivisa deve corrispondere al valore utilizzato per la configurazione del dispositivo VPN.

		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

La connessione verrà stabilita in breve tempo.

## 9\. Verificare una connessione VPN

A questo punto, le connessioni VPN da sito a sito create con Gestione risorse non sono visibili nel portale di anteprima. Tuttavia, è possibile verificare che la connessione abbia avuto esito positivo usando *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. In futuro, per eseguire questa operazione sarà disponibile un cmdlet, nonché la possibilità di visualizzare la connessione nel portale di anteprima.

Per configurare i valori in modo che corrispondano ai propri, è possibile usare l'esempio di cmdlet seguente. Quando richiesto, selezionare *A* per eseguirli tutti.

		Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Al termine, scorrere i cmdlet per visualizzare i valori. Nell'esempio seguente lo stato di connessione risulta *Connesso* ed è possibile visualizzare i byte in ingresso e in uscita.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }


## Per modificare i prefissi di indirizzo IP per un sito locale

Se è necessario modificare i prefissi per il sito locale, usare le istruzioni seguenti. Vengono forniti due set di istruzioni che dipendono dal fatto di aver già creato la connessione del gateway VPN.

### Aggiungere o rimuovere i prefissi senza una connessione del gateway VPN


- **Per aggiungere** prefissi di indirizzi aggiuntivi a un sito locale creato, ma che non dispone ancora di una connessione di gateway VPN, usare l'esempio seguente.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Per rimuovere** un prefisso di indirizzo da un sito locale che non dispone di una connessione VPN, usare l'esempio seguente. Escludere i prefissi non più necessari. In questo esempio, non è più necessario il prefisso 20.0.0.0/24 (dell'esempio precedente). In tal modo, sarà possibile aggiornare il sito locale ed escludere tale prefisso.

		local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Aggiungere o rimuovere i prefissi con una connessione del gateway VPN

Se è stata creata la connessione VPN e si desidera aggiungere o rimuovere i prefissi di indirizzo IP contenuti nel sito locale, è necessario eseguire i passaggi seguenti nell'ordine riportato. Ciò comporterà periodi di inattività per la connessione VPN, in quanto sarà necessario rimuovere e ricreare il gateway. Tuttavia, poiché è stato richiesto un indirizzo IP per la connessione, non sarà necessario riconfigurare il router VPN locale a meno che non si decide di modificare i valori usati in precedenza.

 
1. Rimuovere la connessione del gateway. 
2. Modificare i prefissi per il sito locale. 
3. Creare una nuova connessione del gateway. 

È possibile usare l'esempio seguente come linea guida.


		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureRmVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## Passaggi successivi

Aggiungere una macchina virtuale alla rete virtuale. [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_1203_2015-->