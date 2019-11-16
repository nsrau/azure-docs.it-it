---
title: "Esempio di script dell'interfaccia della riga di comando di Azure: copiare, o spostare, lo snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa con l'interfaccia della riga di comando"
description: "Esempio di script dell'interfaccia della riga di comando di Azure: copiare, o spostare, lo snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa con l'interfaccia della riga di comando"
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
ms.custom: mvc
ms.openlocfilehash: 23886256e405f57c5a581356b4212498779ae6e5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038247"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Copiare lo snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa con l'interfaccia della riga di comando

Questo script consente di copiare uno snapshot di un disco gestito nella stessa sottoscrizione o in una sottoscrizione diversa. Usare questo script per gli scenari seguenti:

1. Eseguire la migrazione di uno snapshot da archiviazione Premium (Premium_LRS) ad archiviazione Standard (Standard_LRS o Standard_ZRS) per ridurre i costi.
1. Eseguire la migrazione di uno snapshot da archiviazione con ridondanza locale (Premium_LRS, Standard_LRS) ad archiviazione con ridondanza della zona (Standard_ZRS) per trarre vantaggio dalla maggiore affidabilità di archiviazione ZRS.
1. Spostare uno snapshot in un'altra sottoscrizione nella stessa area per una conservazione più duratura.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare uno snapshot nella sottoscrizione di destinazione usando l'ID dello snapshot di origine. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Ottiene tutte le proprietà di uno snapshot tramite le proprietà del nome e del gruppo di risorse dello snapshot. La proprietà ID viene usata per copiare lo snapshot in una sottoscrizione diversa.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Consente di copiare uno snapshot creando uno snapshot in una sottoscrizione diversa con l'ID e il nome dello snapshot padre.  |

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale da uno snapshot](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
