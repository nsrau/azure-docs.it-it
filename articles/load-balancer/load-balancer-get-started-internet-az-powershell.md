---
title: Creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona usando PowerShell | Microsoft Docs
description: Informazioni su come creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona usando PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: 9987fe1273dc05a2ad10c65325ad1d487d38247e
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>Creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona usando PowerShell

Questo articolo illustra la creazione di un [servizio di bilanciamento del carico standard](https://aka.ms/azureloadbalancerstandard) pubblico con un front-end con ridondanza della zona tramite un indirizzo standard IP pubblico.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Eseguire la registrazione per l'anteprima di Zone di disponibilità, Load Balancer Standard e l'IP pubblico Standard

Questo articolo richiede che sia installata la versione 4.4.0 o successiva del modulo AzureRM. Per trovare la versione, eseguire `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'installazione o l'aggiornamento, installare la versione più recente del modulo AzureRM da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureRM).

>[!NOTE]
Lo [SKU di Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) è attualmente in anteprima. Durante l'anteprima, la funzionalità potrebbe non avere lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Usare lo [SKU di Load Balancer Basic](load-balancer-overview.md) disponibile a livello generale per i servizi di produzione. 

> [!NOTE]
> Le zone di disponibilità sono in versione di anteprima e sono pronte per scenari di sviluppo e test. È disponibile il supporto per alcune risorse, aree e famiglie di dimensioni di macchina virtuale di Azure selezionate. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Prima di selezionare una zona o l'opzione con ridondanza della zona per l'indirizzo IP pubblico front-end per il servizio di bilanciamento del carico, è necessario completare i passaggi in [Eseguire la registrazione per l'anteprima di Zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse usando il comando seguente:

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Creare un IP pubblico Standard 
Creare un IP pubblico Standard usando il comando seguente:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Creare una configurazione IP front-end per il sito Web

Creare una configurazione IP front-end usando il comando seguente:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
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
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [creare un IP pubblico in una zona di disponibilità](../virtual-network/create-public-ip-availability-zone-portal.md)



