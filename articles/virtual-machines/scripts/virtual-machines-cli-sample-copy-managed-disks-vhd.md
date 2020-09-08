---
title: Copiare un disco gestito in un account di archiviazione - Interfaccia della riga di comando
description: 'Esempio di interfaccia della riga di comando di Azure: esportare o copiare dischi gestiti in un account di archiviazione.'
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/09/2019
ms.author: ramankum
ms.custom: mvc,seodec18, devx-track-azurecli
ms.openlocfilehash: c43a18f1dcb4122eb6c1407ca11b7c60653594c4
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322749"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Esportare/copiare un disco gestito su un account di archiviazione usando l'interfaccia della riga di comando di Azure

Questo script consente di esportare il disco rigido virtuale sottostante di un disco gestito in un account di archiviazione della stessa area o di un'area diversa. Genera innanzitutto l'URI SAS del disco gestito e quindi lo usa per copiare il disco rigido virtuale in un account di archiviazione. Usare questo script per copiare i dischi gestiti in un'altra area per l'espansione a livello di area. Per pubblicare in Azure Marketplace il file di disco rigido virtuale di un disco gestito, è possibile usare questo script per copiare il file in un account di archiviazione e quindi generare un URI SAS del disco rigido virtuale copiato per la pubblicazione nel Marketplace.   


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per generare l'URI SAS per un disco gestito e copia il disco rigido virtuale sottostante in un account di archiviazione usando tale URI. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az disk grant-access](/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Genera SAS di sola lettura usati per copiare il file del disco rigido virtuale sottostante in un account di archiviazione o scaricarlo in locale  |
| [az storage blob copy start](/cli/azure/storage/blob/copy) | Copia un BLOB in modo asincrono da un account di archiviazione a un altro |

## <a name="next-steps"></a>Passaggi successivi

[Creare un disco gestito da un disco rigido virtuale](virtual-machines-cli-sample-create-managed-disk-from-vhd.md)

[Creare una macchina virtuale da un disco gestito](virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../linux/cli-samples.md).
