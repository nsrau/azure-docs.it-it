---
title: 'Esempio di interfaccia della riga di comando di Azure: copiare un disco gestito su un account di archiviazione'
description: 'Esempio di interfaccia della riga di comando di Azure: esportare o copiare un disco gestito su un account di archiviazione.'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 66dcb8d38f8c469656d36ff1da1ac03f900069b2
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583137"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Esportare/copiare un disco gestito su un account di archiviazione usando l'interfaccia della riga di comando di Azure

Questo script consente di esportare il disco rigido virtuale sottostante di un disco gestito in un account di archiviazione della stessa area o di un'area diversa. Genera innanzitutto l'URI SAS del disco gestito e quindi lo usa per copiare il disco rigido virtuale in un account di archiviazione. Usare questo script per copiare i dischi gestiti per l'espansione a livello di area. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per generare l'URI SAS per un disco gestito e copia il disco rigido virtuale sottostante in un account di archiviazione usando tale URI. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az disk grant-access](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Genera SAS di sola lettura usati per copiare il file del disco rigido virtuale sottostante in un account di archiviazione o scaricarlo in locale  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Copia un BLOB in modo asincrono da un account di archiviazione a un altro |

## <a name="next-steps"></a>Passaggi successivi

[Creare un disco gestito da un disco rigido virtuale](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Creare una macchina virtuale da un disco gestito](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
