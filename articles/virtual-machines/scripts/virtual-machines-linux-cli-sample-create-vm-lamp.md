---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Distribuire lo stack LAMP in un set di scalabilità di macchine virtuali con carico bilanciato
description: Usare un'estensione di script personalizzato per distribuire lo stack LAMP in un set di scalabilità di macchine virtuali con carico bilanciato in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.author: cynthn
tags: azure-service-management
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.custom: mvc, devx-track-azurecli
ms.date: 04/05/2017
ms.openlocfilehash: e71b2f940c637a8b1375be71f9b6ca95fd76628e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501866"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Distribuire lo stack LAMP in un set di scalabilità di macchine virtuali con carico bilanciato

Questo esempio crea un set di scalabilità di macchine virtuali e applica un'estensione che esegue uno script personalizzato per distribuire lo stack LAMP in ogni macchina virtuale del set di scalabilità.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Connessione

Usare questo codice per vedere come connettersi alle macchine virtuali e al set di scalabilità.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire il comando seguente per rimuovere il gruppo di risorse, il set di scalabilità, le macchine virtuali e tutte le risorse correlate.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vmss create](/cli/azure/vmss) | Consente di creare un set di scalabilità di macchine virtuali |
| [az network lb rule create](/cli/azure/network/lb/rule) | Consente di aggiungere un endpoint con carico bilanciato |
| [az vmss extension set](/cli/azure/vmss/extension) | Consente di creare l'estensione che esegue lo script personalizzato sulla distribuzione di una macchina virtuale |
| [az vmss update-instances](/cli/azure/vmss) | Consente di eseguire lo script personalizzato nelle istanze di macchine virtuali che sono state distribuite prima che l'estensione sia stata applicata al set di scalabilità. |
| [az vmss scale](/cli/azure/vmss) | Consente di aumentare il set di scalabilità mediante l'aggiunta di più istanze di macchine virtuali. Lo script personalizzato viene eseguito su queste quando vengono distribuite. |
| [az network public-ip list](/cli/azure/network/public-ip) | Consente di ottenere gli indirizzi IP delle macchine virtuali create dall'esempio. |
| [az network lb show](/cli/azure/network/lb) | Consente di ottenere le porte di front-end e back-end usate dal bilanciamento del carico. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
