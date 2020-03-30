---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26e76731f663ac9038bc87182d52c4bd245f1b6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471694"
---
## <a name="limitations"></a>Limitazioni

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Dimensione disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Distribuire un disco condiviso di AzureDeploy an Azure shared disk

Per distribuire un disco gestito con la funzionalità `maxShares` disco condiviso `>1`abilitata, utilizzare la nuova proprietà e definire un valore . In questo modo il disco condisistibile tra più macchine virtuali.

> [!IMPORTANT]
> Il valore `maxShares` di può essere impostato o modificato solo quando un disco viene smontato da tutte le macchine virtuali. Vedere [le dimensioni del](#disk-sizes) disco `maxShares`per i valori consentiti per .

Prima di utilizzare il `[parameters('dataDiskName')]` `[resourceGroup().location]`modello `[parameters('dataDiskSizeGB')]`seguente, sostituire , , e `[parameters('maxShares')]` con valori personalizzati.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Uso di dischi condivisi di Azure con le macchine virtualiUsing Azure shared disks with your VMs

Dopo aver distribuito un `maxShares>1`disco condiviso con , è possibile montare il disco in una o più macchine virtuali.

> [!IMPORTANT]
> Tutte le macchine virtuali che condividono un disco devono essere distribuite nello stesso gruppo di posizionamento di [prossimità.](../articles/virtual-machines/windows/proximity-placement-groups.md)

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Comandi SCSI PR supportati

Dopo aver montato il disco condiviso nelle macchine virtuali del cluster, è possibile stabilire il quorum e leggere/scrivere sul disco usando SCSI PR. Quando si usano dischi condivisi di Azure, sono disponibili i comandi PR seguenti:The following PR commands are available when using Azure shared disks:

Per interagire con il disco, iniziare con l'elenco delle azioni di prenotazione persistente:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Quando si utilizza PR_RESERVE, PR_PREEMPT_RESERVATION o PR_RELEASE_RESERVATION, fornire uno dei seguenti tipi di prenotazione persistente:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

È inoltre necessario fornire una chiave di prenotazione permanente quando si usa PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION o PR_RELEASE-RESERVATION.


## <a name="next-steps"></a>Passaggi successivi

Se sei interessato a provare i dischi condivisi, [iscriviti per la nostra anteprima.](https://aka.ms/AzureSharedDiskPreviewSignUp)