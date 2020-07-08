---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0df74b82c847c9738d97d2001573666714c17672
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81008344"
---
## <a name="limitations"></a>Limitazioni

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Dimensione disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Distribuire dischi condivisi

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Distribuire un'unità SSD Premium come disco condiviso

Per distribuire un disco gestito con la funzionalità disco condiviso abilitata, usare la nuova proprietà `maxShares` e definire un valore maggiore di 1. Questo rende il disco condivisibile tra più macchine virtuali.

> [!IMPORTANT]
> Il valore di `maxShares` può essere impostato o modificato solo quando un disco viene smontato da tutte le macchine virtuali. Vedere le [dimensioni dei dischi](#disk-sizes) per i valori consentiti per `maxShares` .

Prima di usare il modello seguente, sostituire `[parameters('dataDiskName')]` ,, `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` e `[parameters('maxShares')]` con i propri valori.

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

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Distribuire un disco Ultra come disco condiviso

#### <a name="cli"></a>CLI

Per distribuire un disco gestito con la funzionalità disco condiviso abilitata, impostare il `maxShares` parametro su un valore maggiore di 1. Questo rende il disco condivisibile tra più macchine virtuali.

> [!IMPORTANT]
> Il valore di `maxShares` può essere impostato o modificato solo quando un disco viene smontato da tutte le macchine virtuali. Vedere le [dimensioni dei dischi](#disk-sizes) per i valori consentiti per `maxShares` .

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Per distribuire un disco gestito con la funzionalità disco condiviso abilitata, utilizzare la proprietà `maxShares` e definire un valore maggiore di 1. Questo rende il disco condivisibile tra più macchine virtuali.

> [!IMPORTANT]
> Il valore di `maxShares` può essere impostato o modificato solo quando un disco viene smontato da tutte le macchine virtuali. Vedere le [dimensioni dei dischi](#disk-sizes) per i valori consentiti per `maxShares` .

Prima di usare il modello seguente, sostituire,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` , `[parameters('diskMBpsReadWrite')]` , `[parameters('diskIOPSReadOnly')]` e `[parameters('diskMBpsReadOnly')]` con valori personalizzati.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Uso di dischi condivisi di Azure con le macchine virtuali

Una volta distribuito un disco condiviso con `maxShares>1` , è possibile montare il disco in una o più macchine virtuali.

> [!IMPORTANT]
> Tutte le macchine virtuali che condividono un disco devono essere distribuite nello stesso [gruppo di posizionamento di prossimità](../articles/virtual-machines/windows/proximity-placement-groups.md).

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

## <a name="supported-scsi-pr-commands"></a>Comandi della richiesta pull di SCSI supportati

Dopo aver montato il disco condiviso nelle VM del cluster, è possibile stabilire il quorum e leggere/scrivere sul disco usando la richiesta pull SCSI. Quando si usano i dischi condivisi di Azure, sono disponibili i comandi di richiesta pull seguenti:

Per interagire con il disco, iniziare con l'elenco permanente-prenotazione-azione:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Quando si usa PR_RESERVE, PR_PREEMPT_RESERVATION o PR_RELEASE_RESERVATION, fornire uno dei seguenti tipi di prenotazione permanente:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

È anche necessario specificare una chiave di prenotazione permanente quando si usa PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION o la prenotazione di PR_RELEASE.


## <a name="next-steps"></a>Passaggi successivi

Se si è interessati a provare i dischi condivisi, [iscriversi per l'anteprima](https://aka.ms/AzureSharedDiskPreviewSignUp).