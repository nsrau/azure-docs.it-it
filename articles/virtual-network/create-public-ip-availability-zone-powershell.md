---
title: Creare un indirizzo IP pubblico suddiviso in zone con PowerShell | Microsoft Docs
description: "Creare un indirizzo IP pubblico in una zona di disponibilità con PowerShell."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 701a8febb3f897fd7f1a81a00adb4635df153d3a
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>Creare un indirizzo IP pubblico in una zona di disponibilità con PowerShell

È possibile distribuire un indirizzo IP pubblico in una zona di disponibilità di Azure (anteprima). Una zona di disponibilità è una zona fisicamente separata in un'area di Azure. È possibile passare agli argomenti seguenti:

> * Creare un indirizzo IP pubblico in una zona di disponibilità
> * Identificare le risorse correlate create nella zona di disponibilità
  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Questo articolo richiede che sia installata la versione 4.4.0 o successiva del modulo AzureRM. Per trovare la versione, eseguire `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'installazione o l'aggiornamento, installare la versione più recente del modulo AzureRM da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureRM).

> [!NOTE]
> Le zone di disponibilità sono in versione di anteprima e sono pronte per scenari di sviluppo e test. È disponibile il supporto per alcune risorse, aree e famiglie di dimensioni di macchina virtuale di Azure selezionate. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *westeurope*. *westeurope* è una delle aree di Azure che supportano le zone di disponibilità in anteprima.

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>Creare un indirizzo IP pubblico di zona

Creare un indirizzo IP pubblico in una zona di disponibilità usando il comando seguente:

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> Quando si assegna un indirizzo IP pubblico con SKU Standard all'interfaccia di rete di una macchina virtuale, è necessario consentire in modo esplicito il traffico previsto con un [gruppo di sicurezza di rete](security-overview.md#network-security-groups). La comunicazione con la risorsa non riesce finché non si crea e si associa un gruppo di sicurezza di rete e si consente in modo esplicito il traffico desiderato.

## <a name="get-zone-information-about-a-public-ip-address"></a>Ottenere informazioni sulla zona di un indirizzo IP pubblico

Ottenere le informazioni sulla zona di un indirizzo IP pubblico usando il comando seguente:

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>Passaggi successivi

- Leggere altre informazioni sulle [zone di disponibilità](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
- Leggere altre informazioni sugli [indirizzi IP pubblici](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
