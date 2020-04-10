---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfb094bc9f84e7129a3e1c733a054c5f6cd96372
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008637"
---
I dischi Ultra di Azure offrono velocità effettiva elevata, operazioni di I/O al secondo elevate e archiviazione costante su disco a bassa latenza per macchine virtuali (VM) di Azure IaaS. Questa nuova offerta fornisce prestazioni all'avanguardia con gli stessi livelli di disponibilità delle offerte di dischi esistenti. Uno dei principali vantaggi dei dischi ultra è la possibilità di modificare dinamicamente le prestazioni dell'unità SSD insieme ai carichi di lavoro senza la necessità di riavviare le macchine virtuali. I dischi Ultra sono idonei per carichi di lavoro a elevato utilizzo di dati, come SAP HANA, database di alto livello e carichi di lavoro con numerose transazioni.

## <a name="ga-scope-and-limitations"></a>Ambito GA e limitazioni

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Determinare le dimensioni della macchina virtuale e la disponibilità dell'area

### <a name="vms-using-availability-zones"></a>Macchine virtuali che usano zone di disponibilità

Per sfruttare i dischi ultra, è necessario determinare in quale zona di disponibilità ci si trova. Non tutte le aree supportano tutte le dimensioni delle macchine virtuali con dischi ultra. Per determinare se le dimensioni dell'area, dell'area e della macchina virtuale supportano dischi ultra, eseguire uno dei comandi seguenti, assicurarsi di sostituire prima i valori **di area**, **vmSize**e **sottoscrizione:**

#### <a name="cli"></a>CLI

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

La risposta sarà simile al modulo seguente, dove X è la zona da utilizzare per la distribuzione nella regione scelta. X può essere 1, 2 o 3.

Mantenere il valore **zone,** rappresenta la zona di disponibilità e sarà necessario per distribuire un disco Ultra.

|ResourceType  |Nome  |Location  |Zone  |Restrizione  |Funzionalità  |valore  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se non è stata ricevuta alcuna risposta dal comando, la dimensione della macchina virtuale selezionata non è supportata con dischi ultra nell'area selezionata.

Dopo aver conosciuto la zona in cui distribuire la distribuzione, seguire i passaggi di distribuzione descritti in questo articolo per distribuire una macchina virtuale con un disco ultra collegato o collegare un disco ultra a una macchina virtuale esistente.

### <a name="vms-with-no-redundancy-options"></a>Macchine virtuali senza opzioni di ridondanza

Per il momento, i dischi ultra distribuiti negli Stati Uniti occidentali devono essere distribuiti senza alcuna opzione di ridondanza. Tuttavia, non tutte le dimensioni del disco che supportano dischi ultra potrebbero trovarsi in quest'area. Per determinare quali negli Stati Uniti occidentali supportano ultra dischi, è possibile utilizzare uno dei frammenti di codice seguenti. Assicurarsi di `vmSize` sostituire `subscription` prima i valori e:

