---
title: Creare un servizio di bilanciamento del carico pubblico - PowerShell | Microsoft Docs
description: Informazioni su come creare un servizio di bilanciamento del carico pubblico in Resource Manager con PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 8257f548-7019-417f-b15f-d004a1eec826
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4ce11b0b06e1feaf55d17e25500c43a7eb1bf3d5
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="get-started"></a>Creazione di un servizio di bilanciamento del carico pubblico in Resource Manager con PowerShell

> [!div class="op_single_selector"]
> * [Portale](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Modello](../load-balancer/load-balancer-get-started-internet-arm-template.md)



[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Distribuzione della soluzione mediante Azure PowerShell

Le procedure seguenti illustrano come creare un servizio di bilanciamento del carico pubblico usando Azure Resource Manager con PowerShell. Con Azure Resource Manager, ogni risorsa viene creata e configurata singolarmente e poi integrata per creare una servizio di bilanciamento del carico.

Creare e configurare gli oggetti seguenti per distribuire un servizio di bilanciamento del carico:

* Configurazione IP front-end: contiene gli indirizzi IP pubblici (PIP) per il traffico di rete in ingresso.
* Pool di indirizzi back-end: contiene le interfacce di rete (NIC) per consentire alle macchine virtuali di ricevere il traffico di rete dal servizio di bilanciamento del carico.
* Regole di bilanciamento del carico: contiene le regole che eseguono il mapping di una porta pubblica sul servizio di bilanciamento del carico a una porta nel pool di indirizzi back-end.
* Regole NAT: contiene le regole che eseguono il mapping di una porta pubblica sul servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end.
* Probe: contiene probe di integrità usati per verificare la disponibilità di istanze di macchine virtuali nel pool di indirizzi back-end.

Per altre informazioni, vedere [Supporto di Azure Resource Manager per Load Balancer](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurazione di PowerShell per l'uso di Resource Manager

Assicurarsi di avere la versione di produzione più recente del modulo Azure Resource Manager per PowerShell:

1. Accedere ad Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Immettere le credenziali quando richiesto.

2. Controllare le sottoscrizioni per l'account.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Scegliere quali sottoscrizioni Azure usare.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Creare un gruppo di risorse. Se si usa un gruppo di risorse esistente, ignorare questo passaggio.

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Creare una rete virtuale e un indirizzo IP pubblico per il pool di indirizzi IP front-end

1. Creare una subnet e una rete virtuale

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Creare una risorsa di indirizzo IP pubblico di Azure denominata **PublicIP** che dovrà essere usata da un pool di indirizzi IP front-end con nome DNS **loadbalancernrp.westus.cloudapp.azure.com**. Il comando seguente usa il tipo di allocazione statica.

    ```powershell
    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -DomainNameLabel loadbalancernrp
    ```

   > [!IMPORTANT]
   > Il servizio di bilanciamento del carico usa l'etichetta di dominio dell'indirizzo IP pubblico come prefisso per il nome di dominio completo. Questa è una differenza rispetto al modello di distribuzione classica, che usa il servizio cloud come nome di dominio completo del servizio di bilanciamento del carico.
   > In questo esempio il nome di dominio completo è **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Creazione di un pool di indirizzi IP front-end e un pool di indirizzi back-end

1. Creare un pool di indirizzi IP front-end denominato **LB-Frontend** che usa la risorsa **PublicIp**.

    ```powershell
    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP
    ```

2. Creare un pool di indirizzi back-end denominato **LB-backend**.

    ```powershell
    $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend
    ```

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Creazione di regole NAT, una regola di bilanciamento del carico, un probe e un servizio di bilanciamento del carico

Questo esempio crea gli elementi seguenti:

* Una regola NAT per la conversione di tutto il traffico in ingresso nella porta 3441 alla porta 3389
* Una regola NAT per la conversione di tutto il traffico in ingresso nella porta 3442 alla porta 3389
* Una regola probe per il controllo dello stato di integrità in una pagina denominata **HealthProbe.aspx**
* Una regola di bilanciamento del carico per il bilanciamento di tutto il traffico in ingresso nella porta 80 alla porta 80 negli indirizzi nel pool back-end
* Un servizio di bilanciamento del carico che usa tutti questi oggetti

Usare i passaggi seguenti:

1. Creare le regole NAT.

    ```powershell
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
    ```

2. Creare un probe di integrità. Esistono due modi per configurare un probe:

    Probe HTTP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    Probe TCP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

3. Creare una regola del servizio di bilanciamento del carico.

    ```powershell
    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

4. Creare il servizio di bilanciamento del carico usando gli oggetti creati in precedenza.

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

## <a name="create-nics"></a>Creare NIC

Creare interfacce di rete (o modificare quelle esistenti) e associarle a regole NAT, regole di bilanciamento del carico e probe:

1. Ottenere la rete virtuale e la subnet per la rete virtuale in cui devono essere create le NIC.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Creare una NIC denominata **lb-nic1-be**e associarla alla prima regola NAT, quindi al primo (e unico) pool di indirizzi back-end.

    ```powershell
    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
    ```

3. Creare una NIC denominata **lb-nic2-be**e associarla alla seconda regola NAT, quindi al primo (e unico) pool di indirizzi back-end.

    ```powershell
    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
    ```

4. Creare le NIC.

        $backendnic1

    Output previsto:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Usare il cmdlet `Add-AzureRmVMNetworkInterface` per assegnare le schede NIC a macchine virtuali diverse.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Per indicazioni sulla creazione di una macchina virtuale e sull'assegnazione di una scheda di interfaccia di rete, vedere [Creare una VM di Azure con PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Aggiunta dell'interfaccia di rete al servizio di bilanciamento del carico

1. Recuperare il servizio di bilanciamento del carico da Azure.

    Se non è ancora stata eseguita questa operazione, caricare la risorsa di bilanciamento del carico in una variabile. La variabile si chiama **$lb**. Usare gli stessi nomi della risorsa di bilanciamento del carico creata in precedenza.

    ```powershell
    $lb= get-azurermloadbalancer -name NRP-LB -resourcegroupname NRP-RG
    ```

2. Caricare la configurazione back-end in una variabile.

    ```powershell
    $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
    ```

3. Caricare l'interfaccia di rete già creata in una variabile. Il nome della variabile è **$nic**. Il nome dell'interfaccia di rete è lo stesso dell'esempio precedente.

    ```powershell
    $nic =get-azurermnetworkinterface -name lb-nic1-be -resourcegroupname NRP-RG
    ```

4. Modificare la configurazione back-end nell'interfaccia di rete.

    ```powershell
    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
    ```

5. Salvare l'oggetto interfaccia di rete.

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```

    Dopo che un'interfaccia di rete viene aggiunta al pool di back-end di bilanciamento del carico, inizia a ricevere il traffico di rete in base alle regole di bilanciamento del carico per la risorsa di bilanciamento del carico.

## <a name="update-an-existing-load-balancer"></a>Aggiornare un bilanciamento del carico esistente

1. Usando il servizio di bilanciamento del carico dell'esempio precedente, assegnare un oggetto di bilanciamento del carico alla variabile **$slb** usando `Get-AzureLoadBalancer`.

    ```powershell
    $slb = get-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
    ```

2. Nell'esempio seguente, si aggiunge una regola NAT in ingresso mediante la porta 81 nel pool di front-end e la porta 8181 per il pool di back-end a un servizio di bilanciamento del carico esistente.

    ```powershell
    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP
    ```

3. Salvare la nuova configurazione tramite `Set-AzureLoadBalancer`.

    ```powershell
    $slb | Set-AzureRmLoadBalancer
    ```

## <a name="remove-a-load-balancer"></a>Rimuovere un bilanciamento del carico

Usare il comando `Remove-AzureLoadBalancer` per eliminare un servizio di bilanciamento del carico creato in precedenza denominato **NRP-LB** in un gruppo di risorse di nome **NRP-RG**.

```powershell
Remove-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> È possibile usare l'opzione facoltativa **-Force** per evitare la richiesta di eliminazione.

## <a name="next-steps"></a>Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
