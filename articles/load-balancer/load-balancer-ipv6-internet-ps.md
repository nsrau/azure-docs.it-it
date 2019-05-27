---
title: Creare un servizio di bilanciamento del carico Internet con IPv6 - PowerShell
titlesuffix: Azure Load Balancer
description: Informazioni sulla creazione di un servizio di bilanciamento del carico con connessione Internet con IPv6 usando PowerShell per Resource Manager
services: load-balancer
documentationcenter: na
author: KumudD
keywords: ipv6, azure load balancer, dual stack, ip pubblico, ipv6 nativo, mobili, iot
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: e4bc889df008283f05be5f820b66415cd38c1595
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66149264"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Introduzione alla creazione di un servizio di bilanciamento del carico con connessione Internet con IPv6 usando PowerShell per Resource Manager

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interfaccia della riga di comando di Azure](load-balancer-ipv6-internet-cli.md)
> * [Modello](load-balancer-ipv6-internet-template.md)


Azure Load Balancer è un servizio di bilanciamento del carico di livello 4 (TCP, UDP). Il servizio di bilanciamento del carico offre disponibilità elevata distribuendo il traffico in ingresso tra istanze del servizio integre in servizi cloud o macchine virtuali in un set di bilanciamento del carico . Azure Load Balancer può anche presentare tali servizi su più porte, più indirizzi IP o entrambi.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="example-deployment-scenario"></a>Scenario di distribuzione di esempio

Il diagramma seguente illustra la soluzione di bilanciamento del carico distribuita in questo articolo.

![Scenario del bilanciamento del carico](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

In questo scenario si creeranno le seguenti risorse di Azure:

* un servizio di bilanciamento del carico con connessione Internet con un indirizzo IP pubblico IPv4 e IPv6
* due regole di bilanciamento del carico per eseguire il mapping degli indirizzi VIP pubblici agli endpoint privati
* un set di disponibilità contenente le due macchine virtuali
* due macchine virtuali (VM)
* un'interfaccia di rete virtuale per ogni VM con l'assegnazione degli indirizzi IPv4 e IPv6

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Distribuzione della soluzione tramite Azure PowerShell

La procedura seguente illustra come creare un servizio di bilanciamento del carico con connessione Internet usando Azure Resource Manager con PowerShell. Con Azure Resource Manager ogni risorsa viene creata e configurata singolarmente e quindi integrata per creare una risorsa.

Per distribuire un servizio di bilanciamento del carico è necessario creare e configurare gli oggetti seguenti:

* Configurazione di IP front-end: contiene gli indirizzi IP pubblici per il traffico di rete in ingresso.
* Pool di indirizzi back-end: contiene interfacce di rete (NIC) per le macchine virtuali per la ricezione di traffico di rete dal servizio di bilanciamento del carico.
* Regole di bilanciamento del carico: contengono regole per il mapping di una porta pubblica nel servizio di bilanciamento del carico alle porte nel pool di indirizzi back-end.
* Regole NAT in ingresso: contengono regole per il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end.
* Probe: contengono probe di integrità usati per verificare la disponibilità di istanze di macchine virtuali nel pool di indirizzi back-end.

Per altre informazioni, vedere [Supporto di Azure Resource Manager per Load Balancer](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurazione di PowerShell per l'uso di Resource Manager

Assicurarsi di avere la versione di produzione più recente del modulo Azure Resource Manager per PowerShell.

1. Accesso ad Azure

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

    Immettere le credenziali quando richiesto.

2. Controllare le sottoscrizioni per l'account

   ```azurepowershell-interactive
    Get-AzSubscription
    ```

3. Scegliere quali sottoscrizioni Azure usare.

    ```azurepowershell-interactive
    Select-AzSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Creare un nuovo gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Creare una rete virtuale e un indirizzo IP pubblico per il pool di indirizzi IP front-end

1. Creare una rete virtuale con una subnet.

    ```azurepowershell-interactive
    $backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Creare risorse di indirizzi IP pubblici (PIP) di Azure per il pool di indirizzi IP front-end. Assicurarsi di modificare il valore per `-DomainNameLabel` prima di eseguire i comandi seguenti. Il valore deve essere univoco all'interno dell'area di Azure.

    ```azurepowershell-interactive
    $publicIPv4 = New-AzPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > Il servizio di bilanciamento del carico usa l'etichetta di dominio dell'indirizzo IP pubblico come prefisso per il nome di dominio completo. In questo esempio, i nomi di dominio completo sono *lbnrpipv4.westus.cloudapp.azure.com* e *lbnrpipv6.westus.cloudapp.azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Creare una configurazione di indirizzi IP front-end e un pool di indirizzi back-end

1. Creare una configurazione di indirizzi front-end che userà gli indirizzi IP pubblici creati.

    ```azurepowershell-interactive
    $FEIPConfigv4 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Creare i pool di indirizzi back-end.

    ```azurepowershell-interactive
    $backendpoolipv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Creare regole di bilanciamento del carico, regole NAT, un probe e un servizio di bilanciamento del carico

Questo esempio crea gli elementi seguenti:

* Una regola NAT per la conversione di tutto il traffico in ingresso nella porta 443 alla porta 4443
* Regola del servizio di bilanciamento del carico per il bilanciamento di tutto il traffico in ingresso sulla porta 80 verso la porta 80 negli indirizzi nel pool back-end.
* Regola del servizio di bilanciamento del carico per consentire la connessione RDP alle macchine virtuali sulla porta 3389.
* Una regola probe per il controllo dello stato di integrità in una pagina denominata *HealthProbe.aspx* o un servizio sulla porta 8080
* Un servizio di bilanciamento del carico che usa tutti questi oggetti

1. Creare le regole NAT.

    ```azurepowershell-interactive
    $inboundNATRule1v4 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. Creare un probe di integrità. Esistono due modi per configurare un probe:

    Probe HTTP

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    o probe TCP

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    Per questo esempio verranno usati probe TCP.

3. Creare una regola del servizio di bilanciamento del carico.

    ```azurepowershell-interactive
    $lbrule1v4 = New-AzLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Creare il servizio di bilanciamento del carico usando gli oggetti creati in precedenza.

    ```azurepowershell-interactive
    $NRPLB = New-AzLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Creare le schede di interfaccia di rete per le macchine virtuali back-end

1. Ottenere la rete virtuale e la subnet per la rete virtuale in cui devono essere create le schede NIC.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Creare le configurazioni IP e schede di interfaccia di rete per le macchine virtuali.

    ```azurepowershell-interactive
    $nic1IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Creare le macchine virtuali e assegnare le schede di interfaccia di rete appena create

Per altre informazioni, vedere [Creare e preconfigurare una macchina virtuale Windows con Resource Manager e Azure PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Creare un set di disponibilità e l'account di archiviazione

    ```azurepowershell-interactive
    New-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Creare ogni macchina virtuale e assegnare le schede di interfaccia di rete create in precedenza

    ```azurepowershell-interactive
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```

## <a name="next-steps"></a>Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
