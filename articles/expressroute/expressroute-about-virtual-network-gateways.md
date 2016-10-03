<properties 
   pageTitle="Informazioni sui gateway di rete virtuale per ExpressRoute | Microsoft Azure"
   description="Informazioni sui gateway di rete virtuale per ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/> 
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2016"
   ms.author="cherylmc" /> 

# Informazioni sui gateway di rete virtuale per ExpressRoute


Il gateway di rete virtuale viene usato per inviare il traffico di rete tra le reti virtuali di Azure e i percorsi locali. Quando si configura una connessione ExpressRoute, è necessario creare e configurare un gateway di rete virtuale e la connessione corrispondente.

Quando si crea un gateway di rete virtuale, si devono specificare alcune impostazioni. Una delle impostazioni necessarie indica se il gateway verrà utilizzato per il traffico ExpressRoute o del gateway VPN. Nel modello di distribuzione di Gestione risorse, l'impostazione è "-GatewayType".

Quando il traffico di rete viene inviato con una connessione privata dedicata, si usa il tipo di gateway 'ExpressRoute', detto appunto gateway ExpressRoute. Quando il traffico di rete viene inviato crittografato con una connessione pubblica, si usa il tipo di gateway 'Vpn', detto appunto gateway VPN. Le connessioni da sito a sito, da punto a sito e da rete virtuale a rete virtuale usano tutte un gateway VPN.

Ogni rete virtuale può avere un solo gateway di rete virtuale per tipo di gateway. Ad esempio, è possibile configurare un gateway di rete virtuale che usa -GatewayType Vpn e una che usa -GatewayType ExpressRoute. L'articolo è incentrato sul gateway di rete virtuale per ExpressRoute.

## <a name="gwsku"></a>SKU del gateway

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se si utilizza il portale di Azure per creare un gateway di rete virtuale di Gestione risorse, per impostazione predefinita tale gateway viene configurato utilizzando lo SKU standard. Attualmente, non è possibile specificare altri SKU per il modello di distribuzione di Gestione risorse nel portale di Azure. Tuttavia, dopo aver creato il gateway è possibile effettuare l'aggiornamento a uno SKU del gateway più potente (ad esempio, da Basic/Standard a HighPerformance) utilizzando il cmdlet PowerShell "Resize-AzureRmVirtualNetworkGateway".

###  <a name="aggthroughput"></a>Velocità effettiva aggregata stimata per SKU del gateway


La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata. La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]


## <a name="resources"></a>API REST e cmdlet PowerShell

Per altre risorse tecniche e requisiti di sintassi specifici quando si usano le API REST e i cmdlet PowerShell per le configurazioni di gateway di rete virtuale, vedere le pagine seguenti:

|**Classico** | **Gestione risorse**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## Passaggi successivi

Per altre informazioni sulle configurazioni delle connessioni disponibili, vedere [Panoramica tecnica relativa a ExpressRoute](expressroute-introduction.md).







 

<!---HONumber=AcomDC_0921_2016-->