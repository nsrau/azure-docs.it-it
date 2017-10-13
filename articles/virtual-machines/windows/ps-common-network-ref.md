---
title: Comandi di rete di PowerShell comuni per le reti virtuali di Azure | Microsoft Docs
description: Comandi di PowerShell comuni utili per iniziare a creare una rete virtuale e le risorse associate per le macchine virtuali.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: 41a26e8b13fdb2531924ba441017c4c01e664995
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Comandi di rete di PowerShell comuni per le reti virtuali di Azure

Se si vuole creare una macchina virtuale, è necessario creare una [rete virtuale](../../virtual-network/virtual-networks-overview.md) o conoscere una rete virtuale esistente a cui è possibile aggiungere la VM. Quando si crea una macchina virtuale, è in genere necessario prendere in considerazione anche la creazione delle risorse illustrate in questo articolo.

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

Alcune di queste variabili potrebbero essere utili se si esegue più di uno tra i comandi indicati nell'articolo:

- $location: la posizione delle risorse di rete. È possibile usare [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) per trovare un'[area geografica](https://azure.microsoft.com/regions/) appropriata.
- $myResourceGroup: il nome del gruppo di risorse in cui si trovano le risorse di rete.

## <a name="create-network-resources"></a>Creare risorse di rete

| Attività | Comando |
| ---- | ------- |
| Creare configurazioni subnet |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Una rete tipica potrebbe disporre di una subnet per un [bilanciamento del carico Internet](../../load-balancer/load-balancer-internet-overview.md) e una subnet separata per un [bilanciamento del carico interno](../../load-balancer/load-balancer-internal-overview.md). |
| Crea rete virtuale |$vnet = [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Test per un nome di dominio univoco |[Test-AzureRmDnsAvailability](https://docs.microsoft.com/powershell/module/azurerm.network/test-azurermdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>È possibile specificare un nome di dominio DNS per una [risorsa IP pubblica](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), che crea un mapping per domainname.location.cloudapp.azure.com per l'indirizzo IP pubblico nei server DNS gestiti di Azure. Il nome può contenere solo lettere, numeri e trattini. Il primo e l'ultimo carattere devono essere una lettera o un numero e il nome di dominio deve essere univoco nella rispettiva posizione di Azure. Se viene restituito **True** , il nome proposto è univoco a livello globale. |
| Creare un indirizzo IP pubblico |$pip = [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>L'indirizzo IP pubblico usa il nome di dominio verificato in precedenza e viene utilizzato dalla configurazione front-end del servizio di bilanciamento del carico. |
| Creare la configurazione di un indirizzo IP front-end |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>La configurazione front-end include l'indirizzo IP pubblico creato in precedenza per il traffico di rete in ingresso. |
| Creare un pool di indirizzi back-end |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Fornisce gli indirizzi interni per il back-end del servizio di bilanciamento del carico a cui si accede tramite un'interfaccia di rete. |
| Creare un probe |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contiene i probe di integrità usati per controllare la disponibilità delle istanze delle macchine virtuali nel pool di indirizzi back-end. |
| Creare una regola di bilanciamento del carico |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contiene regole che assegnano una porta pubblica nel servizio di bilanciamento del carico a una porta nel pool di indirizzi back-end. |
| Creare una regola NAT in ingresso |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contiene le regole che eseguono il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end. |
| Creare un servizio di bilanciamento del carico |$loadBalancer = [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Creare un'interfaccia di rete |$nic1= [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Creare un'interfaccia di rete usando l'indirizzo IP pubblico e una subnet di rete virtuale creata in precedenza. |

## <a name="get-information-about-network-resources"></a>Visualizzare le informazioni sulle risorse di rete

| Attività | Comando |
| ---- | ------- |
| Elencare le reti virtuali |[Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Elenca tutte le reti virtuali nel gruppo di risorse. |
| Visualizzare le informazioni su una rete virtuale |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Elencare le subnet in una rete virtuale |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Visualizzare le informazioni su una subnet |[Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Visualizza informazioni sulla subnet nella rete virtuale specificata. Il valore $vnet rappresenta l'oggetto restituito da Get-AzureRmVirtualNetwork. |
| Elencare gli indirizzi IP |[Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Elenca gli indirizzi IP pubblici nel gruppo di risorse. |
| Elencare i servizi di bilanciamento del carico |[Get-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Elenca tutti i servizi di bilanciamento del carico nel gruppo di risorse. |
| Elencare le interfacce di rete |[Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Elenca tutte le interfacce di rete nel gruppo di risorse. |
| Visualizzare le informazioni su un'interfaccia di rete |Get-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Visualizza le informazioni su un'interfaccia di rete specifica. |
| Visualizzare la configurazione IP di un'interfaccia di rete |[Get-AzureRmNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Visualizza le informazioni sulla configurazione IP dell'interfaccia di rete specificata. Il valore $nic rappresenta l'oggetto restituito da Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Gestire le risorse di rete

| Attività | Comando |
| ---- | ------- |
| Aggiungere una subnet a una rete virtuale |[Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Aggiunge una subnet a una rete virtuale esistente. Il valore $vnet rappresenta l'oggetto restituito da Get-AzureRmVirtualNetwork. |
| Eliminare una rete virtuale |[Remove-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Rimuove la rete virtuale specificata dal gruppo di risorse. |
| Eliminare un'interfaccia di rete |[Remove-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermnetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Rimuove l'interfaccia di rete specificata dal gruppo di risorse. |
| Eliminare un servizio di bilanciamento del carico |[Remove-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Rimuove il servizio di bilanciamento del carico specificato dal gruppo di risorse. |
| Eliminare un indirizzo IP pubblico |[Remove-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Rimuove l'indirizzo IP pubblico specificato dal gruppo di risorse. |

## <a name="next-steps"></a>Passaggi successivi
* Usare l'interfaccia di rete appena creata quando si [crea una VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Informazioni su come [creare una VM con più interfacce di rete](../../virtual-network/virtual-networks-multiple-nics.md).

