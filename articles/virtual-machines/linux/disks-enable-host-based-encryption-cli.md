---
title: Abilitare la crittografia end-to-end usando la crittografia nell'host-dischi gestiti dell'interfaccia della riga di comando di Azure
description: Usare la crittografia nell'host per abilitare la crittografia end-to-end su Azure Managed Disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ff56654981ef69648b1fa7ad11a8681c887289f6
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816967"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Usare l'interfaccia della riga di comando di Azure per abilitare la crittografia end-to-end usando la crittografia nell'host

Quando si Abilita la crittografia in host, i dati archiviati nell'host della macchina virtuale vengono crittografati a riposo e i flussi vengono crittografati nel servizio di archiviazione. Per informazioni concettuali sulla crittografia nell'host, nonché altri tipi di crittografia del disco gestito, vedere [crittografia end-to-end della crittografia per i dati della VM](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Aree supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

È anche possibile trovare le dimensioni delle macchine virtuali a livello di codice. Per informazioni su come recuperarli a livello di codice, vedere la sezione [ricerca di dimensioni di VM supportate](#finding-supported-vm-sizes) .

## <a name="prerequisites"></a>Prerequisiti

Per poter usare la crittografia nell'host per le VM o i set di scalabilità di macchine virtuali, è necessario abilitare la funzionalità nella sottoscrizione. Inviare un messaggio di posta elettronica a encryptionAtHost@microsoft.com con gli ID sottoscrizione per ottenere la funzionalità abilitata per le sottoscrizioni.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Creare una Azure Key Vault e DiskEncryptionSet

Quando la funzionalità è abilitata, è necessario configurare un Azure Key Vault e un DiskEncryptionSet, se non è già stato fatto.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Esempi

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Creare una VM con la crittografia nell'host abilitata con chiavi gestite dal cliente. 

Creare una macchina virtuale con dischi gestiti usando l'URI di risorsa del DiskEncryptionSet creato in precedenza per crittografare la cache del sistema operativo e i dischi dati con chiavi gestite dal cliente. I dischi temporanei vengono crittografati con chiavi gestite dalla piattaforma. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Creare una VM con crittografia nell'host abilitata con chiavi gestite dalla piattaforma. 

Creare una macchina virtuale con la crittografia nell'host abilitata per crittografare la cache di dischi del sistema operativo/dati e dischi temporanei con chiavi gestite dalla piattaforma. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Aggiornare una macchina virtuale per abilitare la crittografia nell'host. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Verificare lo stato della crittografia nell'host per una macchina virtuale

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Creare un set di scalabilità di macchine virtuali con crittografia in host abilitato con chiavi gestite dal cliente. 

Creare un set di scalabilità di macchine virtuali con dischi gestiti usando l'URI di risorsa del DiskEncryptionSet creato in precedenza per crittografare la cache del sistema operativo e i dischi dati con chiavi gestite dal cliente. I dischi temporanei vengono crittografati con chiavi gestite dalla piattaforma. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Creare un set di scalabilità di macchine virtuali con crittografia in host abilitato con chiavi gestite dalla piattaforma. 

Creare un set di scalabilità di macchine virtuali con la crittografia in host abilitata per crittografare la cache dei dischi del sistema operativo/dati e i dischi temporanei con chiavi gestite dalla piattaforma. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Aggiornare un set di scalabilità di macchine virtuali per abilitare la crittografia nell'host. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Verificare lo stato della crittografia nell'host per un set di scalabilità di macchine virtuali

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
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
