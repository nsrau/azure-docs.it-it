---
title: Aggiornare un'applicazione IPv4 a IPv6 in rete virtuale di Azure-PowerShell
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire indirizzi IPv6 in un'applicazione esistente in rete virtuale di Azure usando Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9d2ff583b032ff1f9aa5dbc9706ea6c981fc7265
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "96009977"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Aggiornare un'applicazione IPv4 a IPv6 in rete virtuale di Azure-PowerShell

Questo articolo illustra come aggiungere connettività IPv6 a un'applicazione IPv4 esistente in una rete virtuale di Azure con un Load Balancer Standard e un indirizzo IP pubblico. L'aggiornamento sul posto include:
- Spazio degli indirizzi IPv6 per la rete virtuale e la subnet
- Load Balancer Standard con configurazioni front-end IPv4 e IPV6
- VM con NIC con una configurazione di IPv4 + IPv6
- IP pubblico IPv6, quindi il servizio di bilanciamento del carico ha connettività IPv6 con connessione Internet

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 6.9.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia stata distribuita una Load Balancer Standard come descritto in [Guida introduttiva: creare un Load Balancer standard-Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md).

## <a name="retrieve-the-resource-group"></a>Recuperare il gruppo di risorse

Prima di poter creare la rete virtuale a doppio stack, è necessario recuperare il gruppo di risorse con [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell-interactive
$rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Creare indirizzi IP IPv6

Creare un indirizzo IPv6 pubblico con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per la Load Balancer standard. Nell'esempio seguente viene creato un indirizzo IP pubblico IPv6 denominato *PublicIP_v6* nel gruppo di risorse *myResourceGroupSLB* :

```azurepowershell-interactive  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Configurare front-end del servizio di bilanciamento del carico

Recuperare la configurazione del servizio di bilanciamento del carico esistente e quindi aggiungere il nuovo indirizzo IP IPv6 usando [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) come indicato di seguito:

```azurepowershell-interactive
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"

# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Configurare il pool back-end del servizio di bilanciamento del carico

Creare il pool back-end nella copia locale della configurazione del servizio di bilanciamento del carico e aggiornare il servizio di bilanciamento del carico in esecuzione con la nuova configurazione del pool back-end, come indicato di seguito:

```azurepowershell-interactive
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"

# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Configurare le regole del servizio di bilanciamento del carico
Recuperare il front-end del servizio di bilanciamento del carico esistente e la configurazione del pool back-end e quindi aggiungere nuove regole di bilanciamento del carico usando [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell-interactive
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb

# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>Aggiungi intervalli di indirizzi IPv6

Aggiungere gli intervalli di indirizzi IPv6 alla rete virtuale e alla subnet che ospita le macchine virtuali come indicato di seguito:

```azurepowershell-interactive
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 

#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("fd00:db8:deca::/48")

#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]

#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("fd00:db8:deca::/64")

#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork
```

## <a name="add-ipv6-configuration-to-nic"></a>Aggiungere la configurazione IPv6 alla scheda NIC

Configurare tutte le NIC VM con un indirizzo IPv6 usando [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) come indicato di seguito:

```azurepowershell-interactive
#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName

#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visualizza rete virtuale dual stack IPv6 in portale di Azure

È possibile visualizzare la rete virtuale dual stack IPv6 in portale di Azure come indicato di seguito:
1. Nella barra di ricerca del portale immettere *myVnet*.
2. Selezionare **myVnet** quando viene visualizzato nei risultati della ricerca. Verrà avviata la pagina **Panoramica** della rete virtuale dual stack denominata *myVNet*. La rete virtuale dual stack Mostra le tre schede NIC con le configurazioni IPv4 e IPv6 presenti nella subnet Dual *stack denominata subnet*.

  ![Rete virtuale dual stack IPv6 in Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato aggiornato un Load Balancer Standard esistente con una configurazione IP front-end IPv4 a una configurazione dual stack (IPv4 e IPv6). Sono state inoltre aggiunte configurazioni IPv6 alle schede NIC delle macchine virtuali nel pool back-end e alla rete virtuale in cui sono ospitate. Per altre informazioni sul supporto di IPv6 in reti virtuali di Azure, vedere [che cos'è IPv6 per rete virtuale di Azure?](ipv6-overview.md)
