---
title: Esempio di script di Azure PowerShell - Configurare una VPN da rete virtuale a rete virtuale | Microsoft Docs
description: Configurare la VPN da sito a sito.
services: vpn-gateway
documentationcenter: vpn-gateway
author: cherylmc
manager: jpconnock
editor: ''
tags: ''
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/02/2018
ms.author: anzaman
ms.openlocfilehash: 8136ed2537b63fbba0bafc2ef00e7a65e176fde8
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757451"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Configurare una connessione gateway VPN tra reti virtuali usando PowerShell

Questo script connette due reti virtuali tramite il tipo di connessione da rete virtuale a rete virtuale.

```azurepowershell-interactive
# Declare variables for VNET 1
  $RG1 = "TestRG1"
  $VNetName1  = "VNet1"
  $FESubName1 = "FrontEnd"
  $BESubName1 = "Backend"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.1.0.0/16"
  $FESubPrefix1 = "10.1.0.0/24"
  $BESubPrefix1 = "10.1.1.0/24"
  $GWSubPrefix1 = "10.1.255.0/27"
  $RG = "TestRG1"
  $Location1 = "East US"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconfig1"
  $Connection12 = "VNet1toVNet2"
  
# Declare variables for VNET 2  
  $RG2 = "TestRG2"
  $VNetName2  = "VNet2"
  $FESubName2 = "FrontEnd"
  $BESubName2 = "Backend"
  $GWSubName2 = "GatewaySubnet"
  $VNetPrefix21 = "10.2.0.0/16"
  $FESubPrefix2 = "10.2.0.0/24"
  $BESubPrefix2 = "10.2.1.0/24"
  $GWSubPrefix2 = "10.2.255.0/27"
  $Location2 = "East US"
  $GWName2 = "VNet2GW"
  $GWIPName2 = "VNet2GWIP"
  $GWIPconfName2 = "gwipconfig2"
  $Connection21 = "VNet2toVNet1"
# Create a resource group
New-AzureRmResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network 1
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName $RG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig1 = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork1
# Set the subnet configuration for virtual network 1
$virtualNetwork1 | Set-AzureRmVirtualNetwork
# Add a gateway subnet
$vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet1
# Set the subnet configuration for the virtual network
$virtualNetwork1 | Set-AzureRmVirtualNetwork
# Request a public IP address
$gwpip1= New-AzureRmPublicIpAddress -Name VNet1GWIP -ResourceGroupName $RG1 -Location 'East US' `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet1 = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet1
$gwipconfig1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip1.Id
# Create the VPN gateway
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName $RG1 `
 -Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 
# Create the second resource group
New-AzureRmResourceGroup -Name $RG2 -Location EastUS
# Create a virtual network 2
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName $RG2 `
  -Location EastUS `
  -Name VNet2 `
  -AddressPrefix 10.2.0.0/16
# Create a subnet configuration
$subnetConfig2 = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.2.0.0/24 `
  -VirtualNetwork $virtualNetwork2
# Set the subnet configuration for virtual network 2
$virtualNetwork2 | Set-AzureRmVirtualNetwork
# Add a gateway subnet
$vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName $RG2 -Name VNet2
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.2.255.0/27 -VirtualNetwork $vnet2
# Set the subnet configuration for the virtual network
$virtualNetwork2 | Set-AzureRmVirtualNetwork
# Request a public IP address
$gwpip2 = New-AzureRmPublicIpAddress -Name VNet2GWIP -ResourceGroupName $RG2 -Location 'East US' `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet2 = Get-AzureRmVirtualNetwork -Name VNet2 -ResourceGroupName $RG2
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet2
$gwipconfig2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig2 -SubnetId $subnet.Id -PublicIpAddressId $gwpip1.Id
# Create the VPN gateway
New-AzureRmVirtualNetworkGateway -Name VNet2GW -ResourceGroupName $RG2 `
 -Location 'East US' -IpConfigurations $gwipconfig2 -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1
# Create the connections
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 `
-VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
 ```
 
## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create non sono più necessarie, usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per eliminare il gruppo di risorse. Questa operazione eliminerà i gruppi di risorse e tutte le risorse che contengono.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
Remove-AzureRmResourceGroup -Name TestRG2
```
 
 
 ## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) | Aggiunge una configurazione di subnet. Questa configurazione viene usata con il processo di creazione della rete virtuale. |
| [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) | Ottiene i dettagli della rete virtuale. |
| [Get-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/get-azurermvirtualnetworkgateway) | Ottiene i dettagli del gateway della rete virtuale. |
| [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) | Ottiene i dettagli della configurazione della subnet della rete virtuale. |
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crea una configurazione di subnet. Questa configurazione viene usata con il processo di creazione della rete virtuale. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Crea una rete virtuale. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crea un indirizzo IP pubblico. |
| [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig) | Crea una nuova configurazione IP del gateway. |
| [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/new-azurermvirtualnetworkgateway) | Crea un gateway VPN. |
| [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection) | Crea una connessione da rete virtuale a rete virtuale. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Imposta la configurazione della subnet per la rete virtuale. |
| [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway) | Imposta la configurazione per il gateway VPN. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).
