---
title: Uso dei dischi gestiti con i set di scalabilità delle macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come e perché usare dischi gestiti con i set di scalabilità delle macchine virtuali
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/01/2017
ms.author: negat
ms.openlocfilehash: 82fa518e6c0498a13f950ce33c51be8581918f9b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2018
---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Set di scalabilità VM di Azure e dischi gestiti

I [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/) di Azure supportano macchine virtuali con dischi gestiti. L'uso di dischi gestiti con i set di scalabilità presenta diversi vantaggi, tra cui:

* Non è più necessario creare in anticipo e gestire gli account di archiviazione per archiviare i dischi del sistema operativo per i set di scalabilità delle macchine virtuali.

* È possibile collegare dischi dati gestiti al set di scalabilità.

* Grazie al disco gestito, un set di scalabilità può contenere fino a un massimo di 1.000 macchine virtuali se basato su un'immagine di piattaforma o di 300 macchine virtuali se basato su un'immagine personalizzata.

## <a name="get-started"></a>Attività iniziali

Un modo semplice per iniziare a usare i set di scalabilità con i dischi gestiti consiste nel distribuirne uno dal Portale di Azure. Per altre informazioni, vedere [questo articolo](./virtual-machine-scale-sets-portal-create.md). Un altro modo semplice per iniziare è usare l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) per distribuire un set di scalabilità. Nell'esempio seguente viene illustrato come creare un set di scalabilità basato su Ubuntu con 10 macchine virtuali, ognuna con un disco dati da 50 e da 100 GB:

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

In alternativa, è possibile esaminare il [repository di Modelli di avvio rapido di Azure in GitHub](https://github.com/Azure/azure-quickstart-templates) e consultare le cartelle contenenti `vmss` per vedere esempi preesistenti di modelli di distribuzione di set di scalabilità. Per individuare quali modelli usano già i dischi gestiti, è possibile fare riferimento a [questo elenco](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui dischi gestiti in generale, vedere [questo articolo](../virtual-machines/windows/managed-disks-overview.md).

Per informazioni su come convertire un modello di Resource Manager per eseguire il provisioning dei set di scalabilità con i dischi gestiti, consultare [questo articolo](./virtual-machine-scale-sets-convert-template-to-md.md). Le stesse modifiche applicate ai modelli di Resource Manager si applicano anche all'API REST di Azure.

Per altre informazioni sull'uso dei dischi di dati gestiti con i set di scalabilità, vedere [questo articolo](./virtual-machine-scale-sets-attached-disks.md).

Per iniziare a usare i set di scalabilità di grandi dimensioni, fare riferimento a [questo articolo](./virtual-machine-scale-sets-placement-groups.md).


