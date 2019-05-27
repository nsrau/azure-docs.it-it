---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 326382339e2b4aeaa488d3d7f76b7ff35f9bc620
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147769"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Abilita e si distribuisce Azure SSDs extra (anteprima)

Azure extra unità velocità effettiva elevata dell'offerta (SSD) (anteprima), numero elevato di IOPS e di archiviazione su disco a bassa latenza coerente per le macchine virtuali IaaS di Azure (VM). Questa nuova offerta fornisce prestazioni all'avanguardia con gli stessi livelli di disponibilità delle offerte di dischi esistenti. Uno dei vantaggi principali di SSDs extra è la possibilità di modificare dinamicamente le prestazioni dell'unità SSD e i carichi di lavoro senza la necessità di riavviare le macchine virtuali. Unità SSD extra sono adatti per carichi di lavoro a elevato utilizzo di dati, ad esempio SAP HANA, i database di livello superiore e i carichi di lavoro con intensa attività di transazione.

Attualmente, SSDs extra sono disponibili in anteprima ed è necessario [registrare](https://aka.ms/UltraSSDPreviewSignUp) nel riquadro di anteprima per accedervi.

## <a name="determine-your-availability-zone"></a>Determinare la zona di disponibilità

Dopo l'approvazione, è necessario determinare quale zona di disponibilità in uso, per poter usare unità SSD extra. Eseguire i comandi seguenti per determinare quale zona negli Stati Uniti orientali 2 per la distribuzione del disco extra per:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

Interfaccia della riga di comando: `az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

La risposta sarà simile a quello riportato di seguito, dove X è la zona da utilizzare per la distribuzione negli Stati Uniti orientali 2. X può essere 1, 2 o 3.

Mantenere il **zone** valore, rappresenta la zona di disponibilità e sarà necessario per distribuire un'unità SSD extra.

|ResourceType  |NOME  |Località  |Zone  |Restrizione  |Capacità  |Value  |
|---------|---------|---------|---------|---------|---------|---------|
|dischi     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Se si è verificato alcun risposta dal comando, quindi la registrazione per la funzionalità è ancora in sospeso o non ancora approvati.

Ora che si conosce la zona in cui eseguire la distribuzione, seguire la procedura illustrata in questo articolo per distribuire le prime macchine virtuali con unità Ultra SSD.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Distribuire un'unità SSD extra tramite Azure Resource Manager

In primo luogo, determinare le dimensioni di macchina virtuale da distribuire. In questa anteprima sono supportate solo le famiglie di macchine virtuali DsV3 ed EsV3. Per altri dettagli su queste dimensioni di macchine virtuali, vedere la seconda tabella in questo [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Se si desidera creare una VM con più SSD extra, vedere l'esempio [creare una VM con più unità SSD extra](https://aka.ms/UltraSSDTemplate).

Se si prevede di usare un modello personalizzato, assicurarsi che **apiVersion** per `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` viene impostato come `2018-06-01` (o versione successiva).

Impostare lo sku di disco **UltraSSD_LRS**, quindi impostare la capacità, IOPS, zona di disponibilità e velocità effettiva in MBps per creare un disco extra.

Dopo aver effettuato il provisioning della macchina virtuale, è possibile partizionare e formattare i dischi dati e configurarli per i carichi di lavoro.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Distribuire un'unità SSD extra utilizzando CLI

In primo luogo, determinare le dimensioni di macchina virtuale da distribuire. In questa anteprima sono supportate solo le famiglie di macchine virtuali DsV3 ed EsV3. Per altri dettagli su queste dimensioni di macchine virtuali, vedere la seconda tabella in questo [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Per usare unità SSD extra, è necessario creare una macchina virtuale che è capace di usare unità SSD extra.

Sostituire o impostare il **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** variabili con i propri valori. Impostare **$zone** al valore della zona di disponibilità che è stato creato dalle [avvio di questo articolo](#determine-your-availability-zone). Eseguire quindi il comando seguente per creare una macchina virtuale abilitata extra:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Creare un'unità SSD extra utilizzando CLI

Dopo aver creato una macchina virtuale che è in grado di utilizzare Ultra-unità SSD, è possibile creare e collegare un'unità SSD extra.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Modificare le prestazioni di un'unità SSD extra utilizzando CLI

Unità SSD extra offrono una funzionalità univoca che consente di regolare le prestazioni, il comando seguente illustra come usare questa funzionalità:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Distribuire un'unità SSD extra usando PowerShell

In primo luogo, determinare le dimensioni di macchina virtuale da distribuire. In questa anteprima sono supportate solo le famiglie di macchine virtuali DsV3 ed EsV3. Per altri dettagli su queste dimensioni di macchine virtuali, vedere la seconda tabella in questo [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Per usare unità SSD extra, è necessario creare una macchina virtuale che è capace di usare unità SSD extra. Sostituire o impostare il **$resourcegroup** e **$vmName** variabili con i propri valori. Impostare **$zone** al valore della zona di disponibilità che è stato creato dalle [avvio di questo articolo](#determine-your-availability-zone). Quindi eseguire il comando seguente [New-AzVm](/powershell/module/az.compute/new-azvm) comando per creare un extra macchina virtuale abilitata:

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

### <a name="create-an-ultra-ssd-using-powershell"></a>Creare un'unità SSD extra usando PowerShell

Dopo aver creato una macchina virtuale che è in grado di utilizzare Ultra-unità SSD, è possibile creare e collegare un'unità SSD extra:

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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Modificare le prestazioni di un'unità SSD extra usando PowerShell

Ultra-unità SSD hanno una capacità univoca consente di regolare le prestazioni, il comando riportato di seguito è riportato un esempio che consente di regolare le prestazioni senza che sia necessario scollegare il disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Passaggi successivi

Se si vuole provare il nuovo tipo di disco [richiedere l'accesso all'anteprima con questo questionario](https://aka.ms/UltraSSDPreviewSignUp).