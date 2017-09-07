---
title: "Esempio di script di Azure PowerShell: Creare uno snapshot da un VHD per ottenere rapidamente più dischi gestiti identici | Microsoft Docs"
description: "Esempio di script di Azure PowerShell: Creare uno snapshot da un VHD per ottenere rapidamente più dischi gestiti identici"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 02a69abd6c17ce765996379309e22afad82c4e10
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Creare uno snapshot da un VHD per ottenere rapidamente più dischi gestiti identici con PowerShell

Questo script crea uno snapshot da un file VHD in un account di archiviazione nella stessa sottoscrizione o in una sottoscrizione diversa. Usare lo script per importare un file VHD specializzato (non generico o preparato con Sysprep) in uno snapshot e quindi usare lo snapshot per creare rapidamente più dischi gestiti. Usarlo anche per importare un VHD di dati in uno snapshot e quindi usare lo snapshot per creare rapidamente più dischi gestiti. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Creare uno snapshot da file VHD")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un disco gestito da un file VHD in una sottoscrizione diversa. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Crea la configurazione usata per la creazione del disco. Include tipo di archiviazione, posizione, ID risorsa dell'account di archiviazione in cui è archiviato il VHD di origine e URI VHD del file VHD di origine. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Crea un disco accettando come parametri la configurazione del disco, il nome del disco e il nome del gruppo di risorse. |

## <a name="next-steps"></a>Passaggi successivi

[Creare un disco gestito da uno snapshot](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Creare una macchina virtuale collegando un disco gestito come disco del sistema operativo](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell della macchina virtuale sono reperibili nella [documentazione della VM Windows di Azure](../../app-service-web/app-service-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
