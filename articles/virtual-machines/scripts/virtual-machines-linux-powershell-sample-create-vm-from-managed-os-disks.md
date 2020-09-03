---
title: Creare una VM collegando un disco gestito come disco del sistema operativo (Linux) - PowerShell
description: Esempio di script di Azure PowerShell - Creare una VM collegando un disco gestito come disco del sistema operativo
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
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 68dc040be67c7b3ba764479655e18e5878f34d67
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077522"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell-linux"></a>Creare una macchina virtuale usando un disco del sistema operativo gestito esistente con PowerShell (Linux)

Questo script crea una macchina virtuale collegando un disco gestito esistente come disco del sistema operativo. Usare questo script negli scenari precedenti:
* Creare una VM da un disco del sistema operativo gestito esistente che è stato copiato da un disco gestito in una sottoscrizione diversa
* Creare una VM da un disco gestito esistente che è stato creato da un file VHD specializzato 
* Creare una VM da un disco del sistema operativo gestito esistente che è stato creato da uno snapshot 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per ottenere le proprietà di un disco gestito, collegare un disco gestito a una nuova VM e creare una VM. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzDisk](/powershell/module/az.compute/get-azdisk) | Ottiene l'oggetto disco in base al nome e al gruppo di risorse di un disco. La proprietà Id dell'oggetto disco restituito viene usata per collegare il disco a una nuova VM |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Crea una configurazione di VM. Questa configurazione include informazioni quali il nome della VM, il sistema operativo e le credenziali amministrative. La configurazione viene usata durante la creazione della VM. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Collega un disco gestito usando la proprietà Id del disco come disco del sistema operativo per una nuova macchina virtuale |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Crea un'interfaccia di rete. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Creare una macchina virtuale. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

Per le immagini del Marketplace usare [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) per impostare le informazioni sul piano.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script PowerShell per la macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
