---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299457"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Aree supportate
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

È possibile usare Azure PowerShell per creare uno snapshot incrementale. È necessaria la versione più recente di Azure PowerShell, il comando seguente verrà installato o aggiornare l'installazione esistente alla versione più recente:You will need the latest version of Azure PowerShell, the following command will either install it or update your existing installation to latest:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Una volta installato, accedere alla sessione `az login`di PowerShell con .

Per creare uno snapshot incrementale con Azure PowerShell, impostare `-Incremental` la configurazione con [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) con `-Snapshot` il parametro e quindi passarla come variabile a [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) tramite il parametro.

Sostituire `<yourDiskNameHere>` `<yourResourceGroupNameHere>`, `<yourDesiredSnapShotNameHere>` e con i valori, è possibile utilizzare lo script seguente per creare uno snapshot incrementale:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

È possibile identificare snapshot incrementali dallo `SourceResourceId` stesso `SourceUniqueId` disco con le proprietà e le proprietà degli snapshot. `SourceResourceId`è l'ID risorsa di Azure Resource Manager del disco padre. `SourceUniqueId`è il valore ereditato dalla `UniqueId` proprietà del disco. Se si elimina un disco e quindi si crea un nuovo `UniqueId` disco con lo stesso nome, il valore della proprietà cambia.

È possibile `SourceResourceId` `SourceUniqueId` utilizzare e creare un elenco di tutti gli snapshot associati a un disco specifico. Sostituire `<yourResourceGroupNameHere>` con il valore e quindi è possibile utilizzare l'esempio seguente per elencare gli snapshot incrementali esistenti:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

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

È anche possibile usare i modelli di Azure Resource Manager per creare uno snapshot incrementale. È necessario assicurarsi che apiVersion è impostato su **2019-03-01** e che anche la proprietà incrementale è impostata su true. Il frammento di codice seguente è un esempio di come creare uno snapshot incrementale con i modelli di Resource Manager:The following snippet is an example of how to create an incremental snapshot with Resource Manager templates:

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

Se si vuole vedere codice di esempio che illustra la funzionalità differenziale degli snapshot incrementali tramite .NET, vedere Copiare backup di dischi gestiti di Azure in [un'altra area con funzionalità differenziali di snapshot incrementali.](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)
