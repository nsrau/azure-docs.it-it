<properties 
   pageTitle="Informazioni sui gateway VPN per la connettività cross-premises delle reti virtuali | Microsoft Azure"
   description="Informazioni sui gateway VPN, che possono essere usati per connessioni cross-premise da sito a sito per le configurazioni ibride, connessioni di rete virtuale e connessioni da punto a sito."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="cherylmc" />

# Informazioni sui gateway VPN

I gateway VPN, anche detti gateway di rete virtuale di Azure, vengono usati per inviare traffico di rete tra reti virtuali e percorsi locali. Vengono inoltre usati per inviare il traffico tra più reti virtuali in Azure. Le sezioni seguenti illustrano gli elementi correlati a un gateway VPN.

Le istruzioni usate per creare il gateway VPN dipendono dal modello di distribuzione usato per creare la rete virtuale. Ad esempio, se la rete virtuale è stata creata usando il modello di distribuzione classica, per creare e configurare il gateway VPN si useranno le linee guida e le istruzioni per il modello di distribuzione classica. Non è possibile creare un gateway VPN di Azure Resource Manager per una rete virtuale creata con il modello di distribuzione classica.

Per altre informazioni sui modelli di distribuzione, vedere l'articolo relativo ai [modelli di distribuzione classica e di Azure Resource Manager](../resource-manager-deployment-model.md).


## <a name="gwsub"></a>Subnet gateway

Per configurare un gateway VPN, è necessario prima di tutto creare una subnet del gateway per la rete virtuale. Per poter funzionare correttamente, tutte le subnet del gateway devono essere denominate GatewaySubnet.

Le dimensioni minime della subnet del gateway dipendono interamente dalla configurazione che si vuole creare. Anche se è possibile creare una subnet del gateway pari a /29 per alcune configurazioni, è consigliabile creare una subnet del gateway di /28 o superiore (/28, /27, /26 e così via).

Creando un gateway più grande si evita di imbattersi in limitazioni delle dimensioni del gateway. Ad esempio, se è stato creato un gateway con una subnet del gateway pari a /29 e si vuole creare una configurazione di coesistenza da sito a sito o ExpressRoute, è necessario eliminare il gateway e la subnet del gateway, creare una subnet del gateway di dimensioni pari a /28 o superiore e quindi ricreare il gateway.

Creare una subnet del gateway di dimensioni maggiori fin dall'inizio permette di risparmiare tempo in seguito durante l'aggiunta di nuove funzionalità di configurazione all'ambiente di rete.

L'esempio seguente illustra una subnet del gateway denominata GatewaySubnet. La notazione CIDR specifica /27. Questa dimensione ammette un numero di indirizzi IP sufficiente per la maggior parte delle configurazioni attualmente esistenti.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27


## <a name="gwtype"></a>Tipi di gateway

Il tipo di gateway specifica il modo in cui il gateway stesso si connette ed è un'impostazione di configurazione necessaria per il modello di distribuzione di Azure Resource Manager. Il tipo di gateway non è da confondere con il tipo di VPN, che specifica il tipo di routing per la VPN. I valori disponibili per `-GatewayType` sono:

- VPN
- ExpressRoute


Questo esempio per il modello di distribuzione di Azure Resource Manager specifica -GatewayType come *Vpn*. Quando si crea un gateway, è necessario assicurarsi che il tipo di gateway sia corretto per la configurazione.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="gwsku"></a>SKU del gateway

Quando si crea un gateway VPN, è necessario specificare lo SKU del gateway da usare. Sono disponibili 3 SKU del Gateway VPN:

- Basic
- Standard
- HighPerformance

L'esempio seguente specifica `-GatewaySku` come *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

### Velocità effettiva aggregata stimata per tipo di SKU e di gateway


La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata. I prezzi variano a seconda dello SKU del gateway. Per informazioni sui prezzi, vedere [Gateway VPN Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway/). La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="vpntype"></a>Tipi di VPN

Per il corretto funzionamento, ogni configurazione richiede un tipo di VPN specifico. Se si combinano due configurazioni, ad esempio creando una connessione da sito a sito e una connessione da punto a sito alla stessa rete virtuale, è necessario usare un tipo di VPN che soddisfi i requisiti di entrambe le connessioni. In caso di connessioni da punto a sito e da sito a sito coesistenti, è necessario usare un tipo di VPN basato su route con il modello di distribuzione di Azure Resource Manager oppure un gateway dinamico con la modalità di distribuzione classica.

Durante la creazione della configurazione, selezionare il tipo di VPN necessario per la connessione.

Sono disponibili due tipi di VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Questo esempio per il modello di distribuzione di Azure Resource Manager specifica `-VpnType` come *RouteBased*. Quando si crea un gateway, è necessario assicurarsi che -VpnType sia corretto per la configurazione.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="connectiontype"></a>Tipi di connessione

Ogni configurazione richiede un tipo di connessione specifico. I valori disponibili di PowerShell per Resource Manager per `-ConnectionType` sono:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

Nell'esempio seguente viene creata una connessione da sito a sito che richiede il tipo di connessione "IPsec".

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="lng"></a>Gateway di rete locali

Il gateway di rete locale in genere fa riferimento al percorso locale. Nel modello di distribuzione classica il gateway di rete locale è definito come sito locale. Assegnare un nome e l'indirizzo IP pubblico del dispositivo VPN locale al gateway di rete locale e specificare i prefissi di indirizzo che si trovano nel percorso locale. Azure esamina i prefissi di indirizzo di destinazione per il traffico di rete, consulta la configurazione specificata per il gateway di rete locale e indirizza i pacchetti di conseguenza. È possibile modificare i prefissi di indirizzo in base alle esigenze.



### Modificare i prefissi di indirizzo - Azure Resource Manager

Quando si modificano i prefissi di indirizzo, la procedura varia a seconda che sia già stato creato o meno il gateway VPN. Vedere la sezione [Per modificare i prefissi di indirizzo IP per un gateway di rete locale](vpn-gateway-create-site-to-site-rm-powershell.md#modify) del relativo articolo.

L'esempio seguente mostra come specificare un gateway di rete locale denominato MyOnPremiseWest che contiene due prefissi di indirizzo IP.

	New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')	

### Modificare i prefissi di indirizzo - Distribuzione classica

Per modificare i siti locali quando si usa il modello di distribuzione classica, al momento è possibile usare la pagina di configurazione Reti locali nel portale classico o modificare direttamente il file di configurazione di rete, NETCFG.XML.


## Dispositivi VPN

Assicurarsi che il dispositivo VPN che si intende usare supporti il tipo di VPN richiesto per la configurazione. Per altre informazioni sui dispositivi VPN compatibili, vedere [Informazioni sui dispositivi VPN per le connessioni di gateway VPN](vpn-gateway-about-vpn-devices.md).

## Requisiti del gateway


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## Passaggi successivi

Per altre informazioni prima di procedere con la pianificazione e la progettazione della configurazione, vedere [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md) .





 

<!---HONumber=AcomDC_0330_2016-->