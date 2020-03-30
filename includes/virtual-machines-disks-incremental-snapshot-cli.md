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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343059"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

You can create an incremental snapshot with the Azure CLI, you will need the latest version of Azure CLI. 

In Windows, il comando seguente consente di installare o aggiornare l'installazione esistente alla versione più recente:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
In Linux, l'installazione dell'interfaccia della riga di comando varia a seconda della versione del sistema operativo.  Vedere [Installare l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli) di Azure per la versione Linux specifica.

Per creare uno snapshot incrementale, `--incremental` utilizzare az snapshot [create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) con il parametro .

Nell'esempio seguente viene creato `<yourDesiredSnapShotNameHere>` `<yourResourceGroupNameHere>`uno`<exampleDiskName>`snapshot `<exampleLocation>` incrementale, replace , , e con valori personalizzati, quindi viene eseguito l'esempio:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

È possibile identificare snapshot incrementali dallo `SourceResourceId` stesso `SourceUniqueId` disco con le proprietà e le proprietà degli snapshot. `SourceResourceId`è l'ID risorsa di Azure Resource Manager del disco padre. `SourceUniqueId`è il valore ereditato dalla `UniqueId` proprietà del disco. Se si elimina un disco e quindi si crea un nuovo `UniqueId` disco con lo stesso nome, il valore della proprietà cambia.

È possibile `SourceResourceId` `SourceUniqueId` utilizzare e creare un elenco di tutti gli snapshot associati a un disco specifico. Nell'esempio seguente verranno elencati tutti gli snapshot incrementali associati a un determinato disco, ma è necessaria una configurazione.

In questo esempio viene utilizzato jq per l'esecuzione di query sui dati. Per eseguire l'esempio, è necessario [installare jq](https://stedolan.github.io/jq/download/).

Sostituire `<yourResourceGroupNameHere>` `<exampleDiskName>` e con i valori, quindi è possibile utilizzare l'esempio seguente per elencare gli snapshot incrementali esistenti, purché sia stato installato jq:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
