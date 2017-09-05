---
title: "Bilanciamento del carico in più configurazioni IP in Azure | Microsoft Docs"
description: Bilanciamento del carico tra configurazioni IP primarie e secondarie.
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: annahar
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 151d0ff3e05a0b4823eb2bb85e068b5789a386cb
ms.contentlocale: it-it
ms.lasthandoff: 08/11/2017

---

# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Bilanciamento del carico in più configurazioni IP tramite PowerShell

> [!div class="op_single_selector"]
> * [Portale](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

Questo articolo descrive come usare Azure Load Balancer con più indirizzi IP su una scheda di interfaccia di rete secondaria. Per questo scenario sono disponibili due VM con Windows, ognuna con una scheda di interfaccia di rete primaria e secondaria. Ogni scheda di interfaccia di rete secondaria dispone di due configurazioni di IP. Ogni macchina virtuale ospita entrambi i siti Web: contoso.com e fabrikam.com. Ogni sito Web è associato a una delle configurazioni IP della scheda di interfaccia di rete secondaria. Azure Load Balancer viene usato per esporre due indirizzi IP front-end, uno per ogni sito Web, per distribuire il traffico alla rispettiva configurazione IP per il sito Web. Questo scenario usa lo stesso numero di porta per entrambi i front-end, nonché per entrambi gli indirizzi IP del pool back-end.

![Immagine dello scenario LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Procedura per il bilanciamento del carico in più configurazioni IP

Per ottenere lo scenario descritto in questo articolo, seguire questa procedura:

1. Installare Azure PowerShell. Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).
2. Creare un gruppo di risorse usando le impostazioni seguenti:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Per altre informazioni, vedere il passaggio 2 dell'articolo relativo alla [creazione di un gruppo di risorse](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Creare un set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) che dovrà contenere le macchine virtuali. Per questo scenario, usare il comando seguente:

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Seguire le istruzioni contenute nei passaggi da 3 a 5 dell'articolo [Creare una VM Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) per preparare la creazione di una macchina virtuale con una scheda di interfaccia di rete singola. Eseguire il passaggio 6.1 e sostituire il comando del passaggio 6.2 con quanto segue:

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Completare quindi i passaggi da 6.3 a 6.8 dell'articolo [Creare una VM Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

5. Aggiungere una seconda configurazione IP a ognuna delle macchine virtuali. Seguire le istruzioni contenute nell'articolo [Assegnare più indirizzi IP alle macchine virtuali](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add). Usare le impostazioni di configurazione seguenti:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Ai fini di questa esercitazione, non è necessario associare le configurazioni IP secondarie agli indirizzi IP pubblici. Modificare il comando per rimuovere la parte relativa all'associazione dell'indirizzo IP pubblico.

6. Completare nuovamente i passaggi da 4 a 6 di questo articolo per VM2. In questo caso assicurarsi di sostituire il nome della macchina virtuale con VM2. Si noti che non è necessario creare una rete virtuale per la seconda macchina virtuale. È possibile scegliere se creare o meno una nuova subnet in base al caso d'uso.

7. Creare due indirizzi IP pubblici e archiviarli in variabili appropriate, come illustrato di seguito:

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Creare due configurazioni IP front-end:

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Creare i pool di indirizzi back-end, un probe e regole di bilanciamento del carico:

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Dopo aver creato queste risorse, creare il servizio di bilanciamento del carico:

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Aggiungere il secondo pool di indirizzi back-end e la configurazione IP front-end per il servizio di bilanciamento del carico appena creato:

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. I comandi seguenti permettono di ottenere le schede di interfaccia di rete e aggiungono entrambe le configurazioni IP di ogni scheda di interfaccia di rete secondaria al pool di indirizzi back-end del servizio di bilanciamento del carico:

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. Infine, è necessario configurare i record risorsa DNS in modo che puntino all'indirizzo IP front-end corrispondente di Azure Load Balancer. È possibile ospitare i domini nel servizio DNS di Azure. Per altre informazioni sull'uso del servizio DNS di Azure con Azure Load Balancer, vedere [Uso del servizio DNS di Azure con altri servizi di Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su come combinare i servizi di bilanciamento del carico di Azure sono disponibili in [Uso dei servizi di bilanciamento del carico in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Informazioni su come è possibile usare diversi tipi di log in Azure per gestire e risolvere i problemi di bilanciamento del carico sono disponibili in [Analisi dei log per Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).

