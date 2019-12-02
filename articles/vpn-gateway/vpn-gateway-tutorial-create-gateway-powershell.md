---
title: 'Esercitazione: Creare e gestire un gateway con il gateway VPN di Azure'
description: Esercitazione - Creare e gestire un gateway VPN con il modulo Azure PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: b144a70ee88138966d9cc38a56e1cff1e63fca1b
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424140"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Esercitazione: Creare e gestire un gateway VPN usando PowerShell

I gateway VPN di Azure offrono connettività cross-premise tra l'infrastruttura locale del cliente e Azure. Questa esercitazione illustra gli elementi di base della distribuzione di un gateway VPN di Azure, ad esempio la creazione e la gestione di un gateway VPN. Si apprenderà come:

> [!div class="checklist"]
> * Creare un gateway VPN
> * Visualizzare l'indirizzo IP pubblico
> * Ridimensionare un gateway VPN
> * Reimpostare un gateway VPN

Il diagramma seguente mostra la rete virtuale e il gateway VPN creati nell'ambito di questa esercitazione.

![Rete virtuale e gateway VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell e Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Valori dei parametri di rete comuni

Di seguito sono riportati i valori dei parametri usati per questa esercitazione. Negli esempi le variabili vengono convertite come illustrato sotto:

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

Modificare i valori seguenti in base alla configurazione dell'ambiente e della rete, quindi copiarli e incollarli per impostare le variabili per questa esercitazione. Se la sessione Cloud Shell scade oppure è necessario usare una finestra PowerShell diversa, copiare e incollare le variabili nella nuova sessione e continuare l'esercitazione.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. È prima necessario creare un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato *TestRG1* nell'area *Stati Uniti orientali*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Il gateway VPN di Azure fornisce connettività cross-premise e funzionalità server VPN da punto a sito per la rete virtuale. Aggiungere il gateway VPN a una rete virtuale esistente o creare una nuova rete virtuale e il gateway. Si noti che l'esempio indica in modo specifico la subnet del gateway. Per il corretto funzionamento, è sempre necessario specificare il nome della subnet del gateway come "GatewaySubnet". Questo esempio crea una nuova rete virtuale con tre subnet: Frontend, Backend e GatewaySubnet usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) e [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Richiedere un indirizzo IP pubblico del gateway VPN

I gateway VPN di Azure comunicano con i dispositivi VPN locali tramite Internet per eseguire la negoziazione IKE (Internet Key Exchange) e stabilire tunnel IPsec. Creare un indirizzo IP pubblico e assegnarlo al gateway VPN, come illustrato nell'esempio seguente, usando [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) e [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> Attualmente, per il gateway è possibile usare solo un indirizzo IP pubblico dinamico. L'indirizzo IP statico non è supportato nei gateway VPN di Azure.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>Creare un gateway VPN

La creazione di un gateway VPN può richiedere almeno 45 minuti. Al termine della creazione del gateway, è possibile creare una connessione tra la propria rete virtuale e un'altra rete virtuale. In alternativa, è possibile creare una connessione tra la propria rete virtuale e una posizione locale. Creare un gateway VPN usando il cmdlet [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Valori dei parametri principali:
* GatewayType: usare **Vpn** per le connessioni da sito a sito e da rete virtuale a rete virtuale
* VpnType: usare **RouteBased** per interagire con una più ampia gamma di dispositivi VPN e disporre di più funzionalità di routing
* GatewaySku: il valore predefinito è **VpnGw1**. Modificare questo valore in un altro SKU VpnGw se sono necessarie altre connessioni o una maggiore velocità effettiva. Per altre informazioni, vedere [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Se si usa TryIt, la sessione potrebbe scadere. Ciò non costituisce un problema. Il gateway verrà comunque creato.

Al termine della creazione del gateway, è possibile creare una connessione tra la propria rete virtuale e un'altra rete virtuale oppure tra la propria rete virtuale e una posizione locale. È anche possibile configurare una connessione da punto a sito alla rete virtuale da un computer client.

## <a name="view-the-gateway-public-ip-address"></a>Visualizzare l'indirizzo IP pubblico del gateway

Se si conosce il nome dell'indirizzo IP pubblico, usare [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) per visualizzare l'indirizzo IP pubblico assegnato al gateway.

Se la sessione è scaduta, copiare nella nuova sessione i parametri di rete comuni indicati all'inizio di questa esercitazione e procedere.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Ridimensionare un gateway

È possibile modificare lo SKU del gateway VPN dopo la creazione del gateway. SKU del gateway diversi supportano specifiche diverse, ad esempio per quanto riguarda velocità effettiva, numero di connessioni e così via. L'esempio seguente usa [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) per ridimensionare il gateway da VpnGw1 a VpnGw2. Per altre informazioni, vedere [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Il ridimensionamento di un gateway VPN richiede circa dai 30 ai 45 minuti, anche se questa operazione **non** interrompe né rimuove le connessioni e le configurazioni esistenti.

## <a name="reset-a-gateway"></a>Reimpostare un gateway

Durante la risoluzione dei problemi, è possibile reimpostare il gateway VPN di Azure per forzare il riavvio delle configurazioni dei tunnel IPsec/IKE. Usare [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) per reimpostare il gateway.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Per altre informazioni, vedere [Reimpostare un gateway VPN](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si passa all'[esercitazione successiva](vpn-gateway-tutorial-vpnconnection-powershell.md) è opportuno conservare queste risorse perché sono incluse tra i prerequisiti.

Se tuttavia il gateway fa parte di una distribuzione di test o di un prototipo, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, il gateway VPN e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le operazioni di base di creazione e gestione di un gateway VPN, tra cui:

> [!div class="checklist"]
> * Creare un gateway VPN
> * Visualizzare l'indirizzo IP pubblico
> * Ridimensionare un gateway VPN
> * Reimpostare un gateway VPN

Passare alle esercitazioni seguenti per informazioni sulle connessioni da sito a sito, da rete virtuale a rete virtuale e da punto a sito.

> [!div class="nextstepaction"]
> * [Creare connessioni da sito a sito](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Creare connessioni da rete virtuale a rete virtuale](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Creare connessioni da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
