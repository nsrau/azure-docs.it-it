---
title: 'Esempio di script dell''interfaccia della riga di comando di Azure: creare un disco gestito da uno snapshot | Microsoft Docs'
description: 'Esempio di script dell''interfaccia della riga di comando di Azure: creare un disco gestito da uno snapshot'
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
ms.openlocfilehash: 26bddc69c7ff4854c84832ec924ab46ea342f1f4
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Creare un disco gestito da uno snapshot con l'interfaccia della riga di comando

Questo script crea un disco gestito da uno snapshot. Può essere usato per ripristinare una macchina virtuale da snapshot dei dischi del sistema operativo e di dati. Creare dischi dati e del sistema operativo gestiti dai rispettivi snapshot e quindi creare una nuova macchina virtuale collegando i dischi gestiti. Collegando i dischi dati creati da snapshot, è anche possibile ripristinare i dischi di dati di una macchina virtuale esistente.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[principale](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Creare un disco gestito da snapshot")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un disco gestito da snapshot. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Ottiene tutte le proprietà di uno snapshot tramite le proprietà del nome e del gruppo di risorse dello snapshot. La proprietà ID viene usata per creare un disco gestito.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Crea un disco gestito mediante l'ID dello snapshot di uno snapshot gestito |

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale collegando un disco gestito come disco del sistema operativo](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../../app-service-web/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

