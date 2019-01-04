---
title: Esempio dell’interfaccia della riga di comando di Azure - Copia uno snapshot su un account di archiviazione in un'altra in un'altra area | Microsoft Docs
description: "Esempio di script dell'interfaccia della riga di comando di Azure: snapshot di esportazione/copia come disco rigido virtuale in un account di archiviazione nella stessa sottoscrizione o in un’area diversa."
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
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 631e20f44ab0cd2c35df459695af0de6059e6788
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582287"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Esportare/copiare snapshot in un account di archiviazione di un'area diversa con l'interfaccia della riga di comando

Questo script consente di esportare uno snapshot gestito in un account di archiviazione di un'area diversa. Per prima cosa viene generato l'URI di firma di accesso condiviso dello snapshot, che viene poi usato per copiare lo snapshot in un account di archiviazione di un'area diversa. Usare questo script per gestire il backup dei dischi gestiti in un'area diversa per il ripristino di emergenza. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per generare l'URI di firma di accesso condiviso per uno snapshot gestito e copia lo snapshot in un account di archiviazione usando tale URI. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_grant_access) | Genera SAS di sola lettura usati per copiare il file VHD sottostante in un account di archiviazione o scaricarlo in locale  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Copia un BLOB in modo asincrono da un account di archiviazione a un altro |

## <a name="next-steps"></a>Passaggi successivi

[Creare un disco gestito da un disco rigido virtuale](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Creare una macchina virtuale da un disco gestito](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
