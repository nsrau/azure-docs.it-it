---
title: 'Esempio di script dell''interfaccia della riga di comando di Azure: copiare, o spostare, lo snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa con l''interfaccia della riga di comando | Microsoft Docs'
description: 'Esempio di script dell''interfaccia della riga di comando di Azure: copiare, o spostare, lo snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa con l''interfaccia della riga di comando'
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
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 3fd0c02f7867ab121fe89f9369e59bade6e4b828
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---

# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Copiare lo snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa con l'interfaccia della riga di comando

Questo script consente di copiare uno snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa. Usare questo script per spostare uno snapshot in una sottoscrizione diversa nella stessa area come snapshot padre.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[principale](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copia snapshot")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare uno snapshot nella sottoscrizione di destinazione usando l'ID dello snapshot di origine. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Ottiene tutte le proprietà di uno snapshot tramite le proprietà del nome e del gruppo di risorse dello snapshot. La proprietà ID viene usata per copiare lo snapshot in una sottoscrizione diversa.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_create) | Consente di copiare uno snapshot creando uno snapshot in una sottoscrizione diversa con l'ID e il nome dello snapshot padre.  |

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale da uno snapshot](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../../app-service-web/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

