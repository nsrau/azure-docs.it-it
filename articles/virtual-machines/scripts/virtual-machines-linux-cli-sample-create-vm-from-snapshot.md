---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una VM da uno snapshot | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una VM da uno snapshot
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 33f21786b1af4d169d184487a030b7e4ea321327
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694534"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Creare una macchina virtuale da uno snapshot con l'interfaccia della riga di comando

Questo script crea una macchina virtuale da uno snapshot di un disco del sistema operativo.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un disco gestito, una macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Ottiene lo snapshot usando il nome dello snapshot e il nome del gruppo di risorse. La proprietà Id dell'oggetto restituito viene usata per creare un disco gestito.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Crea dischi gestiti da uno snapshot usando l'ID dello snapshot, il nome del disco, il tipo di archiviazione e la dimensione  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Crea una VM utilizzando un disco del sistema operativo gestito |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
