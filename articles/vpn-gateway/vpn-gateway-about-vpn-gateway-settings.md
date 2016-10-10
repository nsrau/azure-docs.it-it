<properties 
   pageTitle="Informazioni sulle impostazioni del gateway VPN per i gateway di reti virtuali | Microsoft Azure"
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
   ms.date="09/21/2016"
   ms.author="cherylmc" />  

# Informazioni sulle impostazioni del gateway VPN

Una soluzione per la connessione di gateway VPN si basa sulla configurazione di più risorse per l'invio di traffico di rete tra le reti virtuali e i percorsi locali. Ogni risorsa contiene impostazioni configurabili. La combinazione di impostazioni e risorse determina il risultato della connessione.

Le sezioni di questo articolo descrivono le risorse e le impostazioni correlate a un gateway VPN nel modello di distribuzione **Resource Manager**. L'articolo è utile per visualizzare le configurazioni disponibili tramite i diagrammi di topologia della connessione. Le descrizioni e i diagrammi della topologia per ogni soluzione di connessione sono disponibili nell'articolo [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwtype"></a>Tipi di gateway

Ogni rete virtuale può avere un solo gateway di rete virtuale per tipo. Quando si crea un gateway di rete virtuale, è necessario assicurarsi che il tipo di gateway sia corretto per la configurazione in uso.

I valori disponibili per GatewayType sono:

- VPN
- ExpressRoute

Un gateway VPN richiede il valore *Vpn* per `-GatewayType`.

Esempio:

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased
 

## <a name="gwsku"></a>SKU del gateway


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

**Specificare lo SKU del gateway nel portale di Azure**

Se si usa il portale di Azure per creare un gateway di rete virtuale di Resource Manager, per impostazione predefinita tale gateway viene configurato usando lo SKU standard. Attualmente, non è possibile specificare altri SKU per il modello di distribuzione Resource Manager nel portale di Azure. Tuttavia, dopo aver creato il gateway, è possibile effettuare l'aggiornamento a uno SKU di gateway più potente (ad esempio, da Basic/Standard a HighPerformance) usando il cmdlet PowerShell `Resize-AzureRmVirtualNetworkGateway`. È anche possibile eseguire il downgrade delle dimensioni dello SKU del gateway tramite PowerShell.

**Specificare lo SKU del gateway tramite PowerShell**


Nel seguente esempio di PowerShell `-GatewaySku` viene specificato come *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased

**Modificare uno SKU del gateway**

È possibile ridimensionare uno SKU del gateway. L'esempio di PowerShell seguente illustra uno SKU del gateway che viene ridimensionato su HighPerformance.

	$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

<br> La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata. La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]


## <a name="connectiontype"></a>Tipi di connessione

Nel modello di distribuzione Resource Manager ogni configurazione richiede un tipo di connessione di gateway di rete virtuale specifico. I valori di PowerShell per Resource Manager disponibili per `-ConnectionType` sono:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

L'esempio PowerShell seguente crea una connessione S2S che richiede il tipo di connessione *IPsec*.

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipi di VPN

Quando si crea il gateway di rete virtuale per una configurazione di gateway VPN, è necessario specificare un tipo di VPN. Il tipo di VPN scelto dipende dalla topologia di connessione che si desidera creare. Ad esempio, una connessione P2S richiede un tipo di VPN RouteBased. Un tipo VPN può anche dipendere dall'hardware che verrà usato. Le configurazioni S2S richiedono un dispositivo VPN. Alcuni dispositivi VPN supportano solo un determinato tipo di VPN.

Il tipo di VPN selezionato deve soddisfare tutti i requisiti di connessione per la soluzione da creare. Ad esempio, per creare una connessione di gateway VPN S2S, una connessione di gateway VPN P2S sulla stessa rete virtuale, usare il tipo *RouteBased* poiché P2S richiede questo tipo specifico. È inoltre necessario verificare che il dispositivo VPN supporti una connessione VPN di tipo RouteBased.

Dopo avere creato un gateway di rete virtuale, non è possibile modificare il tipo di VPN. È necessario eliminare il gateway di rete virtuale e crearne uno nuovo. Sono disponibili due tipi di VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


Nel seguente esempio di PowerShell `-VpnType` viene specificato come *RouteBased*. Quando si crea un gateway, è necessario assicurarsi che -VpnType sia corretto per la configurazione.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisiti del gateway

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Subnet del gateway

Per configurare un gateway di rete virtuale, è necessario prima creare una subnet del gateway per la rete virtuale. Per poter funzionare correttamente, la subnet del gateway deve essere denominata *GatewaySubnet*. Questo nome indica ad Azure che questa subnet deve essere usata per il gateway.

Le dimensioni minime della subnet del gateway dipendono interamente dalla configurazione che si vuole creare. Anche se è possibile creare una subnet del gateway pari a/29, è consigliabile crearne una di /28 o superiore (/ 28, /27, /26 e così via.).

Creando un gateway più grande si evita di imbattersi in limitazioni delle dimensioni del gateway. Un gateway di rete virtuale, ad esempio, potrebbe essere stato creato con una dimensione della subnet del gateway pari a /29 per una connessione S2S. Ora si desidera configurare una connessione con coesistenza S2S/ExpressRoute. La configurazione richiede una subnet del gateway di dimensioni minime pari a /28. Per creare la configurazione, è necessario modificare la subnet del gateway in modo da soddisfare il requisito minimo per la connessione, ovvero /28.

L'esempio seguente di PowerShell Resource Manager illustra una subnet del gateway denominata GatewaySubnet. La notazione CIDR specifica /27. Questa dimensione ammette un numero di indirizzi IP sufficiente per la maggior parte delle configurazioni attualmente esistenti.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Verificare che alla subnet del gateway non sia applicato un gruppo di sicurezza di rete (NSG), perché questo può causare l'esito negativo delle connessioni.


## <a name="lng"></a>Gateway di rete locali

Quando si crea una configurazione per il gateway VPN, il gateway di rete locale rappresenta spesso la posizione locale. Nel modello di distribuzione classica il gateway di rete locale è definito come sito locale.

Assegnare un nome e l'indirizzo IP pubblico del dispositivo VPN locale al gateway di rete locale e specificare i prefissi di indirizzo che si trovano nel percorso locale. Azure esamina i prefissi di indirizzo di destinazione per il traffico di rete, consulta la configurazione specificata per il gateway di rete locale e indirizza i pacchetti di conseguenza. È anche possibile specificare i gateway di rete locale per le configurazioni da rete virtuale a rete virtuale che usano una connessione di gateway VPN.

L'esempio seguente di PowerShell consente di creare un nuovo gateway di rete locale:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
	-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Talvolta è necessario modificare le impostazioni di gateway di rete locale. Ad esempio, quando si aggiunge o si modifica l'intervallo di indirizzi oppure se viene modificato l'indirizzo IP del dispositivo VPN. Per una rete virtuale classica, è possibile modificare queste impostazioni nella pagina relativa alle reti locali del portale classico. Per Resource Manager, vedere [Modificare le impostazioni del gateway di rete locale usando PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>API REST e cmdlet PowerShell

Per altre risorse tecniche e requisiti di sintassi specifici quando si usano le API REST e i cmdlet PowerShell per le configurazioni di Gateway VPN, vedere le pagine seguenti:

|**Classico** | **Gestione risorse**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## Passaggi successivi

Per altre informazioni sulle configurazioni delle connessioni disponibili, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).







 

<!---HONumber=AcomDC_0928_2016-->