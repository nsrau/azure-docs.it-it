---
title: Esempi di Azure PowerShell - Creare un set di scalabilità di macchine virtuali completo | Microsoft Docs
description: Esempi di Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ebbc47739b2be72d0dd98c0659bfcaba512e79e9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448916"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Creare un set di scalabilità di macchine virtuali completo con PowerShell

Questo script crea un set di scalabilità di macchine virtuali che esegue Windows Server 2016. Vengono configurate e create singole risorse, anziché essere usate le [opzioni predefinite per la creazione di risorse disponibili in New-AzVmss](powershell-sample-create-simple-scale-set.md). Dopo aver eseguito lo script, è possibile accedere alle istanze di macchina virtuale tramite RDP.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Eseguire questo comando per rimuovere il gruppo di risorse, il set di scalabilità e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crea una configurazione di subnet. Questa configurazione viene usata con il processo di creazione della rete virtuale. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea una rete virtuale. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Crea una configurazione IP front-end per un servizio di bilanciamento del carico. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Crea una configurazione del pool di indirizzi back-end per un servizio di bilanciamento del carico. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Crea la configurazione di una regola NAT in ingresso per un servizio di bilanciamento del carico. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Crea un servizio di bilanciamento del carico. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Crea la configurazione di una porta probe per un servizio di bilanciamento del carico. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Crea la configurazione di una regola per un servizio di bilanciamento del carico. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Aggiornare il bilanciamento del carico con le informazioni fornite. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | Creare una configurazione IP per le istanze della macchina virtuale del set di scalabilità. Le istanze della macchina virtuale sono connesse al pool back-end di bilanciamento del carico, al pool NAT e alla subnet della rete virtuale. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Crea una configurazione del set di scalabilità. Questa configurazione include informazioni quali il numero di istanze della macchina virtuale da creare, lo SKU della macchina virtuale (dimensione) e la modalità di aggiornamento dei criteri. La configurazione viene ampliata da altri cmdlet e usata durante la creazione di set di scalabilità. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Definisce l'immagine da usare per le istanze di macchina virtuale e aggiungerla alla configurazione del set di scalabilità. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Definisce le credenziali di nome e password dell'utente amministrativo e il prefisso per la denominazione della macchina virtuale. Aggiungere questi valori alla configurazione del set di scalabilità. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Aggiunge un'interfaccia di rete virtuale alle istanze di macchina virtuale, in base alla configurazione IP. Aggiungere questi valori alla configurazione del set di scalabilità. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | Crea il set di scalabilità in base alle informazioni specificate nella configurazione del set di scalabilità. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell per i set di scalabilità di macchine virtuali sono disponibili nella [documentazione dei set di scalabilità di macchine virtuali di Azure](../powershell-samples.md).
