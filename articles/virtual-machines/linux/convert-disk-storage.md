---
title: Convertire Azure managed archiviazione su disco da Standard a Premium a Standard o Premium | Microsoft Docs
description: Come convertire Azure managed archiviazione su disco da Standard a Premium a Standard o Premium tramite la CLI di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: bc42bcbf7149f88eb895317a411c7acd5913d63d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417687"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Convertire Azure managed archiviazione su disco da Standard a Premium a Standard o Premium

Esistono quattro tipi di dischi di Azure i dischi gestiti: SSDs Ultra-Azure (anteprima), premium SSD standard unità SSD e unità disco rigido standard. È possibile spostarsi tra i tre tipi di dischi a livello generale (premium SSD standard unità SSD e HDD standard) in base alle esigenze di prestazioni. Non si è ancora in grado di passare da o in un'unità SSD extra, è necessario distribuire uno nuovo.

Questa funzionalità non è supportata per dischi non gestiti. È possibile eseguire facilmente [convertire un disco non gestito in un disco gestito](convert-unmanaged-to-managed-disks.md) sia in grado di passare tra i tipi di disco.

Questo articolo viene illustrato come convertire i dischi gestiti da Standard a Premium o Premium a Standard utilizzando il comando di Azure. Per installare o aggiornare lo strumento, vedere [installare CLI Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Prima di iniziare

* La conversione dei dischi richiede un riavvio della macchina virtuale (VM), quindi pianificare la migrazione delle risorse di archiviazione del disco durante una finestra di manutenzione preesistente.
* Per i dischi non gestiti, primo [conversione a managed disks](convert-unmanaged-to-managed-disks.md) in modo che è possibile spostarsi tra le opzioni di archiviazione.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Passa tutti i dischi gestiti di una macchina virtuale tra Standard e Premium

In questo esempio viene illustrato come convertire tutti i dischi della VM da Standard ad archiviazione Premium o Premium per archiviazione Standard. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio passa anche a una dimensione che supporta archiviazione Premium.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Passa i singoli dischi gestiti tra Standard e Premium

Per il carico di lavoro di sviluppo/test, è consigliabile avere una combinazione di dischi Standard e Premium per ridurre i costi. È possibile scegliere di aggiornare solo i dischi che richiedono prestazioni migliori. In questo esempio viene illustrato come convertire un singolo disco della macchina virtuale standard all'archiviazione Premium o Premium in archiviazione Standard di. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio passa anche a una dimensione che supporta archiviazione Premium.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Cambiare i dischi gestiti tra Standard HDD e SSD Standard

In questo esempio viene illustrato come convertire un singolo disco della macchina virtuale da Standard HDD a unità SSD Standard o da Standard SSD alle unità disco rigido Standard.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Cambiare i dischi gestiti tra Standard e Premium nel portale di Azure

A tale scopo, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare la macchina virtuale dall'elenco dei **macchine virtuali**.
3. Se non viene arrestata la macchina virtuale, selezionare **arrestare** nella parte superiore della VM **Panoramica** riquadro e attesa arrestare la macchina virtuale.
4. Nel riquadro per la macchina virtuale, selezionare **dischi** dal menu di scelta.
5. Selezionare il disco che si desidera convertire.
6. Selezionare **configurazione** dal menu di scelta.
7. Modifica il **tipo di Account** da **Standard HDD** al **unità SSD Premium** o da **unità SSD Premium** a **Standard HDD**.
8. Selezionare **salvare**e chiudere il riquadro del disco.

L'aggiornamento del tipo di disco è istantanea. È possibile riavviare la macchina virtuale dopo la conversione.

## <a name="next-steps"></a>Passaggi successivi

Creare una copia di sola lettura di una macchina virtuale usando [snapshot](snapshot-copy-managed-disk.md).
