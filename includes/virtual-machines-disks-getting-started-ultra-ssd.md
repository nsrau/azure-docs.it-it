---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 269cc52f1e96a6864de55f729fe39a5f609d35c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84902586"
---
Azure ultra Disks offre velocità effettiva elevata, IOPS elevate e archiviazione su disco a bassa latenza coerente per macchine virtuali IaaS di Azure. Questa nuova offerta fornisce prestazioni all'avanguardia con gli stessi livelli di disponibilità delle offerte di dischi esistenti. Uno dei vantaggi principali di ultra disks è la possibilità di modificare dinamicamente le prestazioni dell'unità SSD insieme ai carichi di lavoro senza dover riavviare le macchine virtuali. I dischi Ultra sono idonei per carichi di lavoro a elevato utilizzo di dati, come SAP HANA, database di alto livello e carichi di lavoro con numerose transazioni.

## <a name="ga-scope-and-limitations"></a>Ambito e limitazioni di GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Determinare la disponibilità di dimensioni e aree della macchina virtuale

### <a name="vms-using-availability-zones"></a>VM che usano le zone di disponibilità

Per sfruttare i dischi Ultra, è necessario determinare la zona di disponibilità in cui ci si trova. Non ogni area supporta tutte le dimensioni delle macchine virtuali con dischi Ultra. Per determinare se la regione, la zona e le dimensioni della macchina virtuale supportano i dischi Ultra, eseguire uno dei comandi seguenti, assicurarsi di sostituire prima i valori di **Region**, **vmSize**e **Subscription** :

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

La risposta sarà simile a quella riportata di seguito, dove X è la zona da usare per la distribuzione nell'area scelta. X può essere 1, 2 o 3.

Mantenere il valore **Zones** , che rappresenta la zona di disponibilità e sarà necessario per distribuire un disco Ultra.

|ResourceType  |Nome  |Location  |Zone  |Restrizione  |Funzionalità  |Valore  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se non è stata trovata alcuna risposta dal comando, le dimensioni della macchina virtuale selezionate non sono supportate con i dischi ultra nell'area selezionata.

Ora che si conosce la zona in cui eseguire la distribuzione, seguire i passaggi di distribuzione in questo articolo per distribuire una macchina virtuale con un disco Ultra collegato o collegare un disco Ultra a una macchina virtuale esistente.

### <a name="vms-with-no-redundancy-options"></a>VM senza opzioni di ridondanza

Per il momento, i dischi Ultra distribuiti negli Stati Uniti occidentali devono essere distribuiti senza opzioni di ridondanza. Tuttavia, non tutte le dimensioni del disco che supportano i dischi Ultra possono trovarsi in questa area. Per determinare quali di essi negli Stati Uniti occidentali supportano dischi Ultra, è possibile usare uno dei frammenti di codice seguenti. Assicurarsi di sostituire prima i `vmSize` `subscription` valori e:

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

