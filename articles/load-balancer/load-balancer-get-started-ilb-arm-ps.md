---
title: Creare un servizio di bilanciamento del carico interno di Azure - PowerShell | Documentazione Microsoft
description: Informazioni su come creare un servizio di bilanciamento del carico interno in Gestione risorse con PowerShell
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 7bd31ab8f52ec5e81f6966000554be46eaa59396
ms.contentlocale: it-it
ms.lasthandoff: 02/16/2017

---

# <a name="create-an-internal-load-balancer-using-powershell"></a>Creare un servizio di bilanciamento del carico interno con PowerShell

> [!div class="op_single_selector"]
> * [Portale di Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modello](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).  Questo articolo illustra il modello di distribuzione Resource Manager che Microsoft consiglia di usare per le distribuzioni più recenti in sostituzione del [modello di distribuzione classica](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

La procedura seguente illustra come creare un servizio di bilanciamento del carico interno usando Azure Resource Manager con PowerShell. Con Azure Resource Manager, gli elementi per creare un servizio di bilanciamento del carico interno vengono configurati singolarmente e poi integrati per creare un servizio di bilanciamento del carico.

È necessario creare e configurare gli oggetti seguenti per distribuire un servizio di bilanciamento del carico.

* Configurazione di IP front-end: configurazione dell'indirizzo IP privato per il traffico di rete in ingresso.
* Pool di indirizzi back end: configurazione delle interfacce di rete che riceveranno il traffico con carico bilanciato proveniente dal pool di indirizzi IP front end.
* Regole di bilanciamento del carico: configurazione delle porte di origine e locali per il servizio di bilanciamento del carico.
* Probe: configurazione del probe dello stato di integrità per le istanze di macchina virtuale.
* Regole NAT in ingresso: configurazione delle regole della porta per accedere direttamente a una delle istanze di macchina virtuale.

È possibile ottenere altre informazioni sui componenti del servizio di bilanciamento del carico con Gestione risorse di Azure in [Supporto di Gestione risorse di Azure per il bilanciamento del carico](load-balancer-arm.md).

La procedura seguente illustra come configurare un servizio di bilanciamento del carico tra due macchine virtuali.

## <a name="setup-powershell-to-use-resource-manager"></a>Configurare PowerShell per l'uso di Gestione risorse

Assicurarsi di disporre della versione di produzione più recente del modulo Azure per PowerShell e di aver configurato correttamente PowerShell per l'accesso alla sottoscrizione di Azure.

### <a name="step-1"></a>Passaggio 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account

```powershell
Get-AzureRmSubscription
```

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

### <a name="step-3"></a>Passaggio 3

Scegliere quali sottoscrizioni Azure usare.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="create-resource-group-for-load-balancer"></a>Creare un gruppo di risorse per il servizio di bilanciamento del carico

Creare un nuovo gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente)

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Azure Resource Manager richiede che tutti i gruppi di risorse specifichino una località. che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un servizio di bilanciamento del carico usino lo stesso gruppo di risorse.

Nell'esempio precedente sono stati creare un gruppo di risorse denominato "NRP-RG" e la località "West US".

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Creare la rete virtuale e un indirizzo IP privato per il pool di indirizzi IP front-end

Crea una subnet per la rete virtuale e la assegna alla variabile $backendSubnet

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Creare una rete virtuale:

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Crea la rete virtuale e aggiunge la subnet lb-subnet-be alla rete virtuale NRPVNet e la assegna alla variabile $vnet

## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>Creare il pool di indirizzi IP front-end e il pool di indirizzi back-end

Configurazione di un pool di indirizzi IP front-end per il traffico di rete in ingresso del servizio di bilanciamento del carico e un pool di indirizzi back-end per ricevere il traffico sottoposto a bilanciamento del carico.

### <a name="step-1"></a>Passaggio 1

Creare un pool IP front-end usando l'indirizzo IP privato 10.0.2.5 per la subnet 10.0.2.0/24 che sarà l'endpoint del traffico di rete in ingresso.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2"></a>Passaggio 2

Configurare un pool di indirizzi back-end usato per ricevere il traffico in ingresso dal pool di indirizzi IP front-end:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>Creare regole di bilanciamento del carico, regole NAT, probe e il servizio di bilanciamento del carico

Dopo aver creato il pool di indirizzi IP front-end e il pool di indirizzi back-end, è necessario creare le regole che faranno parte della risorsa di bilanciamento carico:

### <a name="step-1"></a>Passaggio 1

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

