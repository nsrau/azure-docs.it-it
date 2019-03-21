---
title: Esempio di script di Azure PowerShell - Esportare/Copiare il disco rigido virtuale di un disco gestito in un account di archiviazione di un'area diversa | Microsoft Docs
description: Esempio di script di Azure PowerShell - Esportare/Copiare il disco rigido virtuale di un disco gestito in un account di archiviazione della stessa area o di un'area diversa
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: dc1549007feed11919e25ac9dd60e88ef7f401a5
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249621"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell"></a>Esportare/Copiare il disco rigido virtuale di un disco gestito in un account di archiviazione di un'area diversa con PowerShell

Questo script consente di esportare il disco rigido virtuale di un disco gestito in un account di archiviazione di un'area diversa. Genera innanzitutto l'URI SAS del disco gestito e quindi lo usa per copiare il disco rigido virtuale sottostante in un account di archiviazione di un'area diversa. Usare questo script per copiare i dischi gestiti in un'altra area per l'espansione a livello di area.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per generare l'URI SAS di un disco gestito e copia il disco rigido virtuale sottostante in un account di archiviazione usando tale URI. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Grant-AzDiskAccess](https://docs.microsoft.com/powershell/module/az.compute/grant-azdiskaccess) | Genera l'URI SAS di un disco gestito che viene usato per copiare il disco rigido virtuale sottostante in un account di archiviazione. |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | Crea un contesto per l'account di archiviazione usando nome e chiave dell'account. Questo contesto può essere usato per eseguire operazioni di lettura/scrittura sull'account di archiviazione. |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Copia il file VHD sottostante di uno snapshot in un account di archiviazione |

## <a name="next-steps"></a>Passaggi successivi

[Creare un disco gestito da un disco rigido virtuale](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Creare una macchina virtuale da un disco gestito](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell per la macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).