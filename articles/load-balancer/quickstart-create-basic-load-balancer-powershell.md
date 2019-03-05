---
title: "Guida introduttiva: Creare un'istanza di Load Balancer Basic - Azure PowerShell"
titlesuffix: Azure Load Balancer
description: In questa guida introduttiva si apprende come creare un'istanza di Load Balancer Basic usando PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms:custom: seodec18
ms.openlocfilehash: 6b27c21944131d01254e75c7120520a119998132
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673769"
---
# <a name="get-started"></a>Guida introduttiva: Creare un servizio di bilanciamento del carico pubblico con Azure PowerShell

In questa guida introduttiva si apprende come creare un'istanza di Load Balancer Basic usando Azure PowerShell. Per testare il servizio di bilanciamento del carico, si distribuiscono due macchine virtuali che eseguono Windows Server e si bilancia il carico di un'app Web tra le due macchine virtuali.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per poter creare un servizio di bilanciamento del carico è prima necessario creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroupLB* nella posizione *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico
Per accedere all'app in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. Creare un indirizzo IP pubblico con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Nell'esempio seguente viene creato un indirizzo IP pubblico denominato *myPublicIP* nel gruppo di risorse *myResourceGroupLB*:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

In questa sezione viene configurato l'indirizzo IP front-end e il pool di indirizzi back-end del servizio di bilanciamento del carico e quindi viene creata l'istanza di Load Balancer Basic.

### <a name="create-front-end-ip"></a>Creare l'IP front-end

Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Nell'esempio seguente viene creata una configurazione IP front-end denominata *myFrontEnd* e viene associato l'indirizzo *myPublicIP*:

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-back-end-address-pool"></a>Configurare il pool di indirizzi back-end

Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Le macchine virtuali si collegano a questo pool back-end nei passaggi rimanenti. L'esempio seguente crea un pool di indirizzi back-end denominato *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

### <a name="create-a-health-probe"></a>Creare un probe di integrità
Per consentire al servizio di bilanciamento del carico di monitorare lo stato dell'app, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. Per impostazione predefinita, una VM viene rimossa dalla distribuzione del servizio di bilanciamento del carico dopo due errori consecutivi a intervalli di 15 secondi. Un probe di integrità viene creato in base a un protocollo o una specifica pagina di controllo integrità per l'app. 

Nell'esempio seguente viene creato un probe TCP. È anche possibile creare probe HTTP personalizzati per i controlli di integrità con granularità fine. Quando si usa un probe HTTP personalizzato, è necessario creare la pagina di controllo integrità, ad esempio *healthcheck.aspx*. Il probe deve restituire la risposta **HTTP 200 OK** affinché il servizio di bilanciamento del carico mantenga l'host nella rotazione.

Per creare un probe di integrità TCP, usare [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). Nell'esempio seguente viene creato un probe di integrità denominato *myHealthProbe* che monitora ogni macchina virtuale sulla porta *HTTP* *80*:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. Per assicurarsi che solo le macchine virtuali integre ricevano il traffico, è necessario anche definire il probe di integrità da usare.

Creare una regola di bilanciamento del carico con [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). L'esempio seguente crea una regola di bilanciamento del carico denominata *myLoadBalancerRule* e il traffico viene bilanciato sulla porta *TCP* *80*:

```azurepowershell-interactive
$lbrule = New-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>Creare le regole NAT

Creare le regole NAT con [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). Nell'esempio seguente vengono create le regole NAT denominate *myLoadBalancerRDP1* e *myLoadBalancerRDP2* per consentire le connessioni RDP verso i server back-end con le porte 4221 e 4222:

```azurepowershell-interactive
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

Creare l'istanza di Load Balancer Basic con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Nell'esempio seguente viene creata un'istanza pubblica di Load Balancer Basic denominata myLoadBalancer, usando la configurazione IP front-end, il pool back-end, il probe di integrità, la regola di bilanciamento del carico e le regole NAT creati nei passaggi precedenti:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>Creare risorse di rete

