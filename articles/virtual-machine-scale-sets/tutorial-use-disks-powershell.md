---
title: Esercitazione - Creare e usare dischi per set di scalabilità con Azure PowerShell | Microsoft Docs
description: Informazioni su come usare Azure PowerShell per creare e usare dischi gestiti con set di scalabilità di macchine virtuali e come aggiungere, preparare, elencare e rimuovere dischi.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6035a6ddd690db456edfa5777ca2d41e4be8b919
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728590"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>Esercitazione: Creare e usare dischi con un set di scalabilità di macchine virtuali con Azure PowerShell

I set di scalabilità di macchine virtuali usano dischi per archiviare il sistema operativo, le applicazioni e i dati dell'istanza di macchina virtuale. Quando si crea e si gestisce un set di scalabilità, è importante scegliere le dimensioni del disco e la configurazione appropriate per il carico di lavoro previsto. Questa esercitazione illustra la creazione e la gestione dei dischi di VM. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Dischi del sistema operativo e dischi temporanei
> * Dischi dati
> * Dischi Standard e Premium
> * Prestazioni dei dischi
> * Collegare e preparare i dischi dati

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="default-azure-disks"></a>Dischi di Azure predefiniti
Quando si crea o si ridimensiona un set di scalabilità, vengono automaticamente collegati due dischi a ogni istanza di macchina virtuale. 

**Disco del sistema operativo**: i dischi del sistema operativo possono essere ridimensionati fino a 2 TB e ospitano il sistema operativo dell'istanza di macchina virtuale. Il disco del sistema operativo viene etichettato come */dev/sda* per impostazione predefinita. La configurazione della memorizzazione nella cache del disco del sistema operativo è ottimizzata per le prestazioni del sistema operativo. A causa di questa configurazione, il disco del sistema operativo **non deve** ospitare applicazioni o i dati. Per le applicazioni e i dati, usare un disco dati, descritto in dettaglio più avanti in questo articolo. 

**Disco temporaneo**: i dischi temporanei usano un'unità SSD che si trova nello stesso host di Azure dell'istanza di macchina virtuale. Si tratta di dischi ad alte prestazioni e possono essere usati per operazioni quali l'elaborazione dei dati temporanei. Se tuttavia l'istanza di macchina virtuale viene spostata in un nuovo host, tutti i dati archiviati in un disco temporaneo verranno rimossi. Le dimensioni del disco temporaneo sono determinate dalle dimensioni dell'istanza di macchina virtuale. I dischi temporanei vengono etichettati come */dev/sdb* e hanno un punto di montaggio */mnt*.

