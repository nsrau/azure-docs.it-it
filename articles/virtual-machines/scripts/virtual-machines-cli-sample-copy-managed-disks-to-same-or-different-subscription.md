---
title: Copiare i dischi gestiti nella sottoscrizione - Esempio dell'interfaccia della riga di comando
description: Esempio di script dell'interfaccia della riga di comando di Azure - Copiare (o spostare) i dischi gestiti nella stessa sottoscrizione o in un'altra
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ed593b37de556d5b62cfec6b726322d12f4d8104
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051822"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Copiare i dischi gestiti nella stessa sottoscrizione o in una sottoscrizione diversa con l'interfaccia della riga di comando

Questo script consente di copiare un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa ma nella stessa area. La copia funziona solo quando le sottoscrizioni fanno parte dello stesso tenant di Azure AD.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un nuovo disco gestito nella sottoscrizione di destinazione usando l'ID del disco gestito di origine. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az disk show](/cli/azure/disk) | Ottiene tutte le proprietà di uno snapshot tramite le proprietà del nome e del gruppo di risorse del disco gestito. La proprietà ID viene usata per copiare il disco gestito in una sottoscrizione diversa.  |
| [az disk create](/cli/azure/disk) | Consente di copiare un disco gestito creando un nuovo disco gestito in una sottoscrizione diversa tramite l'ID e il nome del disco gestito padre.  |

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale da un disco gestito](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
