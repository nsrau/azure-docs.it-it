---
title: Configurare il bilanciamento del carico e le regole in uscita tramite Azure PowerShellConfigure load balancing and outbound rules by using Azure PowerShell
titleSuffix: Azure Load Balancer
description: Questo articolo illustra come configurare il bilanciamento del carico e le regole in uscita in Load Balancer Standard tramite Azure PowerShell.This article shows how to configure load balancing and outbound rules in Standard Load Balancer by using Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225433"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Configurare il bilanciamento del carico e le regole in uscita in Load Balancer standard tramite Azure PowerShellConfigure load balancing and outbound rules in Standard Load Balancer by using Azure PowerShell

Questo articolo illustra come configurare le regole in uscita in Load Balancer Standard tramite Azure PowerShell.This article shows you how to configure outbound rules in Standard Load Balancer by using Azure PowerShell.  

Al termine dello scenario di questo articolo, la risorsa di bilanciamento del carico contiene due front-end e le regole associate. Si dispone di un front-end per il traffico in ingresso e un altro front-end per il traffico in uscita.  

Ogni front-end fa riferimento a un indirizzo IP pubblico. In questo scenario, l'indirizzo IP pubblico per il traffico in ingresso è diverso dall'indirizzo per il traffico in uscita.   La regola di bilanciamento del carico fornisce solo il bilanciamento del carico in ingresso. La regola in uscita controlla la conversione degli indirizzi di rete in uscita (NAT) per la macchina virtuale.  

Lo scenario utilizza due pool back-end: uno per il traffico in ingresso e uno per il traffico in uscita. Questi pool illustrano la funzionalità e offrono flessibilità per lo scenario.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure
Accedere alla sottoscrizione di Azure usando il comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Seguire quindi le istruzioni visualizzate sullo schermo.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse utilizzando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite le risorse di Azure.An Azure resource group is a logical container into which Azure resources are deployed. Le risorse vengono quindi gestite dal gruppo.

L'esempio seguente crea un gruppo di risorse denominato *myresourcegroupoutbound* nella posizione *eastus2*:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale denominata *myvnetoutbound*. Denominare la subnet *mysubnetoutbound*. Inserirlo in *myresourcegroupoutbound* utilizzando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) e [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Creare un indirizzo IP pubblico in ingressoCreate an inbound public IP address 

Per accedere all'app Web su Internet, è necessario un indirizzo IP pubblico per il servizio di bilanciamento del carico. Load Balancer Standard supporta solo indirizzi IP pubblici standard. 

Utilizzare [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) per creare un indirizzo IP pubblico standard denominato *mypublicipinbound* in *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Creare un indirizzo IP pubblico in uscitaCreate an outbound public IP address 

Creare un indirizzo IP standard per la configurazione in uscita front-end del servizio di bilanciamento del carico utilizzando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Creare un servizio di bilanciamento del carico di Azure

In questa sezione viene illustrato come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:This section explains how to create and configure the following components of the load balancer:
  - IP front-end che riceve il traffico di rete in ingresso nel servizio di bilanciamento del carico
  - Un pool back-end in cui l'IP front-end invia il traffico di rete con carico bilanciato
  - Un pool back-end per la connettività in uscita
  - Un probe di integrità che determina l'integrità delle istanze della macchina virtuale back-end
  - Una regola in ingresso del servizio di bilanciamento del carico che definisce la modalità di distribuzione del traffico alle macchine virtualiA load-balancer inbound rule that defines how traffic is distributed to the VMs
  - Una regola in uscita del servizio di bilanciamento del carico che definisce la modalità di distribuzione del traffico dalle macchine virtualiA load-balancer outbound rule that defines how traffic is distributed from the VMs

