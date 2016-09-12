<properties 
   pageTitle="Informazioni sulle impostazioni del gateway VPN | Microsoft Azure"
   description="Informazioni sulle impostazioni del gateway VPN per la rete virtuale di Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="cherylmc" />  

# Informazioni sulle impostazioni del gateway VPN

Il gateway VPN è una raccolta di risorse usata per inviare il traffico di rete tra reti virtuali e percorsi locali. È possibile usare il gateway VPN anche per inviare il traffico tra reti virtuali in Azure. Le sezioni di questo articolo illustrano le risorse e le impostazioni correlate al gateway VPN.

L'articolo è utile per visualizzare le configurazioni disponibili tramite i diagrammi di connessione. Nell'articolo [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md) sono disponibili diagrammi che illustrano come distribuire ogni configurazione.


## <a name="gwsku"></a>SKU del gateway

Quando si crea un gateway VPN, è necessario specificare la SKU del gateway da usare. Gli SKU del gateway sono applicabili ai tipi di gateway ExpressRoute e VPN. I prezzi variano a seconda dello SKU del gateway. Per informazioni sui prezzi, vedere [Gateway VPN Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway/). Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

Sono disponibili 3 SKU del Gateway VPN:

- Basic
- Standard
- HighPerformance

Nel seguente esempio di PowerShell `-GatewaySku` viene specificato come *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased


###  <a name="aggthroughput"></a>Velocità effettiva aggregata stimata per tipo di SKU e di gateway


La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata. La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="gwtype"></a>Tipi di gateway

Il tipo di gateway specifica il modo in cui il gateway si connette ed è un'impostazione di configurazione obbligatoria per il modello di distribuzione di Resource Manager. Ogni rete virtuale può avere un solo gateway di rete virtuale per tipo. I valori disponibili per `-GatewayType` sono:

- VPN
- ExpressRoute


Questo esempio di PowerShell per il modello di distribuzione di Resource Manager specifica -GatewayType come *Vpn*. Quando si crea un gateway, è necessario assicurarsi che il tipo di gateway sia corretto per la configurazione.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased

## <a name="connectiontype"></a>Tipi di connessione

Ogni configurazione richiede un tipo di connessione specifico. I valori di PowerShell per Resource Manager disponibili per `-ConnectionType` sono:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

Nel seguente esempio di PowerShell, viene creata una connessione S2S che richiede il tipo di connessione "IPsec".

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipi di VPN

Per il corretto funzionamento, ogni configurazione richiede un tipo di VPN specifico. Durante la creazione della configurazione, selezionare il tipo di VPN richiesto per la connessione.

Ad esempio, se si desidera connettersi a una rete virtuale tramite connessione da sito a sito (S2S) e da punto a sito (P2S), è necessario usare un tipo di VPN che soddisfi i requisiti di connessione per entrambe le configurazioni. Le connessioni P2S richiedono un tipo di VPN basato su routing (gateway con routing dinamico), mentre le connessioni S2S talvolta possono supportare un tipo di VPN basato su criteri. Ciò significa se si dispone già di una connessione S2S che usa un tipo di VPN basato su criteri (routing statico), è necessario ricreare il gateway usando il tipo di VPN basato su routing (routing dinamico) per supportare P2S.

Sono disponibili due tipi di VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]



Questo esempio di PowerShell per il modello di distribuzione di Resource Manager specifica `-VpnType` come *RouteBased*. Quando si crea un gateway, è necessario assicurarsi che -VpnType sia corretto per la configurazione.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisiti del gateway

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Subnet del gateway

Per configurare un gateway VPN, è necessario prima di tutto creare una subnet del gateway per la rete virtuale. Per poter funzionare correttamente, la subnet del gateway deve essere denominata *GatewaySubnet*. Questo nome consente ad Azure di sapere che la subnet deve essere usata per il gateway.<BR>Se si usa il portale classico, la subnet del gateway viene automaticamente denominata *Gateway* nell'interfaccia del portale. Questa operazione serve a visualizzare la subnet del gateway solo nel portale classico. In questo caso, la subnet viene effettivamente creata con il valore *GatewaySubnet* e può essere visualizzata così nel portale di Azure e in PowerShell.

Le dimensioni minime della subnet del gateway dipendono interamente dalla configurazione che si vuole creare. Anche se è possibile creare una subnet del gateway pari a/29, è consigliabile crearne una di /28 o superiore (/ 28, /27, /26 e così via.).

Creando un gateway più grande si evita di imbattersi in limitazioni delle dimensioni del gateway. Un gateway di rete virtuale, ad esempio, potrebbe essere stato creato con una dimensione della subnet del gateway pari a /29 per una connessione S2S. Ora si desidera configurare una connessione con coesistenza S2S/ExpressRoute. La configurazione richiede una subnet del gateway di dimensioni minime pari a /28. Per creare la configurazione, è necessario modificare la subnet del gateway in modo da soddisfare il requisito minimo per la connessione, ovvero /28.

Il seguente esempio di PowerShell illustra una subnet del gateway denominata GatewaySubnet. La notazione CIDR specifica /27. Questa dimensione ammette un numero di indirizzi IP sufficiente per la maggior parte delle configurazioni attualmente esistenti.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Verificare che alla subnet del gateway non sia applicato un gruppo di sicurezza di rete (NSG), perché questo può causare l'esito negativo delle connessioni.


## <a name="lng"></a>Gateway di rete locali

Il gateway di rete locale in genere fa riferimento al percorso locale. Nel modello di distribuzione classica il gateway di rete locale è definito come sito locale. Assegnare un nome e l'indirizzo IP pubblico del dispositivo VPN locale al gateway di rete locale e specificare i prefissi di indirizzo che si trovano nel percorso locale. Azure esamina i prefissi di indirizzo di destinazione per il traffico di rete, consulta la configurazione specificata per il gateway di rete locale e indirizza i pacchetti di conseguenza.

Talvolta è necessario modificare le impostazioni di gateway di rete locale. Ad esempio, quando si aggiunge o si modifica l'intervallo di indirizzi oppure se viene modificato l'indirizzo IP del dispositivo VPN. Per una rete virtuale classica, è possibile modificare queste impostazioni nella pagina relativa alle reti locali del portale classico. Per Resource Manager, vedere [Modificare le impostazioni del gateway di rete locale usando PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>API REST e PowerShell

Per ulteriori risorse tecniche e altri requisiti di sintassi specifici quando si usano le API REST e PowerShell, vedere le pagine seguenti:

|**Classico** | **Gestione risorse**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## Passaggi successivi

Per altre informazioni sulle configurazioni delle connessioni disponibili, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).







 

<!---HONumber=AcomDC_0831_2016-->