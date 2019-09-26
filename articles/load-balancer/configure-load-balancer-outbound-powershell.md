---
title: Configurare il bilanciamento del carico e le regole in uscita usando Azure PowerShell
titlesuffix: Azure Load Balancer
description: Questo articolo illustra come configurare il bilanciamento del carico e le regole in uscita in una Load Balancer Standard usando il Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262622"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>Configurare il bilanciamento del carico e le regole in uscita nel servizio di bilanciamento del carico standard usando Azure PowerShell

Questo articolo illustra come configurare le regole in uscita nel servizio di bilanciamento del carico standard usando Azure PowerShell.  

Al termine, la risorsa del servizio di bilanciamento del carico contiene due front-end e regole ad essi associate: uno per il traffico in ingresso e un altro per l'uscita.  Ogni front-end fa riferimento a un indirizzo IP pubblico e in questo scenario viene utilizzato un indirizzo IP pubblico diverso per connessioni in entrata e in uscita.   La regola di bilanciamento del carico offre solo il bilanciamento del carico in entrata e la regola in uscita controlla la NAT in uscita fornita per la macchina virtuale.  Questo articolo usa due pool back-end distinti, uno per le connessioni in ingresso e uno per il traffico in uscita, per illustrare la funzionalità e consentire flessibilità per questo scenario.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>Connettersi all'account Azure
Accedere alla sottoscrizione di Azure con il comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e seguire le istruzioni visualizzate:
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>Crea gruppo di risorse

Creare un gruppo di risorse con [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myresourcegroupoutbound* nella posizione *eastus2*:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale denominata *myvnetoutbound* con una subnet denominata *mysubnetoutbound* in *myresourcegroupoutbound* usando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) e [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0):

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>Crea indirizzo IP pubblico in ingresso 

Per accedere all'app Web in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. Un servizio di bilanciamento del carico standard supporta solo indirizzi IP pubblici standard. Usare [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) per creare un indirizzo IP pubblico standard denominato *mypublicipinbound* in *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>Creare un indirizzo IP pubblico in uscita 

Creare un indirizzo IP standard per la configurazione in uscita front-end del servizio di bilanciamento del carico usando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>Creare un Azure Load Balancer

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:
  - Un IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
  - Un pool back-end in cui l'IP front-end invia il traffico di rete con bilanciamento del carico.
  - Un pool back-end per la connettività in uscita. 
  - Un probe di integrità che determina l'integrità delle istanze delle macchine virtuali back-end.
  - Una regola di bilanciamento del carico in entrata che definisce come verrà distribuito il traffico alle macchine virtuali.
  - Una regola di bilanciamento del carico in uscita che definisce come verrà distribuito il traffico alle macchine virtuali.

### <a name="create-inbound-frontend-ip"></a>Crea IP front-end in ingresso
Creare la configurazione IP front-end in uscita per il servizio di bilanciamento del carico con [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) che include una configurazione IP front-end in ingresso denominata *myfrontendinbound* associata all'indirizzo *IP pubblico mypublicipinbound*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>Creare un indirizzo IP front-end in uscita
Creare la configurazione IP front-end in uscita per il servizio di bilanciamento del carico con [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) che include una configurazione IP front-end in uscita denominata *myfrontendoutbound* associata all'indirizzo *IP pubblico mypublicipoutbound*:

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>Crea pool back-end in ingresso
Creare il pool di back-end in ingresso per il servizio di bilanciamento del carico con [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) denominato *bepoolinbound*:

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>Crea pool back-end in uscita
Creare un pool di indirizzi back-end aggiuntivo per definire la connettività in uscita per un pool di macchine virtuali con [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) denominato *bepooloutbound*. La creazione di un pool in uscita separato garantisce la massima flessibilità, ma è possibile omettere questo passaggio e usare solo il *bepoolinbound* in ingresso.  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>Creare un probe integrità

Un probe di integrità controlla tutte le istanze di una macchina virtuale per assicurarsi che possano inviare il traffico di rete. L'istanza della macchina virtuale con controlli di probe falliti non viene rimossa dal servizio di bilanciamento del carico fino a quando non è nuovamente online e il controllo dei probe ne certifica l'integrità. Creare un probe di integrità con [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) per monitorare l'integrità delle macchine virtuali. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>Creare le regole di bilanciamento del carico

Una regola di bilanciamento del carico definisce la configurazione IP front-end per il traffico in entrata e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. Creare una regola di bilanciamento del carico *myinboundlbrule* con [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) per ascoltare la porta 80 nel pool Front-End *myfrontendinbound* e inviare il traffico di rete con carico bilanciato al pool *di indirizzi back-end bepoolinbound* usa anche la porta 80. 

>[!NOTE]
>Questa regola di bilanciamento del carico Disabilita la NAT in uscita automatica in seguito a questa regola con il parametro **-DisableOutboundSNAT** . Il NAT in uscita viene fornito solo dalla regola in uscita.

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>Creare una regola in uscita

Una regola in uscita definisce l'IP front-end pubblico, rappresentato dal pool front-end *myfrontendoutbound*, che verrà usato per tutto il traffico NAT in uscita, nonché per il pool back-end a cui si applica questa regola.  Creare una regola in uscita *myoutboundrule* per la traduzione della rete in uscita di tutte le macchine virtuali (configurazioni IP NIC) nel pool back-end *bepool*.  Inoltre, il comando seguente modifica il timeout di inattività in uscita da 4 a 15 minuti e alloca le porte SNAT 10000 anziché 1024.  Per altri dettagli, vedere [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0) .

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Se non si vuole usare un pool in uscita separato, è possibile modificare l'argomento del pool di indirizzi nel comando precedente per specificare invece *$bepoolin* .  È consigliabile usare pool distinti per la flessibilità e la leggibilità della configurazione risultante.

### <a name="create-load-balancer"></a>Crea servizio di bilanciamento del carico

Creare un servizio di bilanciamento del carico con l'indirizzo IP in ingresso usando [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) denominato *lb* che include una configurazione IP front-end in ingresso e un pool back-end *bepoolinbound* associato all'indirizzo *IP pubblico mypublicipinbound* creato nel passaggio precedente.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

A questo punto, è possibile continuare ad aggiungere le macchine virtuali al pool back-end *bepoolinbound* __e__ *BEPOOLOUTBOUND* aggiornando la configurazione IP delle rispettive risorse NIC usando [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, è possibile usare il comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) per rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato creato il servizio di bilanciamento del carico standard e sono state configurate le regole di bilanciamento del carico in ingresso, configurate e probe di integrità per le macchine virtuali nel pool back-end. Per altre informazioni sulle Azure Load Balancer, continuare con le esercitazioni per Azure Load Balancer.

> [!div class="nextstepaction"]
> [Esercitazioni su Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
