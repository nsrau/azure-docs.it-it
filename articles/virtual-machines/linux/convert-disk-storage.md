---
title: Convertire l'archiviazione di Azure Managed disks da standard a Premium o Premium a standard
description: Come convertire l'archiviazione di Azure Managed disks da standard a Premium o Premium a standard usando l'interfaccia della riga di comando di Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: abd3c7112a70850d137f77d28e83c60916a96ea3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036629"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Convertire l'archiviazione di Azure Managed disks da standard a Premium o Premium a standard

Sono disponibili quattro tipi di dischi di Azure Managed disks: Azure ultra SSD (anteprima), unità SSD Premium, unità SSD standard e HDD standard. È possibile passare tra i tre tipi di disco GA (unità SSD Premium, unità SSD standard e HDD standard) in base alle esigenze di prestazioni. Non si è ancora in grado di passare da o a un SSD Ultra, è necessario distribuirne uno nuovo.

Questa funzionalità non è supportata per i dischi non gestiti. Tuttavia, è possibile [convertire facilmente un disco non gestito in un disco gestito](convert-unmanaged-to-managed-disks.md) per poter passare da un tipo di disco all'altra.

Questo articolo illustra come convertire i dischi gestiti da standard a Premium o Premium a standard usando l'interfaccia della riga di comando di Azure. Per installare o aggiornare lo strumento, vedere [installare l'interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

* La conversione del disco richiede il riavvio della macchina virtuale (VM), quindi pianificare la migrazione dell'archiviazione su disco durante una finestra di manutenzione preesistente.
* Per i dischi non gestiti, eseguire prima la [conversione in dischi gestiti, in](convert-unmanaged-to-managed-disks.md) modo da poter passare da un'opzione di archiviazione all'altra.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Passa a tutti i dischi gestiti di una macchina virtuale tra Premium e standard

Questo esempio illustra come convertire tutti i dischi di una macchina virtuale dall'archiviazione standard all'archiviazione Premium o da Premium ad archiviazione standard. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. Questo esempio passa anche a una dimensione che supporta archiviazione Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Passa singoli dischi gestiti tra standard e Premium

Per il carico di lavoro di sviluppo/test, potrebbe essere necessario disporre di una combinazione di dischi standard e Premium per ridurre i costi. È possibile scegliere di aggiornare solo i dischi che necessitano di prestazioni migliori. Questo esempio illustra come convertire un singolo disco della macchina virtuale dall'archiviazione standard all'archiviazione Premium o da Premium ad archiviazione standard. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. Questo esempio passa anche a una dimensione che supporta archiviazione Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Passa a dischi gestiti tra HDD Standard e SDD Standard

Questo esempio illustra come convertire un singolo disco della macchina virtuale da HDD Standard a SDD Standard o da SDD Standard a HDD Standard.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Passa a dischi gestiti tra standard e Premium in portale di Azure

Seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare la macchina virtuale dall'elenco di **macchine virtuali**.
3. Se la macchina virtuale non viene arrestata, selezionare **Arresta** nella parte superiore del riquadro di **Panoramica** della macchina virtuale e attendere l'arresto della macchina virtuale.
4. Nel riquadro della macchina virtuale selezionare **dischi** dal menu.
5. Selezionare il disco che si desidera convertire.
6. Selezionare **configurazione** dal menu.
7. Modificare il **tipo di account** da **HDD standard** a **SSD Premium** o da **SSD Premium** a **HDD standard**.
8. Selezionare **Save (Salva**) e chiudere il riquadro del disco.

L'aggiornamento del tipo di disco è istantaneo. È possibile riavviare la macchina virtuale dopo la conversione.

## <a name="next-steps"></a>Passaggi successivi

Eseguire una copia di sola lettura di una macchina virtuale usando gli [snapshot](snapshot-copy-managed-disk.md).