L'esempio precedente crea gli elementi seguenti:

* Regola NAT secondo cui tutto il traffico in ingresso sulla porta 3441 passerà alla porta 3389.
* Seconda regola NAT secondo cui tutto il traffico in ingresso sulla porta 3442 passerà alla porta 3389.
* Regola di bilanciamento del carico per bilanciare il carico di tutto il traffico in ingresso sulla porta pubblica 80 alla porta locale 80 nel pool di indirizzi back-end.
* Regola probe per il controllo dello stato di integrità del percorso "HealthProbe.aspx"

### <a name="step-2"></a>Passaggio 2

Creare il servizio di bilanciamento del carico aggiungendo tutti gli oggetti (regole NAT, regole di bilanciamento del carico, configurazioni di probe):

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-network-interfaces"></a>Creare interfacce di rete

Dopo aver creato il servizio di bilanciamento del carico interno, è necessario definire quali interfacce di rete riceveranno il traffico di rete con bilanciamento del carico in ingresso, regole NAT e probe. In questo caso, l'interfaccia di rete viene configurata singolarmente e può essere assegnata a una macchina virtuale in un secondo momento.

### <a name="step-1"></a>Passaggio 1

Ottenere la rete virtuale di risorse e la subnet per creare interfacce di rete:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

In questo passaggio viene creata un'interfaccia di rete che farà parte del pool di back-end del servizio di bilanciamento del carico e che assocerà la prima regola NAT per RDP per questa interfaccia di rete:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2"></a>Passaggio 2

Creare una seconda interfaccia di rete denominata LB-Nic2-BE:

In questo passaggio viene creata una seconda interfaccia di rete, che sarà assegnata allo stesso pool di back-end del servizio di bilanciamento del carico e che assocerà la seconda regola NAT creata per RDP:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Il risultato finale sarà simile al seguente:

    $backendnic1

Output previsto:

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



### <a name="step-3"></a>Passaggio 3

Usare il comando Add-AzureRmVMNetworkInterface per assegnare la scheda di rete a una macchina virtuale.

Vedere il documento [Creare una VM Windows con Resource Manager e PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) per le istruzioni dettagliate su come creare una macchina virtuale e assegnare una scheda di interfaccia di rete.

## <a name="add-the-network-interface"></a>Aggiungere l'interfaccia di rete

Se è già stata creata una macchina virtuale, è possibile aggiungere l'interfaccia di rete seguendo questa procedura:

### <a name="step-1"></a>Passaggio 1

Se non è ancora stata eseguita questa operazione, caricare la risorsa di bilanciamento del carico in una variabile. La variabile usata viene chiamata $lb e usa gli stessi nomi della risorsa di bilanciamento del carico creata in precedenza.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2"></a>Passaggio 2

Caricare la configurazione back-end in una variabile.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb
```

### <a name="step-3"></a>Passaggio 3

Caricare l'interfaccia di rete già creata in una variabile. Il nome della variabile usata è $nic. Il nome dell'interfaccia di rete usata è lo stesso dell'esempio precedente.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4"></a>Passaggio 4

Modificare la configurazione back-end nell'interfaccia di rete.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5"></a>Passaggio 5

Salvare l'oggetto interfaccia di rete.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Dopo che un'interfaccia di rete viene aggiunta al pool di back-end di bilanciamento del carico, inizia a ricevere il traffico di rete in base alle regole di bilanciamento del carico per la risorsa di bilanciamento carico.

## <a name="update-an-existing-load-balancer"></a>Aggiornare un bilanciamento del carico esistente

### <a name="step-1"></a>Passaggio 1
Usare il servizio di bilanciamento del carico dall'esempio precedente, assegnare l'oggetto del servizio di bilanciamento del carico alla variabile $slb usando Get-AzureRmLoadBalancer

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2"></a>Passaggio 2

Nell'esempio seguente, si aggiungerà una nuova regola NAT in ingresso mediante la porta 81 nel front-end e la porta 8181 per il pool di back-end a un bilanciamento del carico esistente

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3"></a>Passaggio 3

Salvare la nuova configurazione utilizzando Set AzureLoadBalancer

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-a-load-balancer"></a>Rimuovere un bilanciamento del carico

Usare il comando Remove-AzureRmLoadBalancerr per eliminare un servizio di bilanciamento del carico creato in precedenza denominato "NRP-LB" in un gruppo di risorse denominato "NRP-RG"

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> È possibile utilizzare l’opzione facoltativa - Forzare per evitare la richiesta di eliminazione.

## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

