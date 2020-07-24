---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9e7386e21442b5a76aae656a36e2858b52ecef65
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102759"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Aree supportate
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

È possibile utilizzare Azure PowerShell per creare uno snapshot incrementale. Sarà necessaria la versione più recente di Azure PowerShell, il seguente comando lo installerà o aggiornerà l'installazione esistente alla versione più recente:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Una volta installato, accedere alla sessione di PowerShell con `Connect-AzAccount` .

Per creare uno snapshot incrementale con Azure PowerShell, impostare la configurazione con [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) con il `-Incremental` parametro e quindi passarlo come variabile a [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) tramite il `-Snapshot` parametro.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

È possibile identificare gli snapshot incrementali dallo stesso disco con `SourceResourceId` e le `SourceUniqueId` proprietà degli snapshot. `SourceResourceId`ID della risorsa Azure Resource Manager del disco padre. `SourceUniqueId`valore ereditato dalla `UniqueId` proprietà del disco. Se si elimina un disco e quindi si crea un nuovo disco con lo stesso nome, il valore della `UniqueId` proprietà cambia.

È possibile utilizzare `SourceResourceId` e `SourceUniqueId` per creare un elenco di tutti gli snapshot associati a un disco specifico. Sostituire `<yourResourceGroupNameHere>` con il valore, quindi è possibile usare l'esempio seguente per elencare gli snapshot incrementali esistenti:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Modello di Resource Manager

È inoltre possibile utilizzare Azure Resource Manager modelli per creare uno snapshot incrementale. È necessario assicurarsi che apiVersion sia impostato su **2019-03-01** e che anche la proprietà incrementale sia impostata su true. Il frammento di codice seguente è un esempio di creazione di uno snapshot incrementale con i modelli di Gestione risorse:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Se si vuole vedere il codice di esempio che illustra la funzionalità differenziale degli snapshot incrementali, usando .NET, vedere [copiare i backup di Azure Managed disks in un'altra area con funzionalità differenziali di snapshot incrementali](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
