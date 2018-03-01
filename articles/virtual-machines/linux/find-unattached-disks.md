---
title: Trovare ed eliminare dischi gestiti e non gestiti di Azure scollegati | Microsoft Docs
description: Come trovare ed eliminare dischi gestiti e non gestiti di Azure (dischi rigidi virtuali/BLOB di pagine) scollegati tramite l'interfaccia della riga di comando di Azure.
services: virtual-machines-linux
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 281e51783af05e02346b537f0abccdb2def38b31
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Trovare ed eliminare dischi gestiti e non gestiti di Azure scollegati
Quando si elimina una macchina virtuale (VM) in Azure, per impostazione predefinita, nessun disco collegato alla macchina virtuale viene eliminato. Questa funzionalità consente di prevenire la perdita di dati a causa dell'eliminazione accidentale di macchine virtuali. Dopo l'eliminazione di una macchina virtuale, si continuerà a pagare per i dischi scollegati. Questo articolo illustra come trovare ed eliminare tutti i dischi scollegati e ridurre i costi non necessari. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dischi gestiti: trovare ed eliminare dischi scollegati 

Lo script seguente cerca i [dischi gestiti](managed-disks-overview.md) non collegati esaminando il valore della proprietà **ManagedBy**. Quando un disco gestito è collegato a una macchina virtuale, la proprietà **ManagedBy** contiene l'ID risorsa della macchina virtuale. Quando un disco gestito è scollegato, la proprietà **ManagedBy** è null. Lo script esamina tutti i dischi gestiti in una sottoscrizione di Azure. Quando lo script individua un disco gestito con la proprietà **ManagedBy** impostata su null, lo script determina che il disco è scollegato.

>[!IMPORTANT]
>In primo luogo, eseguire lo script impostando la variabile **deleteUnattachedDisks** su 0. Questa azione consente di individuare e visualizzare tutti i dischi gestiti scollegati.
>
>Dopo aver verificato tutti i dischi scollegati, eseguire nuovamente lo script e impostare la variabile **deleteUnattachedDisks** su 1. Questa azione consente di eliminare tutti i dischi gestiti scollegati.
>

```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Dischi non gestiti: trovare ed eliminare dischi scollegati 

I dischi non gestiti sono file disco rigido virtuale che sono archiviati come [BLOB di pagine](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) negli [account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md). Lo script seguente cerca i dischi non gestiti scollegati (BLOB di pagine) esaminando il valore della proprietà **LeaseStatus**. Quando un disco non gestito è collegato a una macchina virtuale, la proprietà **LeaseStatus** è impostata su **Bloccato**. Quando un disco non gestito è scollegato, la proprietà **LeaseStatus** è impostata su **Sbloccato**. Lo script esamina tutti i dischi non gestiti in tutti gli account di archiviazione di Azure in una sottoscrizione di Azure. Quando lo script individua un disco non gestito con la proprietà **LeaseStatus** impostata su **Sbloccato**, lo script determina che il disco è scollegato.

>[!IMPORTANT]
>In primo luogo, eseguire lo script impostando la variabile **deleteUnattachedVHDs** su 0. Questa azione consente di individuare e visualizzare tutti i dischi rigidi virtuali non gestiti scollegati.
>
>Dopo aver verificato tutti i dischi scollegati, eseguire nuovamente lo script e impostare la variabile **deleteUnattachedVHDs** su 1. Questa azione consente di eliminare tutti i dischi rigidi virtuali non gestiti scollegati.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Passaggi successivi

[Eliminare un account di archiviazione](../../storage/common/storage-create-storage-account.md)



