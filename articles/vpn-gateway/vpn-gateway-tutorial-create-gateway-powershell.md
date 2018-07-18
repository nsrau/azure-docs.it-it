---
title: Creare e gestire un gateway VPN di Azure usando PowerShell | Microsoft Docs
description: Esercitazione - Creare e gestire un gateway VPN con il modulo Azure PowerShell
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/14/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 0f10384e7e21d65b3a16869a10f8294b9643c74c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38705684"
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>Creare e gestire un gateway VPN con il modulo Azure PowerShell

I gateway VPN di Azure offrono connettività cross-premise tra l'infrastruttura locale del cliente e Azure. Questa esercitazione illustra gli elementi di base della distribuzione di un gateway VPN di Azure, ad esempio la creazione e la gestione di un gateway VPN. Si apprenderà come:

> [!div class="checklist"]
> * Creare un gateway VPN
> * Ridimensionare un gateway VPN
> * Reimpostare un gateway VPN

Il diagramma seguente mostra la rete virtuale e il gateway VPN creati nell'ambito di questa esercitazione.

![Rete virtuale e gateway VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell e Azure PowerShell

[!INCLUDE [working with cloudshell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.3 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="common-network-parameter-values"></a>Valori dei parametri di rete comuni

Modificare i valori seguenti in base all'ambiente e alla configurazione di rete.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
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

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. È prima necessario creare un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato *TestRG1* nell'area *Stati Uniti orientali*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Il gateway VPN di Azure fornisce connettività cross-premise e funzionalità server VPN da punto a sito per la rete virtuale. Aggiungere il gateway VPN a una rete virtuale esistente o creare una nuova rete virtuale e il gateway. Questo esempio crea una nuova rete virtuale con tre subnet: Frontend, Backend e GatewaySubnet usando [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) e [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Richiedere un indirizzo IP pubblico del gateway VPN

I gateway VPN di Azure comunicano con i dispositivi VPN locali tramite Internet per eseguire la negoziazione IKE (Internet Key Exchange) e stabilire tunnel IPsec. Creare un indirizzo IP pubblico e assegnarlo al gateway VPN, come illustrato nell'esempio seguente, usando [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) e [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> Attualmente, per il gateway è possibile usare solo un indirizzo IP pubblico dinamico. L'indirizzo IP statico non è supportato nei gateway VPN di Azure.

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>Creare un gateway VPN

La creazione di un gateway VPN può richiedere almeno 45 minuti. Al termine della creazione del gateway, è possibile creare una connessione tra la propria rete virtuale e un'altra rete virtuale. In alternativa, è possibile creare una connessione tra la propria rete virtuale e una posizione locale. Creare un gateway VPN usando il cmdlet [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Valori dei parametri principali:
* GatewayType: usare **Vpn** per le connessioni da sito a sito e da rete virtuale a rete virtuale
* VpnType: usare **RouteBased** per interagire con una più ampia gamma di dispositivi VPN e disporre di più funzionalità di routing
* GatewaySku: il valore predefinito è **VpnGw1**. Modificare questo valore in VpnGw2 o VpnGw3 se sono necessarie più connessioni o una velocità effettiva maggiore. Per altre informazioni, vedere [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Al termine della creazione del gateway, è possibile creare una connessione tra la propria rete virtuale e un'altra rete virtuale oppure tra la propria rete virtuale e una posizione locale. È anche possibile configurare una connessione da punto a sito alla rete virtuale da un computer client.

## <a name="resize-vpn-gateway"></a>Ridimensionare un gateway VPN

È possibile modificare lo SKU del gateway VPN dopo la creazione del gateway. SKU del gateway diversi supportano specifiche diverse, ad esempio per quanto riguarda velocità effettiva, numero di connessioni e così via. L'esempio seguente usa [Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway) per ridimensionare il gateway da VpnGw1 a VpnGw2. Per altre informazioni, vedere [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Il ridimensionamento di un gateway VPN richiede circa dai 30 ai 45 minuti, anche se questa operazione **non** interrompe né rimuove le connessioni e le configurazioni esistenti.

## <a name="reset-vpn-gateway"></a>Reimpostare un gateway VPN

Durante la risoluzione dei problemi, è possibile reimpostare il gateway VPN di Azure per forzare il riavvio delle configurazioni dei tunnel IPsec/IKE. Usare [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway) per reimpostare il gateway.

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Per altre informazioni, vedere [Reimpostare un gateway VPN](vpn-gateway-resetgw-classic.md).

## <a name="get-the-gateway-public-ip-address"></a>Ottenere l'indirizzo IP pubblico del gateway

Se si conosce il nome dell'indirizzo IP pubblico, usare [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Reset-AzureRmPublicIpAddress) per visualizzare l'indirizzo IP pubblico assegnato al gateway.

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>Eliminare un gateway VPN

Una configurazione completa di connettività cross-premise e da rete virtuale a rete virtuale richiede diversi tipi di risorse in aggiunta al gateway VPN. Eliminare le connessioni associate al gateway VPN prima di eliminare il gateway stesso. Una volta eliminato il gateway, è possibile eliminare gli indirizzi IP pubblici per il gateway. Per istruzioni dettagliate, vedere [Eliminare un gateway VPN](vpn-gateway-delete-vnet-gateway-powershell.md).

Se il gateway fa parte di una distribuzione di un modello di verifica o di un prototipo, è possibile usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse, il gateway VPN e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le operazioni di base di creazione e gestione di un gateway VPN, tra cui:

> [!div class="checklist"]
> * Creare un gateway VPN
> * Ridimensionare un gateway VPN
> * Reimpostare un gateway VPN

Passare alle esercitazioni seguenti per informazioni sulle connessioni da sito a sito, da rete virtuale a rete virtuale e da punto a sito.

> [!div class="nextstepaction"]
> * [Creare connessioni da sito a sito](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Creare connessioni da rete virtuale a rete virtuale](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Creare connessioni da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