```azurecli
$subscription = "<yourSubID>"
$region = "westus"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

La risposta sarà simile al `UltraSSDAvailable   True` formato seguente, indica se la dimensione della macchina virtuale supporta ultra dischi in questa area.

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

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Distribuire un disco ultra usando Azure Resource ManagerDeploy an ultra disk using Azure Resource Manager

Determinare innanzitutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle macchine virtuali supportate, vedere la sezione [Ambito ga e limitazioni.](#ga-scope-and-limitations)

Se si desidera creare una macchina virtuale con più dischi ultra, fare riferimento all'esempio [Create a VM with multiple ultra disks](https://aka.ms/ultradiskArmTemplate).

Se si intende utilizzare un modello personalizzato, `Microsoft.Compute/virtualMachines` assicurarsi che `2018-06-01` **apiVersion** per e `Microsoft.Compute/Disks` sia impostato su (o versione successiva).

Impostare lo sku del disco su **UltraSSD_LRS**, quindi impostare la capacità del disco, le operazioni di I/O al secondo, la zona di disponibilità e la velocità effettiva in MBps per creare un disco ultra.

Dopo aver effettuato il provisioning della macchina virtuale, è possibile partizionare e formattare i dischi dati e configurarli per i carichi di lavoro.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Distribuire un disco ultra tramite il portale di AzureDeploy an ultra disk using the Azure portal

In questa sezione viene illustrata la distribuzione di una macchina virtuale dotata di un disco ultra come disco dati. Si presuppone che l'utente abbia familiarità con la distribuzione di una macchina virtuale, in caso contrario, vedere la Guida [introduttiva: Creare una macchina virtuale Windows nel portale](../articles/virtual-machines/windows/quick-create-portal.md)di Azure.

- Accedere al [portale](https://portal.azure.com/) di Azure e passare alla distribuzione di una macchina virtuale (VM).
- Assicurarsi di scegliere le [dimensioni e l'area della macchina virtuale supportate.](#ga-scope-and-limitations)
- Selezionare **zona Disponibilità** in **Opzioni disponibilità**.
- Compilare le voci rimanenti con le selezioni di propria scelta.
- Selezionare **Dischi**.

![create-ultra-disk-enabled-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Nel pannello Dischi selezionare **Sì** per **Abilita compatibilità disco ultra-**.
- Selezionare **Crea e collega un nuovo disco** per collegare un disco ultra.

![enable-and-attach-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Nel pannello **Crea un nuovo disco** immettere un nome, quindi selezionare Cambia **dimensione**.
- Modificare il **tipo di account** in Disco **ultra**.
- Modificare i valori di **Dimensioni disco personalizzate (GiB),** **Operazioni di I/O**al secondo e **Velocità effettiva del disco** in base a quelli desiderati.
- Selezionare **OK** in entrambe le lame.
- Continuare con la distribuzione della macchina virtuale, sarà lo stesso che si distribuirebbe qualsiasi altra macchina virtuale.

![create-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Collegare un disco ultra tramite il portale di AzureAttach an ultra disk using the Azure portal

In alternativa, se la macchina virtuale esistente si trova in una zona di area/disponibilità in grado di usare dischi ultra, è possibile usare dischi ultra senza dover creare una nuova macchina virtuale. Abilitando gli ultradischi nella macchina virtuale esistente, quindi collegandoli come dischi dati.

- Passare alla macchina virtuale e selezionare **Dischi**.
- Selezionare **Modifica**.

![options-selector-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Selezionare **Sì** per **Abilita compatibilità disco ultra .**

![ultra-options-yes-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Selezionare **Salva**.
- Selezionare **Aggiungi disco dati,** quindi nell'elenco a discesa **Nome** selezionare **Crea disco**.

![create-and-attach-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Immettere un nome per il nuovo disco, quindi selezionare **Cambia dimensione**.
- Modificare il **tipo di account** in Disco **ultra**.
- Modificare i valori di **Dimensioni disco personalizzate (GiB),** **Operazioni di I/O**al secondo e **Velocità effettiva del disco** in base a quelli desiderati.
- Selezionare **OK,** quindi **Crea**.

![making-a-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Dopo essere stati restituiti al pannello del disco, selezionare **Salva**.

![salvataggio e collegamento di nuovi ultra-disco.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Regolare le prestazioni di un disco ultra usando il portale di AzureAdjust the performance of an ultra disk using the Azure portal

I dischi Ultra offrono una funzionalità unica che consente di regolare le loro prestazioni. È possibile apportare queste modifiche dal portale di Azure sui dischi stessi.

- Passare alla macchina virtuale e selezionare **Dischi**.
- Selezionare l'ultradisco di cui si desidera modificare le prestazioni.

![selezione di ultra-disco da modificare.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Selezionare **Configurazione** e apportare le modifiche desiderate.
- Selezionare **Salva**.

![configurazione di ultra-disk-performance-and-size.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Distribuire un disco ultra usando CLIDeploy an ultra disk using CLI

Determinare innanzitutto le dimensioni della macchina virtuale da distribuire. Vedere la sezione [Ambito ga e limitazioni](#ga-scope-and-limitations) per un elenco delle dimensioni di VM supportate.

È necessario creare una macchina virtuale in grado di usare dischi ultra per collegare un disco ultra.

Sostituire o impostare le variabili **$vmname**, **$rgname**, **$rgname , $diskname** **$location**, **$password** **$user** $password con valori personalizzati. Impostare **$zone** sul valore della zona di disponibilità ottenuto dall'inizio [di questo articolo.](#determine-vm-size-and-region-availability) Eseguire quindi il comando CLI seguente per creare una macchina virtuale ultra abilitata:Then run the following CLI command to create an ultra enabled VM:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Abilitare la compatibilità del disco ultra in una macchina virtuale esistenteEnable ultra disk compatibility on an existing VM

Se la macchina virtuale soddisfa i requisiti descritti [nell'ambito GA e le limitazioni](#ga-scope-and-limitations) e si trova nell'area appropriata per l'account, è possibile abilitare la compatibilità del disco ultra nella macchina virtuale. [appropriate zone for your account](#determine-vm-size-and-region-availability)

Per abilitare la compatibilità con disco ultra, è necessario arrestare la macchina virtuale. Dopo aver arrestato la macchina virtuale, è possibile abilitare la compatibilità, collegare un disco ultra e riavviare la macchina virtuale:After you stop the VM, you may enable compatibility, attach an ultra disk, then restart the VM:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>Creare un disco ultra utilizzando CLICreate an ultra disk using CLI

Ora che si dispone di una macchina virtuale in grado di collegare dischi ultra, è possibile creare e collegare un disco ultra.

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Collegare un disco ultra a una macchina virtuale usando l'interfaccia della riga di comandoAttach an ultra disk to a VM using CLI

In alternativa, se la macchina virtuale esistente si trova in una zona di area/disponibilità in grado di usare dischi ultra, è possibile usare dischi ultra senza dover creare una nuova macchina virtuale.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Regolare le prestazioni di un disco ultra utilizzando CLI

I dischi ultra offrono una funzionalità unica che consente di regolare le loro prestazioni, il seguente comando descrive come utilizzare questa funzione:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Distribuire un disco ultra tramite PowerShellDeploy an ultra disk using PowerShell

Determinare innanzitutto le dimensioni della macchina virtuale da distribuire. Vedere la sezione [Ambito ga e limitazioni](#ga-scope-and-limitations) per un elenco delle dimensioni di VM supportate.

Per usare dischi ultra, è necessario creare una macchina virtuale in grado di usare dischi ultra. Sostituire o impostare le variabili **$resourcegroup** e **$vmName** con valori personalizzati. Impostare **$zone** sul valore della zona di disponibilità ottenuto dall'inizio [di questo articolo.](#determine-vm-size-and-region-availability) Eseguire quindi il comando New-AzVm seguente per creare una macchina virtuale ultraabilitata:Then run the following [New-AzVm](/powershell/module/az.compute/new-azvm) command to create an ultra enabled VM:

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

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Abilitare la compatibilità del disco ultra in una macchina virtuale esistenteEnable ultra disk compatibility on an existing VM

Se la macchina virtuale soddisfa i requisiti descritti [nell'ambito GA e le limitazioni](#ga-scope-and-limitations) e si trova nell'area appropriata per l'account, è possibile abilitare la compatibilità del disco ultra nella macchina virtuale. [appropriate zone for your account](#determine-vm-size-and-region-availability)

Per abilitare la compatibilità con disco ultra, è necessario arrestare la macchina virtuale. Dopo aver arrestato la macchina virtuale, è possibile abilitare la compatibilità, collegare un disco ultra e riavviare la macchina virtuale:After you stop the VM, you may enable compatibility, attach an ultra disk, then restart the VM:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>Creare un disco ultra usando PowerShellCreate an ultra disk using PowerShell

Ora che si dispone di una macchina virtuale in grado di usare dischi ultra, è possibile creare e collegare un disco ultra:Now that you have a VM that is capable of using ultra disks, you can create and attach an ultra disk to it:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Collegare un disco ultra a una macchina virtuale usando PowerShellAttach an ultra disk to a VM using PowerShell

In alternativa, se la macchina virtuale esistente si trova in una zona di area/disponibilità in grado di usare dischi ultra, è possibile usare dischi ultra senza dover creare una nuova macchina virtuale.

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Regolare le prestazioni di un disco ultra usando PowerShellAdjust the performance of an ultra disk using PowerShell

I dischi Ultra hanno una capacità unica che consente di regolare le loro prestazioni, il comando seguente è un esempio che regola le prestazioni senza dover scollegare il disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```