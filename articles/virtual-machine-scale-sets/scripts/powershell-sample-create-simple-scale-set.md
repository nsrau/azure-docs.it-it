---
title: Esempi di Azure PowerShell - Creare un set di scalabilità di macchine virtuali di base
description: Questo script crea un set di scalabilità di macchine virtuali di Azure che esegue Windows Server 2016, a cui è possibile accedere tramite RDP.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 03/27/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 021719751d1f1cc93d1ad841429c2e025a902c8a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078895"
---
# <a name="create-a-basic-virtual-machine-scale-set-with-powershell"></a>Creare un set di scalabilità di macchine virtuali di base con PowerShell
Questo script crea un set di scalabilità di macchine virtuali che esegue Windows Server 2016. Dopo aver eseguito lo script, è possibile accedere alle istanze di macchina virtuale tramite RDP.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Script di esempio


[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.ps1 "Create a simple virtual machine scale set")]

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
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).
