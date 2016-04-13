<properties
   pageTitle="Creare una rete virtuale con una connessione VPN da sito a sito usando PowerShell e Azure Resource Manager | Microsoft Azure"
   description="Questo articolo illustra la creazione di una rete virtuale con il modello di Gestione risorse e la connessione alla rete locale con una connessione del gateway VPN da sito a sito."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/16/2016"
   ms.author="cherylmc"/>

# Creare una rete virtuale con una connessione VPN da sito a sito usando PowerShell e Azure Resource Manager

> [AZURE.SELECTOR]
- [Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portale di Azure - Classico](vpn-gateway-site-to-site-create.md)
- [PowerShell - Gestione risorse](vpn-gateway-create-site-to-site-rm-powershell.md)

Questo articolo illustra la creazione di una rete virtuale e di una connessione VPN da sito a sito alla rete locale con il modello di distribuzione di **Azure Resource Manager**. Le connessioni da sito a sito possono essere usate per le configurazioni cross-premise e ibride.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

![Diagramma da sito a sito](./media/vpn-gateway-create-site-to-site-rm-powershell/site2site.png "Da sito a sito")

**Strumenti e modelli di distribuzione per le connessioni da sito a sito**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## Prima di iniziare

Prima di iniziare la configurazione, verificare che ci siano le condizioni seguenti:

- Un dispositivo VPN compatibile e un utente che sia in grado di configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non si ha familiarità con la configurazione del dispositivo VPN o con gli intervalli di indirizzi IP disponibili nella configurazione di rete locale, sarà necessario coordinarsi con qualcuno in grado di fornire tali dettagli.

- Un indirizzo IP pubblico esterno per il dispositivo VPN. L’indirizzo IP non può trovarsi dietro un NAT.
	
- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi dell'abbonamento a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
	
- È necessario installare la versione più recente dei cmdlet di PowerShell per Gestione risorse di Azure. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## 1\. Eseguire la connessione alla sottoscrizione 

Verificare di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

	Login-AzureRmAccount

Controllare le sottoscrizioni per l'account.

	Get-AzureRmSubscription 

Specificare la sottoscrizione da usare.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2\. Creare una rete virtuale e una subnet del gateway

