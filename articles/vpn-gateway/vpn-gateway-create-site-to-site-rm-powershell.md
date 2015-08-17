<properties
   pageTitle="Creare una rete virtuale con una connessione VPN da sito a sito utilizzando Gestione risorse di Azure e PowerShell | Microsoft Azure"
   description="Creare una connessione VPN da sito a sito dalla rete virtuale al percorso locale utilizzando Gestione risorse di Azure e PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="cherylmc"/>

# Creare una rete virtuale con una connessione VPN da sito a sito utilizzando Gestione risorse di Azure e PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


In questo argomento verrà illustrata la creazione di una rete virtuale di gestione risorse di Azure e una connessione VPN da sito a sito alla rete locale.

Azure dispone attualmente di due modelli di distribuzione: il modello di distribuzione classico e il modello di distribuzione di gestione risorse di Azure. Il programma di installazione di sito a sito è diverso, a seconda del modello utilizzato per distribuire la rete virtuale. Queste istruzioni sono valide per Gestione risorse. Se si desidera creare una connessione di gateway VPN da sito a sito utilizzando il modello di distribuzione classica, vedere[Creare una connessione VPN da sito a sito nel portale di gestione](vpn-gateway-site-to-site-create.md).


## Prima di iniziare

Prima di iniziare la configurazione, verificare che siano soddisfatti i prerequisiti seguenti:

- Un dispositivo VPN compatibile (e un utente che è in grado di configurarlo). Vedere [Informazioni sui dispositivi VPN](vpn-gateway-vpn-devices.md).
- Un indirizzo IP pubblico esterno per il dispositivo VPN. L’indirizzo IP non può trovarsi dietro un NAT.
- La versione più recente dei cmdlets di Azure PowerShell. È possibile scaricare e installare la versione più recente dalla sezione Windows PowerShell della [Pagina di download](http://azure.microsoft.com/downloads/). 
- Una sottoscrizione di Azure. Se non si dispone già di una sottoscrizione di Azure, è possibile attivare i [benefici della sottoscrizione MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).
	

## Eseguire la connessione alla sottoscrizione 


Aprire la console di PowerShell e passare alla modalità di gestione risorse di Azure. Per connettersi, utilizzare l'esempio seguente:

		Add-AzureAccount

Eseguire Select-AzureSubscription per connettersi alla sottoscrizione da usare.

		Select-AzureSubscription "yoursubscription"

Passare alla modalità ARM. Verrà attivata la modalità che consente di utilizzare i cmdlet ARM.

		Switch-AzureMode -Name AzureResourceManager


## Creare una rete virtuale e una subnet del gateway

- Se si dispone già di una rete virtuale con una subnet del gateway, è possibile passare direttamente a[Aggiungi sito locale](#add-your-local-site). 
- Se si dispone di una rete virtuale e si desidera aggiungere una subnet del gateway della rete virtuale, vedere[Aggiungere una subnet del gateway a una rete virtuale](#gatewaysubnet).

### Per creare una rete virtuale e una subnet del gateway

Utilizzare l'esempio riportato di seguito per creare una rete virtuale e una subnet del gateway. Sostituire i valori per il proprio.

Creare un gruppo di risorse

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

Successivamente, creare la rete virtuale. Nell'esempio seguente viene creata una rete virtuale denominata*testvnet*e due subnet, una chiamata*GatewaySubnet*e l'altra denominata*Subnet1*. È importante creare una subnet denominata specificamente*GatewaySubnet*. Se si assegnano altri nomi, la configurazione della connessione avrà esito negativo.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2


### <a name="gatewaysubnet"></a>Per aggiungere una subnet del gateway a una rete virtuale

Se si dispone già di una rete virtuale esistente e si desidera aggiungere una subnet del gateway, è possibile creare la subnet del gateway tramite l'esempio riportato di seguito. Assicurarsi di assegnare alla subnet del gateway il nome 'GatewaySubnet'. Se si assegnano altri nomi, la configurazione VPN non funzionerà come previsto.


	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## Aggiungere il sito locale

In una rete virtuale, il*sito locale*in genere fa riferimento al percorso locale. Si assegnerà un nome al sito con cui Azure può fare riferimento a esso.

Verrà inoltre specificato il prefisso dello spazio dell'indirizzo per il sito locale. Azure utilizzerà il prefisso dell'indirizzo IP che viene specificato per identificare il tipo di traffico da inviare al sito locale. Ciò significa che è necessario specificare ogni prefisso di indirizzo che si desidera associare al sito locale. Se la rete locale viene modificata, è possibile aggiornare facilmente questi prefissi. Utilizzare gli esempi di PowerShell seguenti per specificare il sito locale.

	
- Il*GatewayIPAddress*è l'indirizzo IP del dispositivo VPN locale. Il dispositivo VPN non può trovarsi dietro un NAT. 
- L’*AddressPrefix*è lo spazio di indirizzo locale.

Utilizzare questo esempio per aggiungere il sito locale:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

## Richiedere un indirizzo IP pubblico del gateway di rete virtuale

Successivamente, si richiederà un indirizzo IP pubblico da allocare per il gateway VPN di rete virtuale di Azure. Questo non è lo stesso indirizzo IP assegnato al dispositivo VPN, ma viene assegnato al gateway VPN di Azure stesso. Non è possibile specificare l'indirizzo IP che si desidera utilizzare. viene allocato in modo dinamico al gateway. Si utilizzerà questo indirizzo IP quando si configura il dispositivo VPN locale per la connessione al gateway.

Utilizzare l'esempio di PowerShell riportato di seguito. Il metodo di allocazione per questo indirizzo deve essere dinamico.

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## Creare la configurazione di indirizzamento IP gateway

La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Utilizzare l'esempio riportato di seguito per creare la configurazione del gateway.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## Creare il gateway

In questo passaggio si creerà il gateway di rete virtuale. Utilizzare i valori seguenti:

- Il tipo di Gateway è*Vpn*.
- Il VpnType può essere RouteBased * (definito Gateway dinamico in alcuni documenti), o*Policy Based*(detto Gateway statico in alcuni documenti). Per ulteriori informazioni sui tipi di gateway VPN, vedere[Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## Configurare il dispositivo VPN

A questo punto, è necessario l'indirizzo IP pubblico del gateway della rete virtuale per la configurazione del dispositivo VPN locale. Utilizzare il produttore del dispositivo per le informazioni di configurazione specifiche. Inoltre, fare riferimento a[Dispositivi VPN](http://go.microsoft.com/fwlink/p/?linkid=615099)per ulteriori informazioni.

Per trovare l'indirizzo IP pubblico del gateway di rete virtuale, utilizzare l'esempio seguente:

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## Creare la connessione VPN

Successivamente, verrà creata la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN. Assicurarsi di sostituire i valori per il proprio. La chiave condivisa deve corrispondere al valore utilizzato per la configurazione del dispositivo VPN.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Dopo alcuni minuti, la connessione dovrebbe stabilirsi. A questo punto, le connessioni VPN da sito a sito creato con Gestione risorse non sono visibili nel portale.


## Passaggi successivi

Aggiungere una macchina virtuale alla rete virtuale. [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=August15_HO6-->