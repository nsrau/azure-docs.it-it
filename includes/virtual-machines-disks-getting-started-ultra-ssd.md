---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3f910a3d0466153bd60fe23ef2f9f656cac292ee
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919697"
---
# <a name="using-azure-ultra-disks"></a>Uso di Azure ultra Disks

Azure ultra Disks offre velocità effettiva elevata, IOPS elevate e archiviazione su disco a bassa latenza coerente per macchine virtuali IaaS di Azure. Questa nuova offerta fornisce prestazioni all'avanguardia con gli stessi livelli di disponibilità delle offerte di dischi esistenti. Uno dei vantaggi principali di ultra disks è la possibilità di modificare dinamicamente le prestazioni dell'unità SSD insieme ai carichi di lavoro senza dover riavviare le macchine virtuali. I dischi Ultra sono adatti per carichi di lavoro con utilizzo intensivo di dati, ad esempio SAP HANA, database di livello superiore e carichi di lavoro pesanti per le transazioni.

## <a name="check-if-your-subscription-has-access"></a>Controllare se la sottoscrizione ha accesso

Se è già stata effettuata l'iscrizione a ultra disks e si vuole verificare se la sottoscrizione è abilitata per i dischi Ultra, usare uno dei comandi seguenti: 

Interfaccia della riga di comando: `az feature show --namespace Microsoft.Compute --name UltraSSD`

PowerShell: `Get-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName UltraSSD`

Se la sottoscrizione è abilitata, l'output avrà un aspetto simile al seguente:

```bash
{
  "id": "/subscriptions/<yoursubID>/providers/Microsoft.Features/providers/Microsoft.Compute/features/UltraSSD",
  "name": "Microsoft.Compute/UltraSSD",
  "properties": {
    "state": "Registered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

## <a name="determine-your-availability-zone"></a>Determinare la zona di disponibilità

Una volta approvata, è necessario determinare la zona di disponibilità in cui si trova, per usare dischi Ultra. Eseguire uno dei comandi seguenti per determinare l'area in cui distribuire il disco Ultra, assicurarsi di sostituire prima i valori di **Region**, **vmSize**e **Subscription** :

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

La risposta sarà simile a quella riportata di seguito, dove X è la zona da usare per la distribuzione nell'area scelta. X può essere 1, 2 o 3. Attualmente, solo tre aree supportano dischi Ultra: Stati Uniti orientali 2, Asia sudorientale ed Europa settentrionale.

Mantenere il valore **Zones** , che rappresenta la zona di disponibilità e sarà necessario per distribuire un disco Ultra.

|ResourceType  |Name  |Location  |Zone  |Restrizione  |Capacità  |Value  |
|---------|---------|---------|---------|---------|---------|---------|
|dischi     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se non è stata trovata alcuna risposta dal comando, la registrazione alla funzionalità è ancora in sospeso oppure si sta usando una versione precedente dell'interfaccia della riga di comando o di PowerShell.

Ora che si conosce la zona in cui eseguire la distribuzione, seguire i passaggi di distribuzione in questo articolo per distribuire una macchina virtuale con un disco Ultra collegato o collegare un disco Ultra a una macchina virtuale esistente.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Distribuire un disco Ultra usando Azure Resource Manager

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per il momento, solo le famiglie di macchine virtuali DsV3 e EsV3 supportano dischi Ultra. Per altri dettagli su queste dimensioni di macchine virtuali, vedere la seconda tabella in questo [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Se si vuole creare una VM con più dischi Ultra, vedere l'esempio [creare una macchina virtuale con più dischi Ultra](https://aka.ms/ultradiskArmTemplate).

Se si intende usare un modello personalizzato, assicurarsi che **apiVersion** per `Microsoft.Compute/virtualMachines` `2018-06-01` e `Microsoft.Compute/Disks` sia impostato su (o versione successiva).

Impostare lo SKU del disco su **UltraSSD_LRS**, quindi impostare la capacità del disco, IOPS, la zona di disponibilità e la velocità effettiva in Mbps per creare un disco Ultra.

Dopo aver effettuato il provisioning della macchina virtuale, è possibile partizionare e formattare i dischi dati e configurarli per i carichi di lavoro.

## <a name="deploy-an-ultra-disk-using-cli"></a>Distribuire un disco Ultra usando l'interfaccia della riga di comando

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per il momento, solo le famiglie di macchine virtuali DsV3 e EsV3 supportano dischi Ultra. Per altri dettagli su queste dimensioni di macchine virtuali, vedere la seconda tabella in questo [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

È necessario creare una macchina virtuale in grado di usare dischi Ultra, per poter alleghiare un disco Ultra.

Sostituire o impostare le variabili **$VMName**, **$RgName**, **$DiskName**, **$location**, $password **$User con** valori personalizzati. Impostare **$zone** sul valore della zona di disponibilità ottenuta dall' [inizio di questo articolo](#determine-your-availability-zone). Eseguire quindi il comando dell'interfaccia della riga di comando seguente per creare una macchina virtuale ultra abilitata:

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

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per il momento, solo le famiglie di macchine virtuali DsV3 e EsV3 supportano dischi Ultra. Per altri dettagli su queste dimensioni di macchine virtuali, vedere la seconda tabella in questo [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Per usare dischi Ultra, è necessario creare una macchina virtuale in grado di usare dischi Ultra. Sostituire o impostare le variabili **$ResourceGroup** e **$vmName** con valori personalizzati. Impostare **$zone** sul valore della zona di disponibilità ottenuta dall' [inizio di questo articolo](#determine-your-availability-zone). Eseguire quindi il comando [New-AzVm](/powershell/module/az.compute/new-azvm) seguente per creare una macchina virtuale ultra abilitata:

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