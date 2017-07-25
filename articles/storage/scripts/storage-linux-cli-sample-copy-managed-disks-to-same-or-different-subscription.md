---
title: 'Esempio di script dell''interfaccia della riga di comando di Azure: copiare, o spostare, i dischi gestiti nella stessa sottoscrizione o in una sottoscrizione diversa | Microsoft Docs'
description: 'Esempio di script dell''interfaccia della riga di comando di Azure: copiare, o spostare, i dischi gestiti nella stessa sottoscrizione o in una sottoscrizione diversa'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 90228d66150cf961557b6dbeed11308932677d83
ms.contentlocale: it-it
ms.lasthandoff: 05/23/2017

---

# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Copiare i dischi gestiti nella stessa sottoscrizione o in una sottoscrizione diversa con l'interfaccia della riga di comando

Questo script consente di copiare un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa ma nella stessa area. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[principale](../../../cli_scripts/storage/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copiare un disco gestito")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un nuovo disco gestito nella sottoscrizione di destinazione usando l'ID del disco gestito di origine. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#show) | Ottiene tutte le proprietà di uno snapshot tramite le proprietà del nome e del gruppo di risorse del disco gestito. La proprietà ID viene usata per copiare il disco gestito in una sottoscrizione diversa.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Consente di copiare un disco gestito creando un nuovo disco gestito in una sottoscrizione diversa tramite l'ID e il nome del disco gestito padre.  |

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale da un disco gestito](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

