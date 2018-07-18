---
title: Creare uno snapshot di un disco rigido virtuale in Azure | Microsoft Docs
description: Informazioni su come creare una copia di un disco rigido virtuale in Azure come backup o per la risoluzione dei problemi.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/20/2018
ms.author: cynthn
ms.openlocfilehash: e5882b2ddc708544a7715da13c1f0d18384ce4e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
ms.locfileid: "30318902"
---
# <a name="create-a-snapshot"></a>Creare uno snapshot 

Fare uno snapshot di un sistema operativo o di un disco dati per il backup o per risolvere i problemi della macchina virtuale. Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. 

## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure 

L'esempio seguente richiede che sia installata l'interfaccia della riga di comando Azure 2.0 e che venga eseguito l'accesso all'account Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

La procedura seguente illustra come fare uno snapshot usando il comando `az snapshot create` con il parametro `--source-disk`. Nell'esempio seguente si presuppone che esista una macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`.

Ottenere l'ID del disco.
```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Creare uno snapshot denominato *osDisk-backup*.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Se si vuole archiviare lo snapshot in una risorsa di archiviazione resiliente nella zona, è necessario crearlo in un'area che supporta le [zone di disponibilità](../../availability-zones/az-overview.md) e includere il parametro `--sku Standard_ZRS`.

## <a name="use-azure-portal"></a>Usare il portale di Azure 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. In alto a sinistra fare clic su **Crea una risorsa** e cercare **Snapshot**.
3. Nel pannello Snapshot, fare clic su **Crea**.
4. Immettere un **Nome** per lo snapshot.
5. Selezionare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo. 
6. Selezionare una località per il data center di Azure.  
7. Per **Disco di origine**, selezionare il disco gestito di cui creare lo snapshot.
8. Selezionare il **tipo di account** da usare per archiviare lo snapshot. È consigliabile usare il tipo **Standard_LRS** a meno che non sia necessario archiviare lo snapshot su un disco a prestazioni elevate.
9. Fare clic su **Crea**.


## <a name="next-steps"></a>Passaggi successivi

 Creare una macchina virtuale da uno snapshot creando un disco gestito dallo snapshot e quindi collegando il nuovo disco gestito come disco del sistema operativo. Per altre informazioni, vedere lo script [Creare una macchina virtuale da uno snapshot](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).

