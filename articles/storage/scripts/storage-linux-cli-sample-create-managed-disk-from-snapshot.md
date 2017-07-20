---
title: 'Esempio di script dell&quot;interfaccia della riga di comando di Azure: creare un disco gestito da uno snapshot | Microsoft Docs'
description: 'Esempio di script dell&quot;interfaccia della riga di comando di Azure: creare un disco gestito da uno snapshot'
services: managed-disks-linux
documentationcenter: storage
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: managed-disks-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 5d0c06f0359916ddb2230c67013679bbe8e8aff7
ms.contentlocale: it-it
ms.lasthandoff: 05/23/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Creare un disco gestito da uno snapshot con l'interfaccia della riga di comando

Questo script crea un disco gestito da uno snapshot. Può essere usato per ripristinare una macchina virtuale da snapshot dei dischi del sistema operativo e di dati. Creare dischi del sistema operativo e dischi gestiti di dati dai rispettivi snapshot e quindi creare una nuova macchina virtuale collegando i dischi gestiti. Collegando i dischi dati creati da snapshot, è anche possibile ripristinare i dischi di dati di una macchina virtuale esistente.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[principale](../../../cli_scripts/storage/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Creare un disco gestito da snapshot")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un disco gestito da snapshot. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | Ottiene tutte le proprietà di uno snapshot tramite le proprietà del nome e del gruppo di risorse dello snapshot. La proprietà ID viene usata per creare un disco gestito.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Crea un disco gestito mediante l'ID dello snapshot di uno snapshot gestito |

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale collegando un disco gestito come disco del sistema operativo](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

