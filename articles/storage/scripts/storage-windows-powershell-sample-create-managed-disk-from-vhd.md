---
title: 'Esempio di script di Azure PowerShell: creare un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione o in un&quot;altra | Microsoft Docs'
description: 'Esempio di script di Azure PowerShell: creare un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione o in un&quot;altra'
services: managed-disks-windows
documentationcenter: storage
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: managed-disks-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 079ab69da3c2cb6cc38f0a766ffeb47ed84c90a3
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017

---

# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Creare un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione o in un'altra con PowerShell

Questo script crea un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione o in un'altra. Usare questo script per importare un disco rigido virtuale specializzato (non generico/preparato con Sysprep) nel disco del sistema operativo gestito per creare una macchina virtuale. In alternativa, usarlo per importare un disco rigido virtuale di dati in un disco dati gestito. 

Non creare più dischi gestiti identici da un file VHD in poco tempo. Per creare dischi gestiti da un file VHD, viene creato lo snapshot del BLOB del file VHD e successivamente viene usato per creare dischi gestiti. È possibile creare un solo snapshot del BLOB in un minuto perché causa errori di creazione del disco dovuti alla limitazione. Per evitare questa limitazione, creare uno [snapshot gestito dal file VHD](./../scripts/storage-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) e quindi usarlo per creare più dischi gestiti in poco tempo. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[principale](../../../powershell_scripts/storage/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Creare un disco gestito dal disco rigido virtuale")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un disco gestito da un file VHD in una sottoscrizione diversa. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Crea la configurazione del disco usata per la creazione del disco. Include il tipo di archiviazione, la posizione, l'ID risorsa dell'account di archiviazione in cui è archiviato il VHD di origine e l'URI VHD del VHD padre. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Crea un disco accettando come parametri la configurazione del disco, il nome del disco e il nome del gruppo di risorse. |

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale collegando un disco gestito come disco del sistema operativo](./../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell della macchina virtuale sono reperibili nella [documentazione della VM Windows di Azure](../../virtual-machines/windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
