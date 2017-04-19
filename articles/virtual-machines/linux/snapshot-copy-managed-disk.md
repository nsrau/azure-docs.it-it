---
title: Copiare un disco gestito di Azure per il backup | Documentazione Microsoft
description: Informazioni su come creare una copia di un disco gestito di Azure da usare per il backup o sulla risoluzione dei problemi relativi al disco.
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 2/6/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 22013ec8e5531a2f61d811300bce016fcde5ab86
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Creare una copia di un disco rigido virtuale archiviato come disco gestito di Azure usando snapshot gestiti
Creare uno snapshot di un disco gestito per il backup o creare un disco gestito dallo snapshot e collegarlo a una macchina virtuale di prova per risolvere i problemi. Uno snapshot gestito è una copia temporizzata completa di un disco gestito di macchina virtuale. Uno snapshot crea una copia di sola lettura del disco rigido virtuale che, per impostazione predefinita, viene memorizzato come disco gestito Standard. 

Per informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/managed-disks/). <!--Add link to topic or blog post that explains managed disks. -->

Usare il portale di Azure o l'interfaccia della riga di comando Azure 2.0 per creare uno snapshot del disco gestito.

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Usare l'interfaccia della riga di comando Azure 2.0 per creare uno snapshot

> [!NOTE] 
> L'esempio seguente richiede che sia installata l'interfaccia della riga di comando Azure 2.0 e che venga eseguito l'accesso all'account Azure.

La procedura seguente illustra come ottenere e creare uno snapshot di un disco del sistema operativo gestito usando il comando `az snapshot create` con il parametro `--source-disk`. Nell'esempio seguente si presuppone che esista una macchina virtuale denominata `myVM` creata con un disco del sistema operativo gestito nel gruppo di risorse `myResourceGroup`.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

L'output dovrebbe essere simile a quanto segue:

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Usare il portale di Azure per creare uno snapshot 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. In alto a sinistra fare clic su **Nuovo** e cercare **Snapshot**.
3. Nel pannello Snapshot, fare clic su **Crea**.
4. Immettere un **Nome** per lo snapshot.
5. Selezionare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo. 
6. Selezionare una località per il data center di Azure.  
7. Per **Disco di origine**, selezionare il disco gestito di cui creare lo snapshot.
8. Selezionare il **tipo di account** da usare per archiviare lo snapshot. È consigliabile usare il tipo **Standard_LRS** a meno che non sia necessario archiviare lo snapshot su un disco a prestazioni elevate.
9. Fare clic su **Crea**.

Se si prevede di usare lo snapshot per creare un disco gestito e associarlo a una macchina virtuale a prestazioni elevate, usare il parametro `--sku Premium_LRS` con il comando `az snapshot create`. In questo modo si crea lo snapshot in modo tale che venga archiviato come un disco gestito Premium. Managed Disks Premium offre prestazioni migliori perché consiste in unità SSD, ma con un costo superiore rispetto ai dischi Standard (HDD).



