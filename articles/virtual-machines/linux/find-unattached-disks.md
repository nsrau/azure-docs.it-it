---
title: Trovare ed eliminare dischi gestiti e non gestiti di Azure scollegati | Microsoft Docs
description: Come trovare ed eliminare dischi gestiti e non gestiti di Azure (dischi rigidi virtuali/BLOB di pagine) scollegati tramite l'interfaccia della riga di comando di Azure
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
ms.openlocfilehash: 9ada768cd4128b9dd6949b5a96c557496c6bb11c
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Trovare ed eliminare dischi gestiti e non gestiti di Azure scollegati
Quando si elimina una macchina virtuale in Azure, i dischi collegati a questa non vengono eliminati per impostazione predefinita. Ciò impedisce perdite di dati dovute all'eliminazione di macchine virtuali per errore, ma in questo modo si continua a pagare per i dischi scollegati. Usare questo articolo per trovare ed eliminare tutti i dischi scollegati e risparmiare sui costi. 


## <a name="find-and-delete-unattached-managed-disks"></a>Trovare ed eliminare dischi gestiti scollegati 

Lo script seguente illustra come trovare dischi gestiti scollegati tramite la proprietà ManagedBy.  Lo script esegue un ciclo in tutti i dischi gestiti di una sottoscrizione e trova i dischi scollegati verificando se la proprietà *ManagedBy* è Null. La proprietà *ManagedBy* memorizza l'ID risorsa della macchina virtuale a cui è collegato un disco gestito. 

È consigliabile visualizzare prima tutti i dischi scollegati eseguendo lo script con la variabile *deleteUnattachedDisks* impostata su 0. Dopo aver esaminato i dischi scollegati, eseguire lo script impostando *deleteUnattachedDisks* su 1 per eliminarli tutti.

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
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Trovare ed eliminare dischi non gestiti scollegati 

I dischi non gestiti sono file disco rigido virtuale archiviati come [BLOB di pagine](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) in [account di Archiviazione di Azure](../../storage/common/storage-create-storage-account.md). Lo script seguente illustra come trovare dischi non gestiti scollegati (BLOB di pagine) tramite la proprietà LeaseStatus. Lo script esegue un ciclo in tutti i dischi non gestiti di tutti gli account di archiviazione di una sottoscrizione e trova i dischi non gestiti scollegati verificando se la proprietà *LeaseStatus* corrisponde a Unlocked. La proprietà *LeaseStatus* è impostata su Locked se un disco non gestito è collegato a una macchina virtuale. 

È consigliabile visualizzare prima tutti i dischi scollegati eseguendo lo script con la variabile *deleteUnattachedVHDs* impostata su 0. Dopo aver esaminato i dischi scollegati, eseguire lo script impostando *deleteUnattachedVHDs* su 1 per eliminarli tutti.


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



