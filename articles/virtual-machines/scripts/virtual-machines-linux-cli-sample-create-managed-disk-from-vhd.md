---
title: 'Esempio di script dell''interfaccia della riga di comando di Azure: creare un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione | Microsoft Docs'
description: 'Esempio di script dell''interfaccia della riga di comando di Azure: creare un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione'
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
ms.openlocfilehash: 36d34261325ba3ab120fd6bcf0f3816f84e15182
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---

# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Creare un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione con l'interfaccia della riga di comando

Questo script crea un disco gestito da un file VHD in un account di archiviazione nella stessa sottoscrizione. Usare questo script per importare un disco rigido virtuale specifico, non generico o preparato con Sysprep, nel disco del sistema operativo gestito per creare una macchina virtuale. In alternativa, è possibile usarlo per importare un disco rigido virtuale di dati in un disco di dati gestiti. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[principale](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Creare un disco gestito dal disco rigido virtuale")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un disco gestito da un disco rigido virtuale. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Crea un disco gestito usando l'URI del disco rigido virtuale in un account di archiviazione nella stessa sottoscrizione |

## <a name="next-steps"></a>Passaggi successivi

[Creare una macchina virtuale collegando un disco gestito come disco del sistema operativo](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di dischi gestiti e della macchina virtuale aggiuntiva sono reperibili nella [documentazione della macchina virtuale Linux di Azure](../../app-service-web/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

