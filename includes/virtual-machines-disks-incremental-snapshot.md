---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224568"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Creazione di uno snapshot incrementale (anteprima) per Managed Disks

Gli snapshot incrementali (anteprima) sono backup temporizzati per i dischi gestiti che, quando vengono eseguiti, sono costituiti solo da tutte le modifiche apportate dopo l'ultimo snapshot. Quando si tenta di scaricare o utilizzare in altro modo uno snapshot incrementale, viene utilizzato il VHD completo. Questa nuova funzionalità per gli snapshot dei dischi gestiti può consentire loro di essere più conveniente, perché non è più necessario archiviare l'intero disco con ogni singolo snapshot, a meno che non si scelga di. Analogamente agli snapshot regolari, è possibile usare gli snapshot incrementali per creare un disco gestito completo o, per creare uno snapshot regolare.

Esistono alcune differenze tra uno snapshot incrementale e uno snapshot regolare. Gli snapshot incrementali utilizzeranno sempre l'archiviazione HDD standard, indipendentemente dal tipo di archiviazione del disco, mentre gli snapshot regolari possono usare unità SSD Premium. Se si usano snapshot regolari nell'archiviazione Premium per la scalabilità verticale delle distribuzioni di macchine virtuali, è consigliabile usare immagini personalizzate nell'archiviazione standard della [raccolta immagini condivise](../articles/virtual-machines/linux/shared-image-galleries.md). Consente di ottenere una scalabilità più massiccia con costi ridotti. Inoltre, gli snapshot incrementali offrono potenzialmente una migliore affidabilità con l' [archiviazione con ridondanza della zona](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Se ZRS è disponibile nell'area selezionata, verrà usato automaticamente ZRS per uno snapshot incrementale. Se ZRS non è disponibile nell'area, lo snapshot utilizzerà per impostazione predefinita l' [archiviazione con ridondanza locale](../articles/storage/common/storage-redundancy-lrs.md) (con ridondanza locale). È possibile eseguire l'override di questo comportamento e selezionarne uno manualmente, ma non è consigliabile.

Gli snapshot incrementali offrono inoltre una funzionalità differenziale, disponibile in modo univoco per i dischi gestiti. Consentono di ottenere le modifiche tra due snapshot incrementali degli stessi dischi gestiti, fino al livello di blocco. È possibile usare questa funzionalità per ridurre il footprint dei dati durante la copia degli snapshot tra le aree.

Se non è ancora stata effettuata l'iscrizione per l'anteprima e si vuole iniziare a usare gli snapshot incrementali, inviare un messaggio AzureDisks@microsoft.com di posta elettronica all'indirizzo per ottenere l'accesso all'anteprima pubblica.

## <a name="restrictions"></a>Restrizioni

- Attualmente non è possibile creare snapshot incrementali dopo aver modificato le dimensioni di un disco.
- Non è attualmente possibile spostare gli snapshot incrementali tra le sottoscrizioni.
- Attualmente, è possibile generare solo URI SAS di un massimo di cinque snapshot di una particolare famiglia di snapshot in un determinato momento.
- Non è possibile creare uno snapshot incrementale per un disco specifico al di fuori della sottoscrizione del disco.
- È possibile creare fino a sette snapshot incrementali per ogni disco ogni cinque minuti.
- Per un singolo disco è possibile creare un totale di 200 snapshot incrementali.

## <a name="powershell"></a>PowerShell

È possibile utilizzare Azure PowerShell per creare uno snapshot incrementale. È possibile installare la versione più recente di PowerShell in locale. Sarà necessaria la versione più recente di Azure PowerShell, il seguente comando lo installerà o aggiornerà l'installazione esistente alla versione più recente:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Una volta installato, accedere alla sessione di PowerShell con `az login`.

Sostituire `<yourDiskNameHere>`, `<yourResourceGroupNameHere>` e`<yourDesiredSnapShotNameHere>` con i valori, è possibile usare lo script seguente per creare uno snapshot incrementale:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
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

## <a name="cli"></a>Interfaccia della riga di comando

È possibile creare uno snapshot incrementale con l'interfaccia della riga di comando di Azure usando [AZ snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create). Un comando di esempio avrà un aspetto simile al seguente:

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

È anche possibile identificare quali snapshot sono snapshot incrementali nell'interfaccia della riga di comando usando il `--query` parametro in [AZ snapshot Show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show). È possibile usare questo parametro per eseguire direttamente una query sulle proprietà **SourceResourceId** e **SourceUniqueId** degli snapshot. SourceResourceId è l'ID di risorsa Azure Resource Manager del disco padre. **SourceUniqueId** è il valore ereditato dalla proprietà **UniqueId** del disco. Se si elimina un disco e quindi si crea un disco con lo stesso nome, il valore della proprietà **UniqueId** verrà modificato.

Gli esempi di query potrebbero essere simili ai seguenti:

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>Passaggi successivi

Se non è ancora stata effettuata l'iscrizione per l'anteprima e si vuole iniziare a usare gli snapshot incrementali, inviare un messaggio AzureDisks@microsoft.com di posta elettronica all'indirizzo per ottenere l'accesso all'anteprima pubblica.