### <a name="create-an-inbound-front-end-ip"></a>Creare un indirizzo IP front-end in ingressoCreate an inbound front-end IP
Creare la configurazione IP front-end in ingresso per il servizio di bilanciamento del carico utilizzando [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Il servizio di bilanciamento del carico deve includere una configurazione IP front-end in ingresso denominata *myfrontendinbound*. Associare questa configurazione all'indirizzo IP pubblico *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Creare un IP front-end in uscitaCreate an outbound front-end IP
Creare la configurazione IP front-end in uscita per il servizio di bilanciamento del carico utilizzando [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Questo servizio di bilanciamento del carico deve includere una configurazione IP front-end in uscita denominata *myfrontendoutbound*. Associare questa configurazione all'indirizzo IP pubblico *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Creare un pool back-end in ingressoCreate an inbound back-end pool
Creare il pool in ingresso back-end per il servizio di bilanciamento del carico utilizzando [New-AzLoadBalancerBackaddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Assegnare al pool il nome *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Creare un pool back-end in uscitaCreate an outbound back-end pool
Utilizzare il comando seguente per creare un altro pool di indirizzi back-end per definire la connettività in uscita per un pool di macchine virtuali utilizzando [New-AzLoadBalancerBackAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Denominare il pool *bepooloutbound*. 

Creando un pool in uscita separato, si offre la massima flessibilità. Ma è possibile omettere questo passaggio e utilizzare solo il *bepoolinbound* in ingresso, se si preferisce.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Un probe di integrità controlla tutte le istanze della macchina virtuale per assicurarsi di poter inviare traffico di rete. L'istanza della macchina virtuale che non supera i controlli del probe viene rimossa dal servizio di bilanciamento del carico finché non torna online e un controllo probe determina che è integro. 

Per monitorare l'integrità delle macchine virtuali, creare un probe di integrità utilizzando [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del caricoCreate a load-balancer rule

Una regola di bilanciamento del carico definisce la configurazione IP front-end per il traffico in ingresso e il pool back-end per ricevere il traffico. Definisce inoltre la porta di origine e di destinazione richiesta. 

Creare una regola di bilanciamento del carico denominata *myinboundlbrule* utilizzando [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Questa regola rimarrà in ascolto sulla porta 80 nel pool front-end *myfrontendinbound*. Verrà inoltre utilizzata la porta 80 per inviare il traffico di rete con carico bilanciato al pool di indirizzi back-end *bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Questa regola di bilanciamento del carico disabilita il NAT sicuro in uscita automatico (SNAT) a causa del parametro **-DisableOutboundSNAT.** Il NAT in uscita viene fornito solo dalla regola in uscita.

### <a name="create-an-outbound-rule"></a>Creare una regola in uscita

Una regola in uscita definisce l'IP pubblico front-end, rappresentato dal front-end *myfrontendoutbound*. Questo front-end verrà utilizzato per tutto il traffico NAT in uscita e per il pool back-end a cui si applica la regola.  

Usare il comando seguente per creare una regola in uscita *myoutboundrule* per la conversione di rete in uscita di tutte le macchine virtuali (nelle configurazioni IP della scheda di interfaccia di rete) nel pool back-end *bepool.*  Il comando modifica il timeout di inattività in uscita da 4 a 15 minuti. Alloca 10.000 porte SNAT invece di 1.024. Per ulteriori informazioni, vedere [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Se non si desidera utilizzare un pool in uscita separato, è possibile modificare l'argomento del pool di indirizzi nel comando precedente per specificare *invece $bepoolin.*  È consigliabile utilizzare pool separati per rendere la configurazione risultante flessibile e leggibile.

### <a name="create-a-load-balancer"></a>Creare un servizio di bilanciamento del carico

Utilizzare il comando seguente per creare un servizio di bilanciamento del carico per l'indirizzo IP in ingresso utilizzando [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Assegnare al servizio di bilanciamento del carico il *nome lb*. Deve includere una configurazione IP front-end in ingresso. Il pool back-end *deve* essere associato all'indirizzo IP pubblico *mypublicipinbound* creato nel passaggio precedente.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

A questo punto, è possibile continuare ad aggiungere le macchine virtuali ai pool back-end *bepoolinbound* e *bepooloutbound* aggiornando la configurazione IP delle rispettive risorse nic. Aggiornare la configurazione delle risorse utilizzando [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse, il servizio di bilanciamento del carico e le risorse correlate non sono più necessari, è possibile rimuoverli utilizzando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato creato un servizio di bilanciamento del carico standard, sono state configurate le regole del traffico del servizio di bilanciamento del carico in ingresso e in uscita e è stato configurato un probe di integrità per le macchine virtuali nel pool back-end. 

Per altre informazioni, continuare con [le esercitazioni per Azure Load Balancer.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