La risposta sarà simile alla seguente: `UltraSSDAvailable   True` indica se le dimensioni della macchina virtuale supportano dischi ultra in questa area.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Distribuire un disco Ultra usando Azure Resource Manager

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle VM supportate, vedere la sezione [ambito e limitazioni di GA](#ga-scope-and-limitations) .

Se si vuole creare una VM con più dischi Ultra, vedere l'esempio [creare una macchina virtuale con più dischi Ultra](https://aka.ms/ultradiskArmTemplate).

Se si intende usare un modello personalizzato, assicurarsi che **apiVersion** per `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` sia impostato su `2018-06-01` (o versione successiva).

Impostare lo SKU del disco su **UltraSSD_LRS**, quindi impostare la capacità del disco, IOPS, la zona di disponibilità e la velocità effettiva in Mbps per creare un disco Ultra.

Dopo aver effettuato il provisioning della macchina virtuale, è possibile partizionare e formattare i dischi dati e configurarli per i carichi di lavoro.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Distribuire un disco Ultra usando il portale di Azure

In questa sezione viene illustrata la distribuzione di una macchina virtuale dotata di disco rigido come disco dati. Si presuppone che l'utente abbia familiarità con la distribuzione di una macchina virtuale. in caso contrario, vedere [la Guida introduttiva: creare una macchina virtuale Windows nel portale di Azure](../articles/virtual-machines/windows/quick-create-portal.md).

- Accedere al [portale di Azure](https://portal.azure.com/) e passare a distribuire una macchina virtuale (VM).
- Assicurarsi di scegliere [le dimensioni e l'area della VM supportate](#ga-scope-and-limitations).
- Selezionare **zona di disponibilità** in **Opzioni di disponibilità**.
- Inserire le voci rimanenti con le selezioni preferite.
- Selezionare **Dischi**.

![create-ultra-disk-enabled-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Nel pannello dischi selezionare **Sì** per **Abilita compatibilità dischi Ultra**.
- Selezionare **Crea e alleghi un nuovo disco** per alleghi ora un disco Ultra.

![enable-and-attach-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Nel pannello **Crea un nuovo disco** immettere un nome, quindi selezionare **modifica dimensioni**.
- Modificare il **tipo di account** in **ultra disk**.
- Modificare i valori delle **dimensioni del disco personalizzato (GIB)**, **IOPS del disco**e la **velocità effettiva del disco** in uno di loro scelta.
- Selezionare **OK** in entrambi i pannelli.
- Continuare con la distribuzione della macchina virtuale, sarà uguale a quella di qualsiasi altra macchina virtuale.

![create-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Alleghi un disco Ultra usando il portale di Azure

In alternativa, se la macchina virtuale esistente si trova in un'area geografica o in una zona di disponibilità in grado di usare dischi Ultra, è possibile usare dischi Ultra senza dover creare una nuova macchina virtuale. Abilitando i dischi Ultra nella VM esistente, quindi li alleghi come dischi dati.

- Passare alla macchina virtuale e selezionare **dischi**.
- Selezionare **Modifica**.

![options-selector-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Selezionare **Sì** per **Enable ultra disk Compatibility**.

![ultra-options-yes-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Selezionare **Salva**.
- Selezionare **Aggiungi disco dati** quindi nell'elenco a discesa per **nome** Selezionare **Crea disco**.

![create-and-attach-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Immettere un nome per il nuovo disco e quindi selezionare **modifica dimensioni**.
- Modificare il **tipo di account** in **ultra disk**.
- Modificare i valori delle **dimensioni del disco personalizzato (GIB)**, **IOPS del disco**e la **velocità effettiva del disco** in uno di loro scelta.
- Selezionare **OK** , quindi selezionare **Crea**.

![making-a-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Dopo che si è tornati al pannello del disco, selezionare **Salva**.

![saving-and-attaching-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Regolare le prestazioni di un disco Ultra usando il portale di Azure

I dischi Ultra offrono una funzionalità univoca che consente di regolare le prestazioni. Queste modifiche possono essere apportate dal portale di Azure sui dischi.

- Passare alla macchina virtuale e selezionare **dischi**.
- Selezionare il disco Ultra a cui si desidera modificare le prestazioni.

![selecting-ultra-disk-to-modify.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Selezionare **configurazione** , quindi apportare le modifiche.
- Selezionare **Salva**.

![configuring-ultra-disk-performance-and-size.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Distribuire un disco Ultra usando l'interfaccia della riga di comando

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle VM supportate, vedere la sezione [ambito e limitazioni di GA](#ga-scope-and-limitations) .

È necessario creare una macchina virtuale in grado di usare dischi Ultra, per poter alleghiare un disco Ultra.

Sostituire o impostare le variabili **$VMName**, **$RgName**, **$DiskName**, **$location**, $password **$User con** valori personalizzati. **$password** Impostare **$zone** sul valore della zona di disponibilità ottenuta dall' [inizio di questo articolo](#determine-vm-size-and-region-availability). Eseguire quindi il comando dell'interfaccia della riga di comando seguente per creare una macchina virtuale ultra abilitata:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Abilitare la compatibilità del disco ultra in una macchina virtuale esistente

Se la macchina virtuale soddisfa i requisiti descritti nell' [ambito e nelle limitazioni di GA](#ga-scope-and-limitations) e si trova nell' [area appropriata per l'account](#determine-vm-size-and-region-availability), è possibile abilitare la compatibilità del disco Ultra nella macchina virtuale.

Per abilitare la compatibilità con dischi Ultra, è necessario arrestare la macchina virtuale. Dopo l'arresto della macchina virtuale, è possibile abilitare la compatibilità, alleghi un disco Ultra, quindi riavviare la macchina virtuale:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>Creare un disco Ultra usando l'interfaccia della riga di comando

Ora che si dispone di una macchina virtuale in grado di alleghire dischi Ultra, è possibile creare e collegarvi un disco Ultra.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

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

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

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
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Abilitare la compatibilità del disco ultra in una macchina virtuale esistente

Se la macchina virtuale soddisfa i requisiti descritti nell' [ambito e nelle limitazioni di GA](#ga-scope-and-limitations) e si trova nell' [area appropriata per l'account](#determine-vm-size-and-region-availability), è possibile abilitare la compatibilità del disco Ultra nella macchina virtuale.

Per abilitare la compatibilità con dischi Ultra, è necessario arrestare la macchina virtuale. Dopo l'arresto della macchina virtuale, è possibile abilitare la compatibilità, alleghi un disco Ultra, quindi riavviare la macchina virtuale:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
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
