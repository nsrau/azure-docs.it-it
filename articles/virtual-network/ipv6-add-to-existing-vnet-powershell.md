---
title: Aggiornare un'applicazione IPv4 a IPv6 nella rete virtuale di Azure - PowerShellUpgrade an IPv4 application to IPv6 in Azure Virtual Network - PowerShell
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire indirizzi IPv6 in un'applicazione esistente nella rete virtuale di Azure usando Azure Powershell.This article shows how to deploy IPv6 addresses to an existing application in Azure virtual network using Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: c733538a4e730a95008a8ec1e4d50c20d6ce24ec
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420769"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Aggiornare un'applicazione IPv4 a IPv6 nella rete virtuale di Azure - PowerShellUpgrade an IPv4 application to IPv6 in Azure virtual network - PowerShell

Questo articolo illustra come aggiungere la connettività IPv6 a un'applicazione IPv4 esistente in una rete virtuale di Azure con un servizio di bilanciamento del carico standard e un ip pubblico. L'aggiornamento sul posto include:
- Spazio di indirizzi IPv6 per la rete virtuale e la subnet
- un load balancer Standard con configurazioni front-end IPv4 e IPV6
- Macchine virtuali con schede di interfaccia di rete che dispongono sia di una configurazione IPv4 e IPv6
- IP pubblico IPv6 in modo che il servizio di bilanciamento del carico disponga della connettività IPv6 con connessione Internet



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, questo articolo richiede la versione 6.9.0 o successiva del modulo di Azure PowerShell.If you choose to install and use PowerShell locally, this article requires the Azure PowerShell module version 6.9.0 or later. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone che sia stato distribuito un servizio di bilanciamento del carico Standard come descritto in Guida introduttiva: Creare un servizio di bilanciamento del carico standard - Azure PowerShell.This article assumes that you deployed a Standard Load Balancer as described in [Quickstart: Create a Standard Load Balancer - Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>Recuperare il gruppo di risorseRetrieve the resource group

Prima di poter creare la rete virtuale dual stack, è necessario recuperare il gruppo di risorse con [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Creare un indirizzo IP IPv6Create an IPv6 IP addresses

Creare un indirizzo IPv6 pubblico con New-AzPublicIpAddress per il servizio di bilanciamento del carico Standard.Create a public IPv6 address with [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) for your Standard Load Balancer. Nell'esempio seguente viene creato un indirizzo IP pubblico IPv6 denominato PublicIP_v6 nel gruppo di risorse *myResourceGroupSLB:The* following example creates an IPv6 public IP address named *PublicIP_v6* in the myResourceGroupSLB resource group:

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Configurare il front-end del servizio di bilanciamento del caricoConfigure load balancer front

Recuperare la configurazione del servizio di bilanciamento del carico esistente e quindi aggiungere il nuovo indirizzo IP IPv6 utilizzando [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) come segue:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Configurare il pool back-end del servizio di bilanciamento del caricoConfigure load balancer backend pool

Creare il pool back-end nella copia locale della configurazione del servizio di bilanciamento del carico e aggiornare il servizio di bilanciamento del carico in esecuzione con la nuova configurazione del pool back-end come segue:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Configurare le regole del servizio di bilanciamento del carico
Recuperare la configurazione del front-end e del pool back-end del servizio di bilanciamento del carico esistente e quindi aggiungere nuove regole di bilanciamento del carico utilizzando [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell
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
## <a name="add-ipv6-address-ranges"></a>Aggiungere intervalli di indirizzi IPv6

Aggiungere intervalli di indirizzi IPv6 alla rete virtuale e alla subnet che ospitano le macchine virtuali come segue:Add IPv6 address ranges to the virtual network and subnet hosting the VMs as follows:

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>Aggiungere la configurazione IPv6 alla scheda di interfaccia di rete

Configurare tutte le schede di interfaccia di rete della macchina virtuale con un indirizzo IPv6 usando [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) come segue:

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visualizzare la rete virtuale dual stack IPv6 nel portale di AzureView IPv6 dual stack virtual network in Azure portal
È possibile visualizzare la rete virtuale dual stack IPv6 nel portale di Azure come segue:You can view the IPv6 dual stack virtual network in Azure portal as follows:
1. Nella barra di ricerca del portale, immettere *myVnet*.
2. Quando **myVnet** viene visualizzato nei risultati della ricerca, selezionarlo. Verrà avviata la pagina **Panoramica** della rete virtuale dual stack denominata *myVNet*. La rete virtuale dual stack mostra le tre schede di interfaccia di rete con configurazioni IPv4 e IPv6 situate nella subnet dello stack doppio denominata *mySubnet*.

  ![Rete virtuale dual stack IPv6 in Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato aggiornato un servizio di bilanciamento del carico Standard esistente con una configurazione IP front-end IPv4 a una configurazione dual stack (IPv4 e IPv6). Sono state inoltre aggiunte configurazioni IPv6 alle schede di interfaccia di rete delle macchine virtuali nel pool back-end e alla rete virtuale che le ospita. Per altre informazioni sul supporto IPv6 nelle reti virtuali di Azure, vedere [Che cos'è IPv6 per](ipv6-overview.md) la rete virtuale di Azure?
