---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/17/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 93c97c70a65be40b5801b202ba277b8f86ad1566
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88655284"
---
## <a name="limitations"></a>Limitazioni

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

I dischi condivisi supportano diversi sistemi operativi. Vedere le sezioni [Windows](../articles/virtual-machines/windows/disks-shared.md#windows) e [Linux](../articles/virtual-machines/linux/disks-shared.md#linux) dell'articolo concettuale per i sistemi operativi supportati.

## <a name="disk-sizes"></a>Dimensione disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Distribuire dischi condivisi

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Distribuire un'unità SSD Premium come disco condiviso

Per distribuire un disco gestito con la funzionalità disco condiviso abilitata, usare la nuova proprietà `maxShares` e definire un valore maggiore di 1. Questo rende il disco condivisibile tra più macchine virtuali.

> [!IMPORTANT]
> Il valore di `maxShares` può essere impostato o modificato solo quando un disco viene smontato da tutte le macchine virtuali. Vedere le [dimensioni dei dischi](#disk-sizes) per i valori consentiti per `maxShares` .

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Modello di Resource Manager](#tab/azure-resource-manager)

Prima di usare il modello seguente, sostituire `[parameters('dataDiskName')]` ,, `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` e `[parameters('maxShares')]` con i propri valori.

[SSD Premium modello di disco condiviso](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Distribuire un disco Ultra come disco condiviso

Per distribuire un disco gestito con la funzionalità disco condiviso abilitata, impostare il `maxShares` parametro su un valore maggiore di 1. Questo rende il disco condivisibile tra più macchine virtuali.

> [!IMPORTANT]
> Il valore di `maxShares` può essere impostato o modificato solo quando un disco viene smontato da tutte le macchine virtuali. Vedere le [dimensioni dei dischi](#disk-sizes) per i valori consentiti per `maxShares` .


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Esempio di disco regionale

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Esempio di disco di zona

Questo esempio è quasi identico a quello precedente, ma crea un disco nella zona di disponibilità 1.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Esempio di disco regionale

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Esempio di disco di zona

Questo esempio è quasi identico a quello precedente, ma crea un disco nella zona di disponibilità 1.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Modello di Resource Manager](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Esempio di disco regionale

Prima di usare il modello seguente, sostituire,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` , `[parameters('diskMBpsReadWrite')]` , `[parameters('diskIOPSReadOnly')]` e `[parameters('diskMBpsReadOnly')]` con valori personalizzati.

[Modello di dischi Ultra condivisi a livello di area](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Esempio di disco di zona

Prima di usare il modello seguente, sostituire,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` , `[parameters('diskMBpsReadWrite')]` , `[parameters('diskIOPSReadOnly')]` e `[parameters('diskMBpsReadOnly')]` con valori personalizzati.

[Modello di dischi Ultra condivisi di zona](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Uso di dischi condivisi di Azure con le macchine virtuali

Una volta distribuito un disco condiviso con `maxShares>1` , è possibile montare il disco in una o più macchine virtuali.

> [!NOTE]
> Se si distribuisce un disco Ultra, assicurarsi che corrisponda ai requisiti necessari. Per informazioni dettagliate, vedere la sezione [PowerShell](../articles/virtual-machines/disks-enable-ultra-ssd.md#enable-ultra-disk-compatibility-on-an-existing-vm-1) o l' [interfaccia](../articles/virtual-machines/disks-enable-ultra-ssd.md#enable-ultra-disk-compatibility-on-an-existing-vm) della riga di comando dell'articolo su disco Ultra.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

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

Se si preferisce usare modelli di Azure Resource Manager per distribuire il disco, sono disponibili i modelli di esempio seguenti:
- [SSD Premium](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Dischi Ultra regionali](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Dischi ultra di zona](https://aka.ms/SharedUltraDiskARMtemplateZonal)