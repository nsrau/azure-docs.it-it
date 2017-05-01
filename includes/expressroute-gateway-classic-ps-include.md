Prima di eseguire le attività seguenti è necessario creare una rete virtuale e una subnet del gateway. Vedere l'articolo [Configurare una rete virtuale usando il portale classico](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) per altre informazioni.   

## <a name="add-a-gateway"></a>Aggiungere un gateway
Usare il comando seguente per creare un gateway. Sostituire i valori presenti con i valori desiderati.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Verificare che il gateway sia stato creato
Utilizzare il comando seguente per verificare che il gateway sia stato creato. Questo comando recupera l'ID del gateway, necessario per le altre operazioni.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Ridimensionare un gateway
Esistono diversi [SKU del gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). È possibile usare il comando seguente per modificare la SKU del gateway in qualsiasi momento.

> [!IMPORTANT]
> Questo comando non funziona per il gateway UltraPerformance. Per modificare il gateway in un gateway UltraPerformance, innanzitutto rimuovere il gateway ExpressRoute esistente, quindi creare un nuovo gateway UltraPerformance. Per effettuare il downgrade del gateway da un gateway UltraPerformance, innanzitutto rimuovere il gateway UltraPerformance, quindi creare un nuovo gateway. 
> 
> 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Rimuovere un gateway
Usare il comando seguente per rimuovere un gateway

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>