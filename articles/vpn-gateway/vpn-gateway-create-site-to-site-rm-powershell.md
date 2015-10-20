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
   ms.date="10/13/2015"
   ms.author="cherylmc"/>

# Creare una rete virtuale con una connessione VPN da sito a sito mediante PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Questo articolo illustra la creazione di una rete virtuale e di una connessione VPN da sito a sito alla rete locale mediante il modello di distribuzione di Gestione risorse di Azure. È possibile selezionare l'articolo per il modello di distribuzione e lo strumento di distribuzione usando le schede riportate sopra.

>[AZURE.NOTE]Prima di creare una rete virtuale, è importante comprendere che Azure attualmente funziona con due modelli di distribuzione: Gestione risorse e Classico. Prima di iniziare la configurazione, assicurarsi di comprendere i modelli di distribuzione e gli strumenti. Per informazioni sui modelli di distribuzione, vedere [Modelli di distribuzione di Azure](../azure-classic-rm.md).

## Prima di iniziare

Prima di iniziare la configurazione, verificare che siano presenti le condizioni seguenti:

- Un dispositivo VPN compatibile e un utente in grado di configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).

- Un indirizzo IP pubblico esterno per il dispositivo VPN. L'indirizzo IP non può trovarsi dietro un NAT.

>[AZURE.IMPORTANT]Se non si ha familiarità con la configurazione del dispositivo VPN o non si conoscono gli intervalli di indirizzi IP che si trovano nella configurazione della rete locale, è necessario rivolgersi a un esperto in grado di fornire le informazioni necessarie.
	
- Una sottoscrizione di Azure. Se non si dispone già di una sottoscrizione di Azure, è possibile attivare i [benefici della sottoscrizione MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

- La versione più recente dei cmdlet di Azure PowerShell. È possibile scaricare e installare la versione più recente dalla sezione Windows PowerShell della [pagina di download](http://azure.microsoft.com/downloads/). Questo articolo è relativo ad Azure PowerShell *0.9.8*.

>[AZURE.NOTE]Se si eseguono app cruciali, continuare a usare Azure PowerShell 0.9.8. Nella maggior parte dei casi, la sola differenza tra le due versioni è che il nome del cmdlet nell'anteprima 1.0 segue il modello {verb}-AzureRm{noun}, mentre il nome nella versione 0.9.8 non include Rm. Ad esempio, New-AzureRmResourceGroup invece di New-AzureResourceGroup. Per informazioni sull'anteprima di Azure PowerShell 1.0, vedere questo [post di blog](https://azure.microsoft.com/blog/azps-1-0-pre/). Per altre informazioni sui cmdlet dell'anteprima di Azure PowerShell 1.0, vedere [Cmdlet di Gestione risorse di Azure](https://msdn.microsoft.com/library/mt125356.aspx).


## 1\. Eseguire la connessione alla sottoscrizione 


Aprire la console di PowerShell e connettersi al proprio account.

**Nota:** le istruzioni seguenti si basano sulla versione 0.9.8 dei cmdlet di Azure PowerShell.

Per connettersi, usare l'esempio seguente:

		Add-AzureAccount

Se esistono più sottoscrizioni, usare *Select-AzureSubscription* per connettersi alla sottoscrizione da usare.

		Select-AzureSubscription "yoursubscription"

Successivamente, passare alla modalità Gestione risorse di Azure.
		
		Switch-AzureMode -Name AzureResourceManager

## 2\. Creare una rete virtuale e una subnet del gateway

- Se è già presente una rete virtuale con una subnet del gateway, è possibile passare direttamente a **Passaggio 3 - Aggiungere il sito locale**. 
- Se è già presente una rete virtuale e si vuole aggiungere una subnet del gateway della rete virtuale, vedere [Aggiungere una subnet del gateway a una rete virtuale](#gatewaysubnet).

### Per creare una rete virtuale e una subnet del gateway

Per creare una rete virtuale e una subnet del gateway, usare l'esempio seguente. Sostituire i valori per il proprio.

Creare prima un gruppo di risorse:

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

Successivamente, creare la rete virtuale. Verificare che gli spazi degli indirizzi specificati non si sovrappongano agli spazi degli indirizzi presenti nella rete locale.

L'esempio seguente crea una rete virtuale denominata *testvnet* e due subnet, una denominata *GatewaySubnet* e l'altra denominata *Subnet1*. È importante creare una subnet denominata specificamente *GatewaySubnet*. Se si assegnano altri nomi, la configurazione della connessione avrà esito negativo.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Per aggiungere una subnet del gateway a una rete virtuale (facoltativo)

Questo passaggio è obbligatorio solo se è necessario aggiungere una subnet del gateway a una rete virtuale.

Se si dispone già di una rete virtuale esistente e si desidera aggiungere una subnet del gateway, è possibile creare quest'ultima tramite l'esempio seguente. Assicurarsi di assegnare alla subnet del gateway il nome 'GatewaySubnet'. Se si assegna un altro nome, si creerà una subnet, ma non verrà visualizzata da Azure come una subnet del gateway.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

A questo punto, impostare la configurazione.

		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## 3\. Aggiungere il sito locale

In una rete virtuale il *sito locale* in genere fa riferimento al percorso locale. Si assegnerà un nome al sito con cui Azure può fare riferimento a esso.

Verrà inoltre specificato il prefisso dello spazio dell'indirizzo per il sito locale. Azure utilizzerà il prefisso dell'indirizzo IP che viene specificato per identificare il tipo di traffico da inviare al sito locale. Ciò significa che è necessario specificare ogni prefisso di indirizzo che si desidera associare al sito locale. Se la rete locale viene modificata, è possibile aggiornare facilmente questi prefissi.

Quando si usano gli esempi di PowerShell, tenere presente quanto segue:
	
- *GatewayIPAddress* è l'indirizzo IP del dispositivo VPN locale. Il dispositivo VPN non può trovarsi dietro un NAT. 
- *AddressPrefix* è lo spazio degli indirizzi locale.

Per aggiungere un sito locale con un solo prefisso di indirizzo:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Per aggiungere un sito locale con più prefissi di indirizzo:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Per modificare i prefissi di indirizzo IP per il sito locale

Talvolta è possibile modificare i prefissi del sito locale. I passaggi necessari per modificare i prefissi di indirizzo IP dipendono dal fatto che sia stata creata o meno una connessione di gateway VPN. Vedere [Modificare i prefissi di indirizzo IP per un sito locale](#to-modify-ip-address-prefixes-for-a-local-site).


## 4\. Richiedere un indirizzo IP pubblico per il gateway

Successivamente, si richiederà un indirizzo IP pubblico da allocare per il gateway VPN di rete virtuale di Azure. Questo non è lo stesso indirizzo IP assegnato al dispositivo VPN, ma viene assegnato al gateway VPN di Azure stesso. Non è possibile specificare l'indirizzo IP che si desidera usare; questo viene allocato in modo dinamico al gateway. Si utilizzerà questo indirizzo IP quando si configura il dispositivo VPN locale per la connessione al gateway.

Usare l'esempio di PowerShell seguente. Il metodo di allocazione per questo indirizzo deve essere dinamico.

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\. Creare la configurazione di indirizzamento IP gateway

La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Per creare la configurazione del gateway, usare l'esempio seguente.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Creare il gateway

In questo passaggio si creerà il gateway di rete virtuale. Si noti che la creazione di un gateway richiede alcuni minuti.

Usare i valori seguenti:

- Il tipo di gateway è *VPN*.
- VpnType può essere RouteBased* (definito Gateway dinamico in alcuni documenti) o *Policy Based* (definito Gateway statico in alcuni documenti). Per altre informazioni sui tipi di gateway VPN, vedere [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\. Configurare il dispositivo VPN

A questo punto, è necessario l'indirizzo IP pubblico del gateway della rete virtuale per la configurazione del dispositivo VPN locale. Per le informazioni di configurazione specifiche, usare il produttore del dispositivo. Per altre informazioni, vedere anche [Dispositivi VPN](http://go.microsoft.com/fwlink/p/?linkid=615099).

Per trovare l'indirizzo IP pubblico del gateway di rete virtuale, usare l'esempio seguente:

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Creare la connessione VPN

Successivamente, verrà creata la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN. Assicurarsi di sostituire i valori per il proprio. La chiave condivisa deve corrispondere al valore usato per la configurazione del dispositivo VPN.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

La connessione verrà stabilita in breve tempo.

## 9\. Verificare una connessione VPN

A questo punto, le connessioni VPN da sito a sito create con Gestione risorse non sono visibili nel portale di anteprima. Tuttavia, è possibile verificare che la connessione abbia avuto esito positivo usando *Get-AzureVirtualNetworkGatewayConnection –Debug*. In futuro, per eseguire questa operazione sarà disponibile un cmdlet, nonché la possibilità di visualizzare la connessione nel portale di anteprima.

Per configurare i valori in modo che corrispondano ai propri, è possibile usare l'esempio di cmdlet seguente. Quando richiesto, selezionare *A* per eseguirli tutti.

		Get-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Al termine, scorrere i cmdlet per visualizzare i valori. Nell'esempio seguente viene illustrato lo stato di connessione come *connesso* ed è possibile visualizzare i byte in ingresso e in uscita.

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


- **Per aggiungere** prefissi di indirizzi aggiuntivi a un sito locale creato, ma ancora senza una connessione di gateway VPN, usare l'esempio seguente.

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Per rimuovere** un prefisso di indirizzo da un sito locale senza una connessione VPN, usare l'esempio seguente. Escludere i prefissi non più necessari. In questo esempio, non è più necessario il prefisso 20.0.0.0/24 (dell'esempio precedente). In tal modo, sarà possibile aggiornare il sito locale ed escludere tale prefisso.

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Aggiungere o rimuovere i prefissi con una connessione del gateway VPN

Se è stata creata la connessione VPN e si desidera aggiungere o rimuovere i prefissi di indirizzo IP contenuti nel sito locale, è necessario eseguire i passaggi seguenti nell'ordine riportato. Ciò comporterà periodi di inattività per la connessione VPN, in quanto sarà necessario rimuovere e ricreare il gateway. Tuttavia, poiché è stato richiesto un indirizzo IP per la connessione, non sarà necessario riconfigurare il router VPN locale a meno che non si decide di modificare i valori usati in precedenza.

 
1. Rimuovere la connessione del gateway. 
2. Modificare i prefissi per il sito locale. 
3. Creare una nuova connessione del gateway. 

È possibile usare l'esempio seguente come linea guida.


		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## Passaggi successivi

Aggiungere una macchina virtuale alla rete virtuale. [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=Oct15_HO3-->