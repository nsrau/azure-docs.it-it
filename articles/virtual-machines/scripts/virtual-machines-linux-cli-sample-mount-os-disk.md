---
title: Esempio di script dell'interfaccia della riga di comando di Azure - montaggio del disco del sistema operativo
description: Esempio di script dell'interfaccia della riga di comando di Azure - montaggio del disco del sistema operativo
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 292d67dafa768c82041a2cae8e6d888ee5d9050b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037601"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Risolvere i problemi del disco del sistema operativo della VM

Questo script consente di montare il disco del sistema operativo di una macchina virtuale in cui si è verificato un errore o un problema come disco dati in una seconda macchina virtuale. Può essere utile quando si esegue la risoluzione dei problemi del disco o il ripristino di dati.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm) | Recupera un elenco di macchine virtuali. In questo caso, l'opzione di query viene usata per restituire il disco del sistema operativo della macchina virtuale. Questo valore viene quindi aggiunto a un nome di variabile 'uri'. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm) | Consente di eliminare una macchina virtuale. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Consente di creare una macchina virtuale.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | Collega un disco a una macchina virtuale. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm) | Restituisce gli indirizzi IP di una macchina virtuale. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
