---
title: Creare un servizio di bilanciamento del carico interno di Azure con PowerShell | Microsoft Docs
description: Informazioni su come creare un servizio di bilanciamento del carico interno usando il modulo Azure PowerShell con Azure Resource Manager
services: load-balancer
documentationcenter: na
author: KumudD
manager: jennoc
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 6eea6c9bc7f686096c3cf0c97bfbe65a5507de2a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Creare un servizio di bilanciamento del carico interno usando il modulo Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modello](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Introduzione alla configurazione

Questo articolo descrive come creare un servizio di bilanciamento del carico interno usando Azure Resource Manager con il modulo Azure PowerShell. Nel modello di distribuzione di Azure Resource Manager gli oggetti necessari per creare un servizio di bilanciamento del carico interno vengono configurati singolarmente. Una volta creati e configurati, gli oggetti vengono combinati per creare un servizio di bilanciamento del carico.

Per distribuire un servizio di bilanciamento del carico, è necessario creare gli oggetti seguenti:

* Pool di indirizzi IP front-end: indirizzo IP privato per tutto il traffico di rete in ingresso.
* Pool di indirizzi back-end: interfacce di rete che ricevono il traffico con carico bilanciato proveniente dall'indirizzo IP front-end.
* Regole di bilanciamento del carico: configurazione delle porte, di origine e locali, per il servizio di bilanciamento del carico.
* Configurazione del probe: probe dello stato di integrità per le macchine virtuali.
* Regole NAT in ingresso: regole delle porte per l'accesso diretto alle macchine virtuali.

Per altre informazioni sui componenti del servizio di bilanciamento del carico, vedere [Supporto di Azure Resource Manager per il servizio di bilanciamento del carico](load-balancer-arm.md).

La procedura seguente illustra come configurare un servizio di bilanciamento del carico tra due macchine virtuali.

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurazione di PowerShell per l'uso di Resource Manager

Assicurarsi di avere la versione di produzione più recente del modulo Azure PowerShell. PowerShell deve essere configurato correttamente per accedere alla sottoscrizione di Azure.

### <a name="step-1-start-powershell"></a>Passaggio 1: Avviare PowerShell

Avviare il modulo PowerShell per Azure Resource Manager.

```powershell
Login-AzureRmAccount
```

### <a name="step-2-view-your-subscriptions"></a>Passaggio 2: Visualizzare le sottoscrizioni

Controllare le sottoscrizione di Azure disponibili.

```powershell
Get-AzureRmSubscription
```

Immettere le credenziali quando viene chiesto di autenticarsi.

### <a name="step-3-select-the-subscription-to-use"></a>Passaggio 3: Selezionare la sottoscrizione da usare

Scegliere le sottoscrizioni di Azure da usare per la distribuzione del servizio di bilanciamento del carico.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>Passaggio 4: Scegliere il gruppo di risorse per il servizio di bilanciamento del carico

Creare un nuovo gruppo di risorse per il servizio di bilanciamento del carico. Se si usa un gruppo di risorse esistente, ignorare questo passaggio.

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per tutte le risorse in questo gruppo di risorse. Usare sempre lo stesso gruppo di risorse per tutti i comandi correlati alla creazione del servizio di bilanciamento del carico.

Nell'esempio è stato creato un gruppo di risorse denominato **NRP-RG** nell'area Stati Uniti occidentali.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Creare la rete virtuale e l'indirizzo IP pubblico per il pool di indirizzi IP front-end

Creare una subnet per la rete virtuale e assegnarla alla variabile **$backendSubnet**.

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Creare una rete virtuale.

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

La rete virtuale viene creata. La subnet **LB-Subnet-BE** viene aggiunta alla rete virtuale **NRPVNet**. Questi valori vengono assegnati alla variabile **$vnet**.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Creare il pool di indirizzi IP front-end e il pool di indirizzi back-end

Creare un pool di indirizzi IP front-end per il traffico in ingresso e un pool di indirizzi back-end per ricevere il traffico con carico bilanciato.

### <a name="step-1-create-a-front-end-ip-pool"></a>Passaggio 1: Creare un pool di indirizzi IP front-end

Creare un pool di indirizzi IP front-end con l'indirizzo IP privato 10.0.2.5 per la subnet 10.0.2.0/24. Questo indirizzo è l'endpoint del traffico di rete in ingresso.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>Passaggio 2: Creare un pool di indirizzi back-end

Creare un pool di indirizzi back-end per ricevere il traffico in ingresso dal pool di indirizzi IP front-end:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Creare le regole di configurazione, il probe e il servizio di bilanciamento del carico

Dopo aver creato il pool di indirizzi IP front-end e il pool di indirizzi back-end, specificare le regole per la risorsa di bilanciamento del carico.

### <a name="step-1-create-the-configuration-rules"></a>Passaggio 1: Creare le regole di configurazione

L'esempio crea i quattro oggetti regola seguenti:

* Regola NAT in ingresso per Remote Desktop Protocol (RDP): reindirizza tutto il traffico in ingresso sulla porta 3441 alla porta 3389.
* Seconda regola NAT in ingresso per RDP: reindirizza tutto il traffico in ingresso sulla porta 3442 alla porta 3389.
* Regola per il probe di integrità: controlla lo stato di integrità del percorso di HealthProbe.aspx.
* Regola di bilanciamento del carico: bilancia il carico di tutto il traffico in ingresso sulla porta pubblica 80 alla porta locale 80 nel pool di indirizzi back-end.

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>Passaggio 2: Creare il servizio di bilanciamento del carico

Creare il servizio di bilanciamento del carico e combinare gli oggetti regola (NAT in ingresso per RDP, bilanciamento del carico e probe di integrità):

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>Creare le interfacce di rete

Dopo avere creato il servizio di bilanciamento del carico interno, definire le interfacce di rete (NIC) che riceveranno il traffico di rete in ingresso con carico bilanciato, le regole NAT e il probe. Ogni interfaccia di rete viene configurata singolarmente e viene successivamente assegnata a una macchina virtuale.

### <a name="step-1-create-the-first-network-interface"></a>Passaggio 1: Creare la prima interfaccia di rete

Ottenere la rete virtuale e la subnet delle risorse. Per creare le interfacce di rete, vengono usati questi valori:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Creare la prima interfaccia di rete con il nome **lb-nic1-be**. Assegnare l'interfaccia di rete al pool back-end del servizio di bilanciamento del carico. Associare la prima regola NAT per RDP a questa scheda di interfaccia di rete:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>Passaggio 2: Creare la seconda interfaccia di rete

Creare la seconda interfaccia di rete con il nome **lb-nic2-be**. Assegnare la seconda interfaccia di rete allo stesso pool back-end del servizio di bilanciamento del carico della prima interfaccia. Associare la seconda scheda di interfaccia di rete alla seconda regola NAT per RDP:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Rivedere la configurazione:

    $backendnic1

Le impostazioni devono essere le seguenti:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
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
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>Passaggio 3: Assegnare la scheda di interfaccia di rete a una macchina virtuale

Assegnare la scheda di interfaccia di rete a una macchina virtuale usando il comando `Add-AzureRmVMNetworkInterface`.

Per istruzioni dettagliate per la creazione di una macchina virtuale e l'assegnazione della scheda di interfaccia di rete, vedere [Creare una macchina virtuale di Azure con PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Aggiungere l'interfaccia di rete

Dopo aver creato la macchina virtuale, aggiungere l'interfaccia di rete.

### <a name="step-1-store-the-load-balancer-resource"></a>Passaggio 1: Archiviare la risorsa di bilanciamento del carico

Se non è già stato fatto, archiviare la risorsa di bilanciamento del carico in una variabile. Viene usato il nome di variabile **$lb**. Per i valori degli attributi nello script, usare i nomi per le risorse di bilanciamento del carico create nei passaggi precedenti.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>Passaggio 2: Archiviare la configurazione back-end

Archiviare la configurazione back-end nella variabile **$backend**.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>Passaggio 3: Archiviare l'interfaccia di rete

Archiviare l'interfaccia di rete in un'altra variabile. Questa interfaccia è stata creata nel passaggio 1 "Creare le interfacce di rete". Viene usato il nome di variabile **$nic1**. Usare lo stesso nome di interfaccia di rete dell'esempio precedente.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>Passaggio 4: Modificare la configurazione back-end

Modificare la configurazione back-end nell'interfaccia di rete.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>Passaggio 5: Salvare l'oggetto interfaccia di rete

Salvare l'oggetto interfaccia di rete.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Dopo aver aggiunto l'interfaccia al pool back-end, il carico del traffico di rete viene bilanciato in base alle regole. Queste regole sono state configurate in "Creare le regole di configurazione, il probe e il servizio di bilanciamento del carico".

## <a name="update-an-existing-load-balancer"></a>Aggiornare un bilanciamento del carico esistente

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>Passaggio 1: Assegnare l'oggetto servizio bilanciamento del carico a una variabile

Assegnare l'oggetto servizio di bilanciamento del carico (dall'esempio precedente) alla variabile **$slb** usando il comando `Get-AzureRmLoadBalancer`:

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>Passaggio 2: Aggiungere una regola NAT

Aggiungere una regola NAT in ingresso a un servizio di bilanciamento del carico esistente. Usare la porta 81 per il pool front-end e la porta 8181 per il pool back-end:

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>Passaggio 3: Salvare la configurazione

Salvare la nuova configurazione usando il comando `Set-AzureLoadBalancer`:

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Rimuovere un servizio di bilanciamento del carico esistente

Eliminare il servizio di bilanciamento del carico **NRP-LB** nel gruppo di risorse **NRP-RG** usando il comando `Remove-AzureRmLoadBalancer`:

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Usare l'opzione **-Force** facoltativa per impedire la richiesta di conferma dell'eliminazione.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare la modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)
* [Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
