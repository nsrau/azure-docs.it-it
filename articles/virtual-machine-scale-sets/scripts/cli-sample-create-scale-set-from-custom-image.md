---
title: Esempi dell'interfaccia della riga di comando di Azure 2.0 - Usare un'immagine di macchina virtuale personalizzata | Microsoft Docs
description: Esempi dell'interfaccia della riga di comando di Azure 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 056a1ff59ca5bcaf460d98db7d50fd3566f11427
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-the-azure-cli-20"></a>Creare un set di scalabilità di macchine virtuali da un'immagine di macchina virtuale personalizzata con l'interfaccia della riga di comando di Azure 2.0
Questo script crea un set di scalabilità di macchine virtuali che usa un'immagine di macchina virtuale personalizzata come origine delle istanze di macchina virtuale.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.sh "Create a virtual machine scale set with a custom VM image")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Eseguire questo comando per rimuovere il gruppo di risorse, il set di scalabilità e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi seguenti per creare un gruppo di risorse, il set di scalabilità di macchine virtuali e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Crea il set di scalabilità di macchine virtuali e lo connette alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Per distribuire il traffico a più istanze di macchina virtuale, viene anche creato un servizio di bilanciamento del carico. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az group delete](/cli/azure/ad/group#delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'interfaccia della riga di comando di Azure 2.0, vedere la [documentazione dell'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Azure 2.0 per i set di scalabilità di macchine virtuali sono disponibili nella [documentazione dei set di scalabilità di macchine virtuali di Azure](../cli-samples.md).