---
title: Trovare ed eliminare dischi gestiti e non gestiti di Azure scollegati | Microsoft Docs
description: Come trovare ed eliminare dischi gestiti e non gestiti di Azure (dischi rigidi virtuali/BLOB di pagine) scollegati tramite Azure PowerShell.
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Trovare ed eliminare dischi gestiti e non gestiti di Azure scollegati
Quando si elimina una macchina virtuale in Azure, i dischi collegati a questa non vengono eliminati per impostazione predefinita. Ciò impedisce perdite di dati dovute all'eliminazione di macchine virtuali per errore, ma in questo modo si continua a pagare per i dischi scollegati. Usare questo articolo per trovare ed eliminare tutti i dischi scollegati e risparmiare sui costi. 


## <a name="find-and-delete-unattached-managed-disks"></a>Trovare ed eliminare dischi gestiti scollegati 

Lo script seguente illustra come trovare [dischi gestiti](managed-disks-overview.md) scollegati tramite la proprietà *ManagedBy*. Lo script esegue un ciclo in tutti i dischi gestiti di una sottoscrizione e trova i dischi scollegati verificando se la proprietà *ManagedBy* è Null. La proprietà *ManagedBy* memorizza l'ID risorsa della macchina virtuale a cui è collegato un disco gestito.

È consigliabile visualizzare prima tutti i dischi scollegati eseguendo lo script con la variabile *deleteUnattachedDisks* impostata su 0. Dopo aver esaminato i dischi scollegati, eseguire lo script impostando *deleteUnattachedDisks* su 1 per eliminarli tutti.

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Trovare ed eliminare dischi non gestiti scollegati 

I dischi non gestiti sono file VHD archiviati come [BLOB di pagine] (/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) in [account di Archiviazione di Azure](../../storage/common/storage-create-storage-account.md). Lo script seguente illustra come trovare dischi non gestiti scollegati (BLOB di pagine) tramite la proprietà *LeaseStatus*. Lo script esegue un ciclo in tutti i dischi non gestiti di tutti gli account di archiviazione di una sottoscrizione e trova i dischi non gestiti scollegati verificando se la proprietà *LeaseStatus* corrisponde a Unlocked. La proprietà *LeaseStatus* è impostata su Locked se un disco non gestito è collegato a una macchina virtuale.

È consigliabile visualizzare prima tutti i dischi scollegati eseguendo lo script con la variabile *deleteUnattachedVHDs* impostata su 0. Dopo aver esaminato i dischi scollegati, eseguire lo script impostando *deleteUnattachedVHDs* su 1 per eliminarli tutti.


```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}

```

## <a name="next-steps"></a>Passaggi successivi

[Eliminare un account di archiviazione](../../storage/common/storage-create-storage-account.md)




