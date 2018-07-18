---
title: Creare un servizio Load Balancer Standard con un front-end di indirizzo IP pubblico di zona usando Azure PowerShell | Microsoft Docs
description: Informazioni su come creare un servizio Load Balancer Standard con un front-end di indirizzo IP pubblico di zona usando Azure PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: dbb4176ac61cf707b28cddc98db80a1188be3cc8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
ms.locfileid: "31592131"
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-frontend-using-azure-powershell"></a>Creare un servizio Load Balancer Standard con un front-end di zona usando Azure PowerShell

Questo articolo illustra la creazione di un servizio [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) pubblico con un front-end di zona tramite un indirizzo IP pubblico standard. Per comprendere il funzionamento delle zone di disponibilità con il servizio Load Balancer Standard, vedere [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Il supporto per le zone di disponibilità è disponibile per determinate risorse, aree e famiglie di dimensioni di macchine virtuali di Azure. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Connect-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse usando il comando seguente:

```powershell
New-AzureRmResourceGroup -Name myResourceGroupZLB -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Creare un IP pubblico Standard 
Creare un IP pubblico Standard usando il comando seguente:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupZLB -Name 'myPublicIPZonal' `
  -Location westeurope -AllocationMethod Static -Sku Standard -zone 1
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Creare una configurazione IP front-end per il sito Web

Creare una configurazione IP front-end usando il comando seguente:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Creare un pool di indirizzi back-end

Creare un pool di indirizzi back-end usando il comando seguente:

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Creare un probe di bilanciamento del carico sulla porta 80

Creare un probe di integrità sulla porta 80 per il servizio di bilanciamento del carico usando il comando seguente:

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico
 Creare una regola del servizio di bilanciamento del carico usando il comando seguente:

```powershell
   $rule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Creare un servizio di bilanciamento del carico
Creare un servizio di bilanciamento del carico standard usando il comando seguente:

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroupZLB -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md).



