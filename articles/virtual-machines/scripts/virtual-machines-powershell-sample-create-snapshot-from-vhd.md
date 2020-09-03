---
title: Snapshot del disco rigido virtuale per creare molti dischi gestiti identici (Windows) - PowerShell
description: 'Esempio di script di Azure PowerShell: Creare uno snapshot da un VHD per ottenere rapidamente più dischi gestiti identici'
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 47ade94ad2e0be0ce44d346336c6c7eea4fca78b
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322736"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-windows"></a>Creare uno snapshot da un disco rigido virtuale per ottenere rapidamente più dischi gestiti identici con PowerShell (Windows)

Questo script crea uno snapshot da un file VHD in un account di archiviazione nella stessa sottoscrizione o in una sottoscrizione diversa. Usare lo script per importare un file VHD specializzato (non generico o preparato con Sysprep) in uno snapshot e quindi usare lo snapshot per creare rapidamente più dischi gestiti. Usarlo anche per importare un VHD di dati in uno snapshot e quindi usare lo snapshot per creare rapidamente più dischi gestiti. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>Passaggi successivi

[Creare un disco gestito da uno snapshot](virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Creare una macchina virtuale collegando un disco gestito come disco del sistema operativo](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script PowerShell della macchina virtuale sono reperibili nella [documentazione della VM Windows di Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
