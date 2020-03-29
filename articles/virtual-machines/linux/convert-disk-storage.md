---
title: Convertire lo spazio di archiviazione dei dischi gestiti tra SSD standard e premiumConvert managed disks storage between standard and premium SSD
description: Come convertire l'archiviazione dei dischi gestiti di Azure da standard a premium o da premium a standard usando l'interfaccia della riga di comando di Azure.How to convert Azure managed disks storage from standard to premium or premium to standard by using the Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431497"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Convertire l'archiviazione dei dischi gestiti di Azure da Standard a Premium o Premium a Standard

Esistono quattro tipi di dischi di dischi gestiti da Azure: Azure ultra SSD (anteprima), SSD premium, SSD standard e HDD standard. Puoi passare da un tipo di disco GA all'altro (SSD premium, SSD standard e HDD standard) in base alle tue esigenze di prestazioni. Non è ancora possibile passare da o verso un ultra SSD, è necessario distribuirne uno nuovo.

Questa funzionalità non è supportata per i dischi non gestiti. Tuttavia, è possibile convertire facilmente [un disco non gestito in un disco gestito](convert-unmanaged-to-managed-disks.md) per poter passare da un tipo di disco all'altro.

Questo articolo illustra come convertire i dischi gestiti da Standard a Premium o Premium a Standard usando l'interfaccia della riga di comando di Azure.This article shows how to convert managed disks from Standard to Premium or Premium to Standard by using the Azure CLI. Per installare o aggiornare lo strumento, vedere [Installare l'interfaccia della riga di comando](/cli/azure/install-azure-cli)di Azure.To install or upgrade the tool, see Install Azure CLI .

## <a name="before-you-begin"></a>Prima di iniziare

* La conversione del disco richiede il riavvio della macchina virtuale (VM), pertanto pianificare la migrazione dello spazio di archiviazione su disco durante una finestra di manutenzione preesistente.
* Per i dischi non gestiti, eseguire prima [la conversione in dischi gestiti](convert-unmanaged-to-managed-disks.md) in modo da poter passare da un'opzione di archiviazione all'altra.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Passare tutti i dischi gestiti di una macchina virtuale tra Premium e StandardSwitch all managed disks of a VM between Premium and Standard

Questo esempio mostra come convertire tutti i dischi di una macchina virtuale dall'archiviazione Standard a Premium o da Premium all'archiviazione Standard.This example shows how to convert all of a VM's disks from Standard to Premium storage or from Premium to Standard storage. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. Questo esempio passa anche a una dimensione che supporta l'archiviazione Premium.This example also switches to a size that supports Premium storage.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Passare da un dischi gestiti all'altro tra Standard e Premium

Per il carico di lavoro di sviluppo/test, è consigliabile disporre di una combinazione di dischi Standard e Premium per ridurre i costi. È possibile scegliere di aggiornare solo i dischi che richiedono prestazioni migliori. Questo esempio mostra come convertire un singolo disco VM dall'archiviazione Standard a Premium o da Premium all'archiviazione Standard.This example shows how to convert a single VM disk from Standard to Premium storage or from Premium to Standard storage. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. Questo esempio passa anche a una dimensione che supporta l'archiviazione Premium.This example also switches to a size that supports Premium storage.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Commutazione di dischi gestiti tra HDD standard e SSD standard

Questo esempio mostra come convertire un singolo disco VM da HDD standard a SSD standard o da SSD standard a HDD standard.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Passare da un disco gestito all'altro tra Standard e Premium nel portale di AzureSwitch managed disks between Standard and Premium in Azure portal

A tale scopo, seguire questa procedura:

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare la macchina virtuale dall'elenco delle **macchine virtuali**.
3. Se la macchina virtuale non è arrestata, selezionare **Interrompi** nella parte superiore del riquadro **Panoramica** macchina virtuale e attendere l'arresto della macchina virtuale.
4. Nel riquadro della macchina virtuale selezionare **Dischi** dal menu.
5. Selezionare il disco che si desidera convertire.
6. Selezionare **Configurazione** dal menu.
7. Modificare il **tipo di account** da **HdD standard** a Premium **SSD** o da **Premium SSD** a **HDD standard**.
8. Selezionare **Salva**e chiudere il riquadro del disco.

L'aggiornamento del tipo di disco è istantaneo. È possibile riavviare la macchina virtuale dopo la conversione.

## <a name="next-steps"></a>Passaggi successivi

Creare una copia di sola lettura di una macchina virtuale usando [gli snapshot](snapshot-copy-managed-disk.md).
