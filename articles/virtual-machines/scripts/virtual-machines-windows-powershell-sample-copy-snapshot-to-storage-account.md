---
title: Esportare o copiare uno snapshot come disco rigido virtuale in un account di archiviazione in un'area diversa - Esempio di PowerShell
description: Esempio di script di Azure PowerShell - Esportare/copiare uno snapshot come disco rigido virtuale in un account di archiviazione nella stessa area o in un'area diversa
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 9b8510d3eb66cc63a0c9c660b34514a83a35d25c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509581"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Esportare/copiare snapshot gestiti come disco rigido virtuale in un account di archiviazione di un'area diversa con PowerShell

Questo script consente di esportare uno snapshot gestito in un account di archiviazione di un'area diversa. Per prima cosa viene generato l'URI di firma di accesso condiviso dello snapshot, che viene poi usato per copiare lo snapshot in un account di archiviazione di un'area diversa. Usare questo script per gestire il backup dei dischi gestiti in un'area diversa per il ripristino di emergenza.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per generare l'URI di firma di accesso condiviso per uno snapshot gestito e copia lo snapshot in un account di archiviazione usando tale URI. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Grant-AzSnapshotAccess](/powershell/module/az.compute/new-azdisk) | Genera l'URI di firma di accesso condiviso per uno snapshot, che viene usato per copiarlo in un account di archiviazione. |
| [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) | Crea un contesto per l'account di archiviazione usando nome e chiave dell'account. Questo contesto può essere usato per eseguire operazioni di lettura/scrittura sull'account di archiviazione. |
| [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) | Copia il file VHD sottostante di uno snapshot in un account di archiviazione |

## <a name="next-steps"></a>Passaggi successivi

[Creare un disco gestito da un disco rigido virtuale](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Creare una macchina virtuale da un disco gestito](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell della macchina virtuale sono reperibili nella [documentazione della VM Windows di Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
