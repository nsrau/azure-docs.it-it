---
title: Creare una VM da uno snapshot (Linux) - Esempio di PowerShell
description: Esempio di script di Azure PowerShell - Creare una VM da uno snapshot
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: d6bf74b9040ec3f677c23c84f8ece22befb35d56
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010292"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-linux"></a>Creare una macchina virtuale da uno snapshot con PowerShell (Linux)

Questo script crea una macchina virtuale da uno snapshot di un disco del sistema operativo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per ottenere le proprietà dello snapshot, creare un disco gestito da uno snapshot e creare una VM. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Ottiene uno snapshot usando il nome dello snapshot. |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Crea una configurazione di disco. Questa configurazione viene usata con il processo di creazione del disco. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Crea un disco gestito. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Crea una configurazione di VM. Questa configurazione include informazioni quali il nome della VM, il sistema operativo e le credenziali amministrative. La configurazione viene usata durante la creazione della VM. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Collega il disco gestito come disco del sistema operativo alla macchina virtuale |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Crea un'interfaccia di rete. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Consente di creare una macchina virtuale. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script PowerShell per la macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
