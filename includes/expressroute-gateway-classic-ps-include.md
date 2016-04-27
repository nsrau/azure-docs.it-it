Prima di eseguire le attività seguenti è necessario creare una rete virtuale e una subnet del gateway. Vedere l'articolo [Configurare una rete virtuale usando il portale classico](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) per altre informazioni.

## Aggiungere un gateway

Usare il comando seguente per creare un gateway. Sostituire i valori presenti con i valori desiderati.

	New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard

## Verificare che il gateway sia stato creato

Utilizzare il comando seguente per verificare che il gateway sia stato creato. Questo comando recupera l'ID del gateway, necessario per le altre operazioni.

	Get-AzureVirtualNetworkGateway

## Ridimensionare un gateway

Sono disponibili tre [SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md). È possibile usare il comando seguente per modificare la SKU del gateway in qualsiasi momento.

	Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## Rimuovere un gateway

Usare il comando seguente per rimuovere un gateway

	Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>

<!---HONumber=AcomDC_0413_2016-->