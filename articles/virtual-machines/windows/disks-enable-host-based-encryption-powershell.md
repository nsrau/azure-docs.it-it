---
title: Azure PowerShell abilitare la crittografia end-to-end nell'host VM
description: Come abilitare la crittografia end-to-end per le macchine virtuali di Azure usando la crittografia nell'host.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6cb6235c5c1a34cb3f48d315adee565591bb72c4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088462"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-powershell"></a>Abilitare la crittografia end-to-end usando la crittografia nel Azure PowerShell Host

Quando si Abilita la crittografia in host, i dati archiviati nell'host della macchina virtuale vengono crittografati a riposo e i flussi vengono crittografati nel servizio di archiviazione. Per informazioni concettuali sulla crittografia nell'host, nonché altri tipi di crittografia del disco gestito, vedere [crittografia end-to-end della crittografia per i dati della VM](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Aree supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

È anche possibile trovare le dimensioni delle macchine virtuali a livello di codice. Per informazioni su come recuperarli a livello di codice, vedere la sezione [ricerca di dimensioni di VM supportate](#finding-supported-vm-sizes) .

## <a name="prerequisites"></a>Prerequisiti

Per poter usare la crittografia nell'host per le VM o i set di scalabilità di macchine virtuali, è necessario abilitare la funzionalità nella sottoscrizione. Inviare un messaggio di posta elettronica a encryptionAtHost@microsoft . com con gli ID sottoscrizione per ottenere la funzionalità abilitata per le sottoscrizioni.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Creare una Azure Key Vault e DiskEncryptionSet

Quando la funzionalità è abilitata, è necessario configurare un Azure Key Vault e un DiskEncryptionSet, se non è già stato fatto.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>Abilitare la crittografia nell'host per i dischi collegati alla VM e ai set di scalabilità di macchine virtuali

È possibile abilitare la crittografia nell'host impostando una nuova proprietà EncryptionAtHost in securityProfile di macchine virtuali o set di scalabilità di macchine virtuali usando le API versione **2020-06-01** e successive.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>Script di esempio

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>Abilitare la crittografia nell'host per i dischi collegati a una macchina virtuale con chiavi gestite dal cliente

Creare una macchina virtuale con dischi gestiti usando l'URI di risorsa del DiskEncryptionSet creato in precedenza.

Sostituire `<yourPassword>` , `<yourVMName>` , `<yourVMSize>` , `<yourDESName>` , `<yoursubscriptionID>` , `<yourResourceGroupName>` e `<yourRegion>` , quindi eseguire lo script.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-diskEncryptionSetId "/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" `
-region "<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>Abilitare la crittografia nell'host per i dischi collegati a una macchina virtuale con chiavi gestite dalla piattaforma

Sostituire `<yourPassword>` ,,, `<yourVMName>` `<yourVMSize>` `<yourResourceGroupName>` e `<yourRegion>` , quindi eseguire lo script.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-region "<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>Ricerca delle dimensioni delle macchine virtuali supportate

Le dimensioni delle macchine virtuali legacy non sono supportate. È possibile trovare l'elenco delle dimensioni delle VM supportate in uno dei seguenti casi:

Chiamare l' [API SKU di risorse](/rest/api/compute/resourceskus/list) e verificare che la `EncryptionAtHostSupported` funzionalità sia impostata su **true**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Oppure, chiamando il cmdlet [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) di PowerShell.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state create e configurate queste risorse, è possibile usarle per proteggere i dischi gestiti. Il collegamento seguente contiene script di esempio, ognuno con un rispettivo scenario, che è possibile usare per proteggere i dischi gestiti.

[Esempi di modelli di Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
