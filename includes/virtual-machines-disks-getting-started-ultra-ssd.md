---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 63045bf1b836215b00b9b7c1b46dd208152fa772
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74101120"
---
Azure ultra Disks offre velocità effettiva elevata, IOPS elevate e archiviazione su disco a bassa latenza coerente per macchine virtuali IaaS di Azure. Questa nuova offerta fornisce prestazioni all'avanguardia con gli stessi livelli di disponibilità delle offerte di dischi esistenti. Uno dei vantaggi principali di ultra disks è la possibilità di modificare dinamicamente le prestazioni dell'unità SSD insieme ai carichi di lavoro senza dover riavviare le macchine virtuali. I dischi Ultra sono adatti per carichi di lavoro con utilizzo intensivo di dati, ad esempio SAP HANA, database di livello superiore e carichi di lavoro pesanti per le transazioni.

## <a name="ga-scope-and-limitations"></a>Ambito e limitazioni di GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Determinare la disponibilità di dimensioni e aree della macchina virtuale

Per sfruttare i dischi Ultra, è necessario determinare la zona di disponibilità in cui ci si trova. Non ogni area supporta tutte le dimensioni delle macchine virtuali con dischi Ultra. Per determinare se la regione, la zona e le dimensioni della macchina virtuale supportano i dischi Ultra, eseguire uno dei comandi seguenti, assicurarsi di sostituire prima i valori di **Region**, **vmSize**e **Subscription** :

Interfaccia della riga di comando:

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

La risposta sarà simile a quella riportata di seguito, dove X è la zona da usare per la distribuzione nell'area scelta. X può essere 1, 2 o 3.

Mantenere il valore **Zones** , che rappresenta la zona di disponibilità e sarà necessario per distribuire un disco Ultra.

|ResourceType  |Nome  |Location  |Zone  |Restrizione  |Funzionalità  |Valore  |
|---------|---------|---------|---------|---------|---------|---------|
|dischi     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se non è stata trovata alcuna risposta dal comando, le dimensioni della macchina virtuale selezionate non sono supportate con i dischi ultra nell'area selezionata.

Ora che si conosce la zona in cui eseguire la distribuzione, seguire i passaggi di distribuzione in questo articolo per distribuire una macchina virtuale con un disco Ultra collegato o collegare un disco Ultra a una macchina virtuale esistente.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Distribuire un disco Ultra usando Azure Resource Manager

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle VM supportate, vedere la sezione [ambito e limitazioni di GA](#ga-scope-and-limitations) .

Se si vuole creare una VM con più dischi Ultra, vedere l'esempio [creare una macchina virtuale con più dischi Ultra](https://aka.ms/ultradiskArmTemplate).

Se si intende usare un modello personalizzato, assicurarsi che **apiVersion** per `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` sia impostato su `2018-06-01` (o versione successiva).

Impostare lo SKU del disco su **UltraSSD_LRS**, quindi impostare la capacità del disco, IOPS, la zona di disponibilità e la velocità effettiva in Mbps per creare un disco Ultra.

Dopo aver effettuato il provisioning della macchina virtuale, è possibile partizionare e formattare i dischi dati e configurarli per i carichi di lavoro.

## <a name="deploy-an-ultra-disk-using-cli"></a>Distribuire un disco Ultra usando l'interfaccia della riga di comando

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle VM supportate, vedere la sezione [ambito e limitazioni di GA](#ga-scope-and-limitations) .

È necessario creare una macchina virtuale in grado di usare dischi Ultra, per poter alleghiare un disco Ultra.

Sostituire o impostare le variabili **$VMName**, **$RgName**, **$DiskName**, **$location**, $password **$User con** valori personalizzati. Impostare **$zone** sul valore della zona di disponibilità ottenuta dall' [inizio di questo articolo](#determine-vm-size-and-region-availability). Eseguire quindi il comando dell'interfaccia della riga di comando seguente per creare una macchina virtuale ultra abilitata:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Creare un disco Ultra usando l'interfaccia della riga di comando

Ora che si dispone di una macchina virtuale in grado di alleghire dischi Ultra, è possibile creare e collegarvi un disco Ultra.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Aggiungere un disco Ultra a una VM usando l'interfaccia della riga di comando

In alternativa, se la macchina virtuale esistente si trova in un'area geografica o in una zona di disponibilità in grado di usare dischi Ultra, è possibile usare dischi Ultra senza dover creare una nuova macchina virtuale.

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Regolare le prestazioni di un disco Ultra usando l'interfaccia della riga di comando

I dischi Ultra offrono una funzionalità univoca che consente di regolare le prestazioni. il comando seguente illustra come usare questa funzionalità:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Distribuire un disco Ultra con PowerShell

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle VM supportate, vedere la sezione [ambito e limitazioni di GA](#ga-scope-and-limitations) .

Per usare dischi Ultra, è necessario creare una macchina virtuale in grado di usare dischi Ultra. Sostituire o impostare le variabili **$ResourceGroup** e **$vmName** con valori personalizzati. Impostare **$zone** sul valore della zona di disponibilità ottenuta dall' [inizio di questo articolo](#determine-vm-size-and-region-availability). Eseguire quindi il comando [New-AzVm](/powershell/module/az.compute/new-azvm) seguente per creare una macchina virtuale ultra abilitata:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>Creare un disco Ultra con PowerShell

Ora che si dispone di una macchina virtuale in grado di usare dischi Ultra, è possibile creare e alleghirvi un disco Ultra:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Aggiungere un disco Ultra a una VM tramite PowerShell

In alternativa, se la macchina virtuale esistente si trova in un'area geografica o in una zona di disponibilità in grado di usare dischi Ultra, è possibile usare dischi Ultra senza dover creare una nuova macchina virtuale.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Regolare le prestazioni di un disco Ultra con PowerShell

I dischi Ultra hanno una funzionalità univoca che consente di regolare le prestazioni. il comando seguente è un esempio che consente di modificare le prestazioni senza dover scollegare il disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Passaggi successivi

Se si desidera provare il nuovo tipo di disco [richiedere l'accesso con questo sondaggio](https://aka.ms/UltraDiskSignup).