Prima di distribuire alcune macchine virtuali e testare il servizio di bilanciamento del carico, è necessario creare le risorse di rete virtuale di supporto, ovvero la rete virtuale e le schede di interfaccia di rete virtuale. 

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Nell'esempio seguente viene creata una rete virtuale denominata *myVnet* con una subnet denominata *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete per definire le connessioni in ingresso alla rete virtuale.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Creare una regola del gruppo di sicurezza di rete per la porta 3389

Per consentire le connessioni RDP tramite la porta 3389, creare una regola del gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Creare una regola del gruppo di sicurezza di rete per la porta 80

Per consentire le connessioni in ingresso tramite la porta 80, creare una regola del gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup' `
-SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Creare NIC

Creare le schede di interfaccia di rete virtuale con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Nell'esempio seguente vengono create due schede di interfaccia di rete virtuali. Una scheda di interfaccia di rete virtuale per ogni VM creata per l'app nei passaggi successivi. È possibile creare altre schede di interfaccia di rete virtuale e macchine virtuali in qualsiasi momento e aggiungerle al bilanciamento del carico:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Per aumentare la disponibilità elevata dell'app, posizionare le macchine virtuali in un set di disponibilità.

Creare un set di disponibilità con [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). L'esempio seguente crea un set di disponibilità denominato *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

A questo punto è possibile creare le VM con [New-AzVM](/powershell/module/az.compute/new-azvm). Nell'esempio seguente vengono creare due macchine virtuali e i componenti di rete virtuale necessari, se non esistono già. Durante la creazione della macchina virtuale nell'esempio seguente, le schede di rete create in precedenza sono associate a macchine virtuali poiché sono assegnate alla stessa rete (*myVnet*) e subnet (*mySubnet*) virtuali:

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Il parametro `-AsJob` crea la VM come attività in background, in modo che i prompt di PowerShell vengano restituiti all'utente. È possibile visualizzare i dettagli dei processi in background con il cmdlet `Job`. La creazione e la configurazione di entrambe le macchine virtuali richiedono alcuni minuti.

### <a name="install-iis-with-custom-web-page"></a>Installare IIS con una pagina Web personalizzata
 
Installare IIS con una pagina Web personalizzata in entrambe le macchine virtuali di back-end come indicato di seguito:

1. Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico. Usando `Get-AzPublicIPAddress` ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico.

  ```azurepowershell-interactive
    Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
  ```
2. Creare una connessione desktop remoto per VM1 usando l'indirizzo IP pubblico ottenuto nel passaggio precedente. 

  ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
  ```
3. Immettere le credenziali di *VM1* per avviare la sessione RDP.
4. Avviare Windows PowerShell su VM1 e usare i comandi seguenti per installare il server IIS e aggiornare il file con estensione htm predefinito.
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. Chiudere la connessione RDP con *myVM1*.
6. Creare una connessione RDP con *myVM2* con il comando `mstsc /v:PublicIpAddress:4222` e ripetere il passaggio 4 per *VM2*.

## <a name="test-load-balancer"></a>Testare il bilanciamento del carico
Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Nell'esempio seguente si ottiene l'indirizzo IP per *myPublicIP* creato in precedenza:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

Sarà quindi possibile immettere l'indirizzo IP pubblico in un Web browser. Verrà visualizzato il sito Web, con il nome host della macchina virtuale a cui il servizio di bilanciamento del carico ha distribuito il traffico, come nell'esempio seguente:

![Testare il bilanciamento del carico](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Per verificare la distribuzione del traffico tra le due macchine virtuali che eseguono l'app da parte del servizio di bilanciamento del carico, forzare l'aggiornamento del Web browser.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è creata un'istanza di Load Balancer Basic, si sono collegate macchine virtuali, si è eseguita la configurazione della regola del traffico di bilanciamento del carico e del probe di integrità e quindi si è testato il servizio di bilanciamento del carico. Per altre informazioni su Azure Load Balancer, passare alle esercitazioni su Azure Load Balancer.

> [!div class="nextstepaction"]
> [Esercitazioni su Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)