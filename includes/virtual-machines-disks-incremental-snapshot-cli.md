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
ms.openlocfilehash: 69be71a58c3aed4f52b77e63c9ddf12365301b08
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299176"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Aree supportate
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

È possibile creare uno snapshot incrementale con l'interfaccia della riga di comando di Azure. sarà necessaria la versione più recente dell'interfaccia della riga di comando di Azure. 

In Windows, il comando seguente consente di installare o aggiornare l'installazione esistente alla versione più recente:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
In Linux l'installazione dell'interfaccia della riga di comando può variare a seconda della versione del sistema operativo.  Vedere [installare l'interfaccia della](https://docs.microsoft.com/cli/azure/install-azure-cli) riga di comando di Azure per la versione specifica di Linux.

Per creare uno snapshot incrementale, usare [AZ snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) con il parametro `--incremental`.

Nell'esempio seguente viene creato uno snapshot incrementale, viene sostituito `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`,`<exampleDiskName>`e `<exampleLocation>` con valori personalizzati, quindi viene eseguito l'esempio:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

È possibile identificare gli snapshot incrementali dallo stesso disco con le proprietà `SourceResourceId` e `SourceUniqueId` degli snapshot. `SourceResourceId` è l'ID di risorsa Azure Resource Manager del disco padre. `SourceUniqueId` è il valore ereditato dalla proprietà `UniqueId` del disco. Se si elimina un disco e quindi si crea un nuovo disco con lo stesso nome, viene modificato il valore della proprietà `UniqueId`.

È possibile usare `SourceResourceId` e `SourceUniqueId` per creare un elenco di tutti gli snapshot associati a un disco specifico. Nell'esempio seguente vengono elencati tutti gli snapshot incrementali associati a un disco specifico, ma è necessario un certo programma di installazione.

Questo esempio USA JQ per eseguire query sui dati. Per eseguire l'esempio, è necessario [installare JQ](https://stedolan.github.io/jq/download/).

Sostituire `<yourResourceGroupNameHere>` e `<exampleDiskName>` con i valori, è possibile usare l'esempio seguente per elencare gli snapshot incrementali esistenti, purché sia stato installato anche JQ:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
