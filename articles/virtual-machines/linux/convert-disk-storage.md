---
title: Convertire l'archiviazione di Azure Managed Disks da Standard a Premium e viceversa | Microsoft Docs
description: Come convertire l'archiviazione di Azure Managed Disks da Standard a Premium e viceversa usando l'interfaccia della riga di comando di Azure.
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 0380b4aaa23b4aaba4c67d05e2d62f3ef41d6a32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Convertire l'archiviazione di Azure Managed Disks da Standard a Premium e viceversa

Managed Disks offre due opzioni di archiviazione: [Premium](../../storage/storage-premium-storage.md) (basata su SSD) e [Standard](../../storage/storage-standard-storage.md) (basata su HDD). È possibile passare facilmente tra le due opzioni con un tempo di inattività minimo in base alle esigenze in termini di prestazioni. Questa funzionalità non è disponibile per i dischi non gestiti. È possibile eseguire facilmente la [conversione a Managed Disks](convert-unmanaged-to-managed-disks.md) per passare facilmente tra le due opzioni.

Questo articolo illustra come convertire la versione di Managed Disks da Standard a Premium e viceversa usando l'interfaccia della riga di comando di Azure. Se è necessario installarla o aggiornarla, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Prima di iniziare

* La conversione richiede un riavvio della VM, quindi pianificare la migrazione dell'archiviazione su dischi durante una finestra di manutenzione preesistente. 
* Se si usano dischi non gestiti, prima di tutto eseguire la [conversione a Managed Disks](convert-unmanaged-to-managed-disks.md) per poi passare tra le due opzioni di archiviazione come indicato in questo articolo. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Convertire tutte le istanze di Managed Disks di una VM da Standard a Premium e viceversa

L'esempio seguente illustra come passare dall'archiviazione Standard all'archiviazione Premium per tutti i dischi di una VM. Per usare i dischi gestiti Premium, la VM deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio si passa anche a una dimensione che supporta l'archiviazione Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Convertire un disco gestito da Standard a Premium e viceversa

Per il carico di lavoro di sviluppo/test, potrebbe essere necessaria una combinazione di dischi Standard e Premium, per ridurre i costi. A tale scopo, è possibile eseguire l'aggiornamento ad Archiviazione Premium solo per i dischi che richiedono prestazioni migliori. L'esempio seguente illustra come passare dall'archiviazione Standard all'archiviazione Premium e viceversa per un singolo disco di una VM. Per usare i dischi gestiti Premium, la VM deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio si passa anche a una dimensione che supporta l'archiviazione Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>Passaggi successivi

Eseguire una copia di sola lettura di una VM usando [snapshot](snapshot-copy-managed-disk.md).

