---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una VM collegando un disco gestito come disco del sistema operativo | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una VM collegando un disco gestito come disco del sistema operativo
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 674b8bc1db7bccdb99e89510d18a4ae10bf1329b
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017

---

# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Creare una macchina virtuale usando un disco del sistema operativo gestito esistente con l'interfaccia della riga di comando

Questo script crea una macchina virtuale collegando un disco gestito esistente come disco del sistema operativo. Usare questo script negli scenari precedenti:
* Creare una VM da un disco del sistema operativo gestito esistente che è stato copiato da un disco gestito in una sottoscrizione diversa
* Creare una VM da un disco gestito esistente che è stato creato da un file VHD specializzato 
* Creare una VM da un disco del sistema operativo gestito esistente che è stato creato da uno snapshot 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Creare una VM da un disco gestito")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per ottenere le proprietà di un disco gestito, collegare un disco gestito a una nuova VM e creare una VM. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#show) | Ottiene le proprietà del disco gestito usando il nome del disco e il nome del gruppo di risorse. La proprietà Id viene usata per collegare un disco gestito a una nuova VM |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crea una VM utilizzando un disco del sistema operativo gestito |
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

