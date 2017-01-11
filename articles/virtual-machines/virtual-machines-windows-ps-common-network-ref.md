---
title: Comandi di rete di PowerShell comuni per le VM | Microsoft Docs
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
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: ce08e9dcd0585e00bdd42b8aa34ac2c597a53a6e


---
# <a name="common-network-azure-powershell-commands-for-vms"></a>Comandi di rete di Azure PowerShell comuni per le macchine virtuali
Se si vuole creare una macchina virtuale, è necessario creare una [rete virtuale](../virtual-network/virtual-networks-overview.md) o conoscere una rete virtuale esistente a cui è possibile aggiungere la VM. Quando si crea una macchina virtuale, è in genere necessario prendere in considerazione anche la creazione delle risorse illustrate in questo articolo.

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="create-network-resources"></a>Creare risorse di rete
| Attività | Comando |
| --- | --- |
| Creare configurazioni subnet |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name "nome_subnet" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "nome_subnet" -AddressPrefix XX.X.X.X/XX<BR><BR>Una rete tipica potrebbe disporre di una subnet per un [bilanciamento del carico Internet](../load-balancer/load-balancer-internet-overview.md) e una subnet separata per un [bilanciamento del carico interno](../load-balancer/load-balancer-internal-overview.md). |
| Crea rete virtuale |$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name "nome_rete_virtuale" -ResourceGroupName "nome_gruppo_di_risorse" -Location "nome_posizione" -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Test per un nome di dominio univoco |[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName "nome_dominio" -Location "nome_posizione"<BR><BR>È possibile specificare un nome di dominio DNS per una [risorsa IP pubblica](../virtual-network/virtual-network-ip-addresses-overview-arm.md), che crea un mapping per domainname.location.cloudapp.azure.com per l'indirizzo IP pubblico nei server DNS gestiti di Azure. Il nome può contenere solo lettere, numeri e trattini. Il primo e l'ultimo carattere devono essere una lettera o un numero e il nome di dominio deve essere univoco nella rispettiva posizione di Azure. Se viene restituito **True** , il nome proposto è univoco a livello globale. |
| Creare un indirizzo IP pubblico |$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name "nome_indirizzo_ip" -ResourceGroupName "nome_gruppo_di_risorse" -DomainNameLabel "nome_dominio" -Location "nome_posizione" -AllocationMethod Dynamic<BR><BR>L'indirizzo IP pubblico usa il nome di dominio verificato in precedenza e viene utilizzato dalla configurazione front-end del servizio di bilanciamento del carico. |
| Creare la configurazione di un indirizzo IP front-end |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name "nome_ip_front-end" -PublicIpAddress $pip<BR><BR>La configurazione front-end include l'indirizzo IP pubblico creato in precedenza per il traffico di rete in ingresso. |
| Creare un pool di indirizzi back-end |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name "nome_pool_back-end"<BR><BR>Fornisce gli indirizzi interni per il back-end del servizio di bilanciamento del carico a cui si accede tramite un'interfaccia di rete. |
| Creare un probe |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name "nome_probe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contiene i probe di integrità usati per controllare la disponibilità delle istanze delle macchine virtuali nel pool di indirizzi back-end. |
| Creare una regola di bilanciamento del carico |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contiene regole che assegnano una porta pubblica nel servizio di bilanciamento del carico a una porta nel pool di indirizzi back-end. |
| Creare una regola NAT in ingresso |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name "nome_regola" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contiene le regole che eseguono il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end. |
| Creare un servizio di bilanciamento del carico |$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName "nome_gruppo_di_risorse" -Name "nome_servizio_bilanciamento_carico" -Location "nome_posizione" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Creare un'interfaccia di rete |$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName "nome_gruppo_di_risorse" -Name "nome_interfaccia_di_rete" -Location "nome_posizione" -PrivateIpAddress XX.X.X.X -Subnet subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Creare un'interfaccia di rete usando l'indirizzo IP pubblico e una subnet di rete virtuale creata in precedenza. |

## <a name="get-information-about-network-resources"></a>Visualizzare le informazioni sulle risorse di rete
| Attività | Comando |
| --- | --- |
| Elencare le reti virtuali |[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName "nome_gruppo_di_risorse"<BR><BR>Elenca tutte le reti virtuali nel gruppo di risorse. |
| Visualizzare le informazioni su una rete virtuale |Get-AzureRmVirtualNetwork -Name "nome_rete_virtuale" -ResourceGroupName "nome_gruppo_di_risorse" |
| Elencare le subnet in una rete virtuale |Get-AzureRmVirtualNetwork -Name "nome_rete_virtuale" -ResourceGroupName "nome_gruppo_di_risorse" &#124; Select Subnets |
| Visualizzare le informazioni su una subnet |[Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "nome_subnet" -VirtualNetwork $vnet<BR><BR>Visualizza informazioni sulla subnet nella rete virtuale specificata. Il valore $vnet rappresenta l'oggetto restituito da Get-AzureRmVirtualNetwork. |
| Elencare gli indirizzi IP |[Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) -ResourceGroupName "nome_gruppo_di_risorse"<BR><BR>Elenca gli indirizzi IP pubblici nel gruppo di risorse. |
| Elencare i servizi di bilanciamento del carico |[Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) -ResourceGroupName "nome_gruppo_di_risorse"<BR><BR>Elenca tutti i servizi di bilanciamento del carico nel gruppo di risorse. |
| Elencare le interfacce di rete |[Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) -ResourceGroupName "nome_gruppo_di_risorse"<BR><BR>Elenca tutte le interfacce di rete nel gruppo di risorse. |
| Visualizzare le informazioni su un'interfaccia di rete |Get-AzureRmNetworkInterface -Name "nome_interfaccia_di_rete" -ResourceGroupName "nome_gruppo_di_risorse"<BR><BR>Visualizza le informazioni su un'interfaccia di rete specifica. |
| Visualizzare la configurazione IP di un'interfaccia di rete |[Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -Name "nome_configurazione_ip" -NetworkInterface $nic<BR><BR>Visualizza le informazioni sulla configurazione IP dell'interfaccia di rete specificata. Il valore $nic rappresenta l'oggetto restituito da Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Gestire le risorse di rete
| Attività | Comando |
| --- | --- |
| Aggiungere una subnet a una rete virtuale |[Add-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) -AddressPrefix XX.X.X.X/XX -Name "nome_subnet" -VirtualNetwork $vnet<BR><BR>Aggiunge una subnet a una rete virtuale esistente. Il valore $vnet rappresenta l'oggetto restituito da Get-AzureRmVirtualNetwork. |
| Eliminare una rete virtuale |[Remove-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -Name "nome_rete_virtuale" -ResourceGroupName "nome_gruppo_di_risorse"<BR><BR>Rimuove la rete virtuale specificata dal gruppo di risorse. |
| Eliminare un'interfaccia di rete |[Remove-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -Name "nome_interfaccia_di_rete" -ResourceGroupName "nome_gruppo_di_risorse"<BR><BR>Rimuove l'interfaccia di rete specificata dal gruppo di risorse. |
| Eliminare un servizio di bilanciamento del carico |[Remove-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -Name "nome_servizio_bilanciamento_carico" -ResourceGroupName "nome_gruppo_di_risorse"<BR><BR>Rimuove il servizio di bilanciamento del carico specificato dal gruppo di risorse. |
| Eliminare un indirizzo IP pubblico |[Remove-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-Name "nome_indirizzo_ip" -ResourceGroupName "nome_gruppo_di_risorse"<BR><BR>Rimuove l'indirizzo IP pubblico specificato dal gruppo di risorse. |

## <a name="next-steps"></a>Passaggi successivi
* Usare l'interfaccia di rete appena creata quando si [crea una VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Informazioni su come [creare una VM con più interfacce di rete](../virtual-network/virtual-networks-multiple-nics.md).




<!--HONumber=Dec16_HO2-->