### <a name="temporary-disk-sizes"></a>Dimensioni del disco temporaneo
| Type | Dimensioni comuni | Dimensioni massime del disco temporaneo (GiB) |
|----|----|----|
| [Utilizzo generico](../virtual-machines/windows/sizes-general.md) | Serie A, B e D | 1600 |
| [Ottimizzate per il calcolo](../virtual-machines/windows/sizes-compute.md) | Serie F | 576 |
| [Ottimizzate per la memoria](../virtual-machines/windows/sizes-memory.md) | Serie D, E, G e M | 6144 |
| [Ottimizzate per l'archiviazione](../virtual-machines/windows/sizes-storage.md) | Serie L | 5630 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | Serie N | 1\.440 |
| [Prestazioni elevate](../virtual-machines/windows/sizes-hpc.md) | Serie A e H | 2000 |


## <a name="azure-data-disks"></a>Dischi dati di Azure
È possibile aggiungere altri dischi dati se è necessario installare applicazioni e archiviare dati. I dischi dati devono essere usati in qualsiasi situazione in cui si desidera un'archiviazione dei dati durevoli e reattiva. Ogni disco dati ha una capacità massima di 4 TB. Le dimensioni dell'istanza di macchina virtuale determinano il numero di dischi dati che possono essere collegati. Per ogni vCPU della macchina virtuale, è possibile collegare due dischi dati.

### <a name="max-data-disks-per-vm"></a>Numero massimo di dischi di dati per macchina virtuale
| Type | Dimensioni comuni | Numero massimo di dischi di dati per macchina virtuale |
|----|----|----|
| [Utilizzo generico](../virtual-machines/windows/sizes-general.md) | Serie A, B e D | 64 |
| [Ottimizzate per il calcolo](../virtual-machines/windows/sizes-compute.md) | Serie F | 64 |
| [Ottimizzate per la memoria](../virtual-machines/windows/sizes-memory.md) | Serie D, E, G e M | 64 |
| [Ottimizzate per l'archiviazione](../virtual-machines/windows/sizes-storage.md) | Serie L | 64 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | Serie N | 64 |
| [Prestazioni elevate](../virtual-machines/windows/sizes-hpc.md) | Serie A e H | 64 |


## <a name="vm-disk-types"></a>Tipi di dischi per la VM
Azure offre due tipi di dischi.

### <a name="standard-disk"></a>Disco standard
Archiviazione Standard è supportato da unità disco rigido e offre un'archiviazione conveniente con buone prestazioni. I dischi standard sono ideali per un carico di lavoro di test e sviluppo conveniente.

### <a name="premium-disk"></a>Disco premium
I dischi Premium sono supportati da dischi a bassa latenza e ad alte prestazioni basati su SSD. Questi dischi sono consigliati per le VM che eseguono carichi di lavoro di produzione. L'archiviazione premium supporta le macchine virtuali serie DS, DSv2, GS e FS. Quando si selezionano le dimensioni del disco, il valore viene arrotondato per eccesso al tipo successivo. Ad esempio, se le dimensioni del disco non superano i 128 GB, il tipo di disco è P10. Se le dimensioni del disco vanno da 129 a 512 GB, il tipo sarà un P20. Un valore superiore a 512 GB determina un tipo di disco P30.

### <a name="premium-disk-performance"></a>Prestazioni disco premium
|Tipo di disco di Archiviazione Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Dimensioni del disco (arrotondate) | 32 GB | 64 GB | 128 GB | 512 GB | 1\.024 GB (1 TB) | 2\.048 GB (2 TB) | 4\.095 GB (4 TB) |
| Operazioni IOPS al secondo max per disco | 120 | 240 | 500 | 2\.300 | 5\.000 | 7\.500 | 7\.500 |
Velocità effettiva per disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Sebbene la tabella sopra riportata identifichi il numero massimo di operazioni di I/O al secondo per disco, è possibile raggiungere un livello superiore di prestazioni tramite lo striping di più dischi di dati. Ad esempio, una macchina virtuale Standard_GS5 può raggiungere un massimo di 80.000 operazioni di I/O al secondo. Per informazioni dettagliate sul numero massimo di operazioni di I/O al secondo per macchina virtuale, vedere [Dimensioni delle macchine virtuali Windows](../virtual-machines/windows/sizes.md).


## <a name="create-and-attach-disks"></a>Creare e collegare dischi
È possibile creare e collegare i dischi quando si crea un set di scalabilità oppure con un set di scalabilità esistente.

### <a name="attach-disks-at-scale-set-creation"></a>Collegare dischi al momento della creazione del set di scalabilità
Creare un set di scalabilità di macchine virtuali con [New-AzVmss](/powershell/module/az.compute/new-azvmss). Quando richiesto, immettere un nome utente e una password per le istanze di macchina virtuale. Per distribuire il traffico alle singole istanze di macchine virtuali, viene creato anche un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico include regole per la distribuzione del traffico sulla porta TCP 80, oltre che per consentire il traffico di Desktop remoto sulla porta TCP 3389 e la comunicazione remota di PowerShell sulla porta TCP 5985.

Vengono creati due dischi con il parametro `-DataDiskSizeGb`. Il primo disco è da *64* GB, il secondo è da *128* GB. Quando richiesto, fornire le proprie credenziali amministrative desiderate per le istanze di macchina virtuale nel set di scalabilità:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

La creazione e la configurazione di tutte le risorse e le istanze di VM del set di scalabilità richiedono alcuni minuti.

### <a name="attach-a-disk-to-existing-scale-set"></a>Collegare un disco a un set di scalabilità esistente
È anche possibile collegare dischi a un set di scalabilità esistente. Usare il set di scalabilità creato nel passaggio precedente per aggiungere un altro disco con [Add-AzVmssDataDisk](/powershell/module/az.compute/add-azvmssdatadisk). L'esempio seguente collega un altro disco dati da *128* GB a un set di scalabilità esistente:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>Preparare i dischi dati
I dischi che vengono creati e collegati a istanze di VM del set di scalabilità non sono formattati. Prima di usarli con i dati e le applicazioni, è necessario prepararli. Per preparare i dischi, creare una partizione e un file system, quindi montare i dischi.

Per automatizzare il processo in più istanze di macchina virtuale di un set di scalabilità, è possibile usare l'estensione Script personalizzato di Azure. Questa estensione può eseguire script in locale in ogni istanza di macchina virtuale, ad esempio per preparare i dischi dati collegati. Per altre informazioni, vedere [Panoramica dell'estensione script personalizzata](../virtual-machines/windows/extensions-customscript.md).


Questo esempio esegue uno script da un repository GitHub di esempio in ogni istanza di VM con [Add-AzVmssExtension](/powershell/module/az.compute/Add-AzVmssExtension), per preparare tutti i dischi dati non formattati collegati:


```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Per confermare che i dischi siano stati preparati correttamente, usare RDP per connettersi a una delle istanze di macchina virtuale. 

Recuperare per prima cosa l'oggetto servizio di bilanciamento del carico con [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Visualizzare quindi le regole NAT in ingresso con [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig). Le regole NAT elencano il valore *FrontendPort* per ogni istanza di macchina virtuale in cui RDP è in ascolto. Ottenere infine l'indirizzo IP pubblico del servizio di bilanciamento del carico con [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

Per connettersi alla VM, specificare l'indirizzo IP pubblico e il numero di porta dell'istanza di VM richiesta, in base a quanto visualizzato dai comandi precedenti. Quando richiesto, immettere le credenziali usate durante la creazione del set di scalabilità. Se si usa Azure Cloud Shell, eseguire questo passaggio da un prompt di PowerShell in locale o da un client Desktop remoto. L'esempio seguente stabilisce la connessione all'istanza di VM *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Aprire una sessione di PowerShell locale nell'istanza di macchina virtuale ed esaminare i dischi connessi con [Get-Disk](/powershell/module/storage/get-disk):

```powershell
Get-Disk
```

L'output di esempio seguente mostra che sono collegati tre dischi dati all'istanza di macchina virtuale.

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

Esaminare i filesystem e i punti di montaggio nell'istanza di macchina virtuale come indicato di seguito:

```powershell
Get-Partition
```

L'output di esempio seguente mostra che ai tre dischi dati sono assegnate lettere di unità:

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

I dischi in ogni istanza di macchina virtuale nel set di scalabilità vengono preparati automaticamente nello stesso modo. Dato il possibile aumento delle prestazioni del set di scalabilità, i dischi dati necessari vengono collegati alle nuove istanze di macchina virtuale. Viene anche eseguita l'estensione Script personalizzato per preparare automaticamente i dischi.

Chiudere la connessione Desktop remoto con l'istanza di macchina virtuale.


## <a name="list-attached-disks"></a>Elencare i dischi collegati
Per visualizzare informazioni sui dischi collegati a un set di scalabilità, usare [Get-AzVmss](/powershell/module/az.compute/get-azvmss) nel modo seguente:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

Sotto la proprietà *VirtualMachineProfile.StorageProfile* viene visualizzato l'elenco di *DataDisks*. Vengono visualizzate informazioni su dimensioni del disco, livello di archiviazione e LUN (numero di unità logica). L'output di esempio seguente visualizza i dettagli dei tre dischi dati collegati al set di scalabilità:

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>Scollegare un disco
Quando un disco non è più necessario, è possibile rimuoverlo dal set di scalabilità. Il disco viene rimosso da tutte le istanze di macchina virtuale presenti nel set di scalabilità. Per rimuovere un disco da un set di scalabilità, usare [Remove-AzVmssDataDisk](/powershell/module/az.compute/remove-azvmssdatadisk) e specificare il LUN del disco. I LUN vengono visualizzati nell'output di [Get-AzVmss](/powershell/module/az.compute/get-azvmss) nella sezione precedente. Nell'esempio seguente viene rimosso il LUN *3* dal set di scalabilità:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>Pulire le risorse
Per rimuovere il set di scalabilità e i dischi, eliminare il gruppo di risorse e tutte le relative risorse con [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Il parametro `-Force` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo. Il parametro `-AsJob` restituisce il controllo al prompt senza attendere il completamento dell'operazione.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come creare e usare dischi con i set di scalabilità con Azure PowerShell:

> [!div class="checklist"]
> * Dischi del sistema operativo e dischi temporanei
> * Dischi dati
> * Dischi Standard e Premium
> * Prestazioni dei dischi
> * Collegare e preparare i dischi dati

Passare all'esercitazione successiva per informazioni su come usare un'immagine personalizzata per le istanze di macchina virtuale del set di scalabilità.

> [!div class="nextstepaction"]
> [Usare un'immagine personalizzata per le istanze di macchina virtuale del set di scalabilità](tutorial-use-custom-image-powershell.md)
