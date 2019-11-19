---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5751ed33673ca859ba1aed54cfc7c2e7ecc8e495
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74124188"
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


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Distribuire un disco Ultra usando il portale di Azure

In questa sezione viene illustrata la distribuzione di una macchina virtuale dotata di disco rigido come disco dati. Si presuppone che l'utente abbia familiarità con la distribuzione di una macchina virtuale. in caso contrario, vedere [la Guida introduttiva: creare una macchina virtuale Windows nel portale di Azure](../articles/virtual-machines/windows/quick-create-portal.md).

- Accedere al [portale di Azure](https://portal.azure.com/) e passare a distribuire una macchina virtuale (VM).
- Assicurarsi di scegliere [le dimensioni e l'area della VM supportate](#ga-scope-and-limitations).
- Selezionare **zona di disponibilità** in **Opzioni di disponibilità**.
- Inserire le voci rimanenti con le selezioni preferite.
- Selezionare **Dischi**.

![Create-ultra-disk-Enabled-VM. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Nel pannello dischi selezionare **Sì** per **Abilita compatibilità dischi Ultra**.
- Selezionare **Crea e alleghi un nuovo disco** per alleghi ora un disco Ultra.

![Enable-and-Attach-ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Nel pannello **Crea un nuovo disco** immettere un nome, quindi selezionare **modifica dimensioni**.
- Modificare il **tipo di account** in **ultra disk**.
- Modificare i valori delle **dimensioni del disco personalizzato (GIB)** , **IOPS del disco**e la **velocità effettiva del disco** in uno di loro scelta.
- Selezionare **OK** in entrambi i pannelli.
- Continuare con la distribuzione della macchina virtuale, sarà uguale a quella di qualsiasi altra macchina virtuale.

![Create-ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Alleghi un disco Ultra usando il portale di Azure

In alternativa, se la macchina virtuale esistente si trova in un'area geografica o in una zona di disponibilità in grado di usare dischi Ultra, è possibile usare dischi Ultra senza dover creare una nuova macchina virtuale. Abilitando i dischi Ultra nella VM esistente, quindi li alleghi come dischi dati.

- Passare alla macchina virtuale e selezionare **dischi**.
- Selezionare **Modifica**.

![Options-Selector-ultra-Disks. png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Selezionare **Sì** per **Enable ultra disk Compatibility**.

![Ultra-Options-Yes-Enable. png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Selezionare **Salva**.
- Selezionare **Aggiungi disco dati** quindi nell'elenco a discesa per **nome** Selezionare **Crea disco**.

![Create-and-Attach-New-ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Immettere un nome per il nuovo disco e quindi selezionare **modifica dimensioni**.
- Modificare il **tipo di account** in **ultra disk**.
- Modificare i valori delle **dimensioni del disco personalizzato (GIB)** , **IOPS del disco**e la **velocità effettiva del disco** in uno di loro scelta.
- Selezionare **OK** , quindi selezionare **Crea**.

![Making-a-New-ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Dopo che si è tornati al pannello del disco, selezionare **Salva**.

![Saving-and-Attaching-New-ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Regolare le prestazioni di un disco Ultra usando il portale di Azure

I dischi Ultra offrono una funzionalità univoca che consente di regolare le prestazioni. Queste modifiche possono essere apportate dal portale di Azure sui dischi.

- Passare alla macchina virtuale e selezionare **dischi**.
- Selezionare il disco Ultra a cui si desidera modificare le prestazioni.

![Selecting-ultra-disk-to-Modify. png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Selezionare **configurazione** , quindi apportare le modifiche.
- Selezionare **Salva**.

![Configuring-ultra-Disk-Performance-and-size. png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

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
