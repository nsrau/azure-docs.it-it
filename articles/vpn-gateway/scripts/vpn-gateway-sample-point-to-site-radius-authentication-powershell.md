---
title: Esempio di script di Azure PowerShell - Configurare la VPN da punto a sito con l'autenticazione tramite nome utente/password RADIUS | Microsoft Docs
description: Configurare la VPN da punto a sito con l'autenticazione tramite nome utente/password RADIUS. Questo articolo usa PowerShell.
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
ms.date: 05/30/2018
ms.author: anzaman
ms.openlocfilehash: 445190d86ab5d67132302a274f34a583cd94f937
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658513"
---
# <a name="create-a-vpn-gateway-and-add-point-to-site-configuration-using-powershell"></a>Creare un gateway VPN e aggiungere la configurazione da punto a sito tramite PowerShell

Questo script crea un gateway VPN basato su route e aggiunge la configurazione da punto a sito tramite autenticazione con nome utente/password RADIUS


```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "10.0.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $BESubPrefix = "10.1.1.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "192.168.0.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $GWIPconfName = "gwipconf"
# Create a resource group
New-AzureRmResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzureRmVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzureRmVirtualNetwork
# Request a public IP address
$gwpip= New-AzureRmPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
 -Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
# Create a secure string for the RADIUS secret
$Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"

# Add the VPN client address pool and the RADIUS server information
$Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
 -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
 -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create non sono più necessarie, usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per eliminare il gruppo di risorse. Questa operazione eliminerà il gruppo di risorse e tutte le risorse in esso contenute.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
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
| [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgateway) | Crea un gateway VPN. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Imposta la configurazione della subnet per la rete virtuale. |
| [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway) | Imposta la configurazione per il gateway VPN. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).