I seguenti esempi mostrano una subnet del gateway di /28. Anche se è possibile, non è consigliabile creare una subnet del gateway più piccola di /29. È consigliabile creare una subnet del gateway con dimensioni di /27 o superiori (/26, /25 e così via) per soddisfare i requisiti di funzionalità aggiuntive. Se si ha già una rete virtuale con una subnet del gateway con dimensioni di /29 o superiori, è possibile procedere direttamente al [Passaggio 3: Aggiungere il gateway di rete locale](#localnet).

### Per creare una rete virtuale e una subnet del gateway

Utilizzare l'esempio riportato di seguito per creare una rete virtuale e una subnet del gateway. Sostituire i valori per il proprio.

Creare prima un gruppo di risorse:
	
	New-AzureRmResourceGroup -Name testrg -Location 'West US'

Successivamente, creare la rete virtuale. Verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale.

Nell'esempio seguente viene creata una rete virtuale denominata *testvnet* e due subnet, una denominata *GatewaySubnet* e l'altra denominata *Subnet1*. È importante creare una subnet denominata specificamente *GatewaySubnet*. Se si assegnano altri nomi, la configurazione della connessione avrà esito negativo.

	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
	$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
	New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Per aggiungere una subnet del gateway a una rete virtuale già creata

Questo passaggio è obbligatorio solo se è necessario aggiungere una subnet del gateway a una rete virtuale creata in precedenza.

È possibile creare la subnet del gateway con l'esempio riportato di seguito. Assicurarsi di assegnare alla subnet del gateway il nome 'GatewaySubnet'. Se si assegna un nome diverso, la subnet verrà creata ma non verrà trattata da Azure come una subnet del gateway.

	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

A questo punto, impostare la configurazione.

	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. <a name="localnet"></a>Aggiungere il gateway di rete locale

In una rete virtuale il gateway di rete locale in genere fa riferimento al percorso locale. Assegnare al sito un nome che Azure possa usare come riferimento e specificare il prefisso dello spazio dell'indirizzo per il gateway di rete locale.

Azure usa il prefisso di indirizzo IP che viene specificato per identificare il traffico da inviare al percorso locale. Ciò significa che è necessario specificare ogni prefisso di indirizzo da associare al gateway di rete locale. Se la rete locale viene modificata, è possibile aggiornare facilmente questi prefissi.

Quando si usano gli esempi di PowerShell, tenere presente quanto segue:
	
- *GatewayIPAddress* corrisponde all'indirizzo IP del dispositivo VPN locale. Il dispositivo VPN non può trovarsi dietro un NAT. 
- *AddressPrefix* corrisponde allo spazio degli indirizzi locale.

Per aggiungere un gateway di rete locale con un solo prefisso di indirizzo:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Per aggiungere un gateway di rete locale con più prefissi di indirizzo:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Per modificare i prefissi di indirizzo IP per il gateway di rete locale

Talvolta è possibile modificare i prefissi del gateway di rete locale. I passaggi necessari per modificare i prefissi di indirizzo IP dipendono dal fatto che sia stata creata o meno una connessione di gateway VPN. Vedere la sezione [Per modificare i prefissi di indirizzo IP per un gateway di rete locale](#modify) di questo articolo.


## 4\. Richiedere un indirizzo IP pubblico del gateway VPN

Successivamente, si richiederà un indirizzo IP pubblico da allocare per il gateway VPN di rete virtuale di Azure. Non si tratta dello stesso indirizzo IP assegnato al dispositivo VPN, ma viene assegnato al gateway VPN di Azure stesso. Non è possibile specificare l'indirizzo IP che si desidera usare; questo viene allocato in modo dinamico al gateway. Si utilizzerà questo indirizzo IP quando si configura il dispositivo VPN locale per la connessione al gateway.

Utilizzare l'esempio di PowerShell riportato di seguito. Il metodo di allocazione per questo indirizzo deve essere dinamico.

	$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

>[AZURE.NOTE] Il gateway VPN di Azure per il modello di distribuzione di Gestione risorse supporta attualmente solo indirizzi IP pubblici usando il metodo di allocazione dinamica. Ciò non significa però che l'indirizzo IP verrà modificato. L'indirizzo IP del gateway VPN di Azure viene modificato solamente quando il gateway viene eliminato e ricreato. L'indirizzo IP pubblico del gateway non cambierà in caso di ridimensionamento, reimpostazione o altri aggiornamenti o manutenzioni interne del gateway VPN di Azure.

## 5\. Creare la configurazione di indirizzamento IP gateway

La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Per creare la configurazione del gateway, usare l'esempio seguente.

	$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Creare il gateway di rete virtuale

In questo passaggio si creerà il gateway di rete virtuale. Si noti che la creazione di un gateway può richiedere molto tempo. Spesso anche oltre 20 minuti.

Usare i valori seguenti:

- Il valore di **-GatewayType** per una configurazione da sito a sito è **Vpn**. Il tipo di gateway è sempre specifico della configurazione che si sta implementando. Ad esempio, altre configurazioni di gateway possono richiedere -GatewayType ExpressRoute. 

- Il valore di **-VpnType** può essere **RouteBased**, definito Gateway dinamico in alcuni documenti, o **PolicyBased**, definito Gateway statico in alcuni documenti. Per altre informazioni sui tipi di gateway VPN, vedere [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md#vpntype).
- Il valore di **-GatewaySku** può essere **Basic**, **Standard** o **HighPerformance**. 	

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

## 7\. Configurare il dispositivo VPN

A questo punto, è necessario l'indirizzo IP pubblico del gateway della rete virtuale per la configurazione del dispositivo VPN locale. Utilizzare il produttore del dispositivo per le informazioni di configurazione specifiche. Per altre informazioni, vedere anche [Dispositivi VPN](vpn-gateway-about-vpn-devices.md).

Per trovare l'indirizzo IP pubblico del gateway di rete virtuale, utilizzare l'esempio seguente:

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Creare la connessione VPN

Successivamente, verrà creata la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN. Assicurarsi di sostituire i valori con i propri. La chiave condivisa deve corrispondere al valore utilizzato per la configurazione del dispositivo VPN. Si noti che `-ConnectionType` per la connessione da sito a sito è **IPsec**.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

La connessione verrà stabilita in breve tempo.

## 9\. Verificare una connessione VPN

Esistono diversi modi per verificare la connessione VPN. Di seguito viene illustrato come eseguire una verifica di base usando il portale di Azure e PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Per modificare i prefissi di indirizzo IP per un gateway di rete locale

Se è necessario modificare i prefissi per il gateway di rete locale, usare le istruzioni riportate di seguito. Sono disponibili due set di istruzioni. La scelta delle istruzioni dipende dal fatto che sia stata già creata o meno la connessione del gateway VPN.

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]


## Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

<!-----HONumber=AcomDC_0406_2016-->