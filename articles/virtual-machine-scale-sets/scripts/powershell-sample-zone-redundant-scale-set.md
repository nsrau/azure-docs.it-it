---
title: Esempi di Azure PowerShell - Set di scalabilità con ridondanza della zona | Microsoft Docs
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
ms.date: 04/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 00dd839c3ef8369490ffdd8840d6d4d8c69c76b5
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983883"
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>Creare un set di scalabilità di macchine virtuali con ridondanza della zona con PowerShell
Questo script crea un set di scalabilità di macchine virtuali che esegue Windows Server 2016 in più zone di disponibilità. Dopo aver eseguito lo script, è possibile accedere alla macchina virtuale tramite RDP.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Eseguire questo comando per rimuovere il gruppo di risorse, il set di scalabilità e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Crea il set di scalabilità di macchine virtuali e tutte le risorse di supporto, tra cui la rete virtuale, il servizio di bilanciamento del carico e le regole NAT. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Ottiene informazioni su un set di scalabilità di macchine virtuali. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Aggiunge un'estensione per VM per Script personalizzato per l'installazione di un'applicazione Web di base. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Aggiorna il modello del set di scalabilità di macchine virtuali per l'applicazione dell'estensione di VM. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Ottiene informazioni sull'indirizzo IP pubblico assegnato usato dal servizio di bilanciamento del carico. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell per i set di scalabilità di macchine virtuali sono disponibili nella [documentazione dei set di scalabilità di macchine virtuali di Azure](../powershell-samples.md).
