---
title: Creare una macchina virtuale Windows da un disco rigido virtuale specializzato in Azure
description: Creare una nuova macchina virtuale Windows collegando un disco gestito specializzato come disco del sistema operativo usando il modello di distribuzione Resource Manager.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: ac18056f9bfdf22c55b5effac810b8c24ab4d81d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033849"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Creare una macchina virtuale Windows da un disco specializzato usando PowerShell

Creare una nuova macchina virtuale collegando un disco gestito specializzato come disco del sistema operativo. Un disco specializzato è una copia di un disco rigido virtuale proveniente da una macchina virtuale esistente che contiene gli account utente, le applicazioni e altri dati di stato dalla macchina virtuale originale. 

Quando si usa un disco rigido virtuale specializzato per creare una nuova VM, la nuova VM mantiene il nome computer della VM originale. Vengono mantenute anche altre informazioni specifiche del computer e, in alcuni casi, queste informazioni duplicate possono causare problemi. Quando si copia una macchina virtuale, tenere presente quali tipi di informazioni specifiche del computer vengono usate dalle applicazioni.

Sono disponibili diverse opzioni:
* [Usare un disco gestito esistente](#option-1-use-an-existing-disk). Questa opzione è utile se la macchina virtuale disponibile non funziona correttamente. È possibile eliminare la macchina virtuale e quindi riutilizzare il disco gestito per creare una nuova macchina virtuale. 
* [Caricare un VHD](#option-2-upload-a-specialized-vhd) 
* [Copiare una macchina virtuale di Azure esistente usando gli snapshot](#option-3-copy-an-existing-azure-vm)

È anche possibile usare il portale di Azure per [creare una nuova macchina virtuale da un disco rigido virtuale specializzato](create-vm-specialized-portal.md).

Questo articolo illustra come usare i dischi gestiti. Se è presente una distribuzione legacy che richiede l'uso di un account di archiviazione, vedere [Creare una VM da un disco rigido virtuale specializzato in un account di archiviazione](sa-create-vm-specialized.md).

È consigliabile limitare il numero di distribuzioni simultanee a 20 VM da un singolo disco rigido virtuale o snapshot. 

## <a name="option-1-use-an-existing-disk"></a>Opzione 1: Usare un disco esistente

Se una macchina virtuale è stata eliminata e si vuole riutilizzare il disco del sistema operativo per crearne una nuova, usare [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Sarà quindi possibile collegare questo disco come disco del sistema operativo a una [nuova macchina virtuale](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opzione 2: Caricare un disco rigido virtuale specializzato

È possibile caricare il disco rigido virtuale da una VM specializzata creata con uno strumento di virtualizzazione locale, ad esempio Hyper-V, o da una VM esportata da un altro cloud.

### <a name="prepare-the-vm"></a>Preparare la macchina virtuale
Usare il disco rigido virtuale così com'è per creare una nuova macchina virtuale. 
  
  * [Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Non** generalizzare la macchina Virtuale usando Sysprep.
  * Rimuovere tutti gli strumenti di virtualizzazione guest e gli agenti installati nella macchina virtuale, ad esempio gli strumenti VMware.
  * Verificare che la macchina virtuale sia configurata per ottenere l'indirizzo IP e le impostazioni DNS da DHCP. In questo modo il server ottiene un indirizzo IP all'interno della rete virtuale all'avvio. 


### <a name="upload-the-vhd"></a>Caricare il disco rigido virtuale

È ora possibile caricare un disco rigido virtuale direttamente in un disco gestito. Per istruzioni, vedere [caricare un disco rigido virtuale in Azure usando Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Opzione 3: Copiare una macchina virtuale di Azure esistente

È possibile creare una copia di una macchina virtuale che usa dischi gestiti acquisendo uno snapshot della macchina virtuale e quindi usando tale snapshot per creare un nuovo disco gestito e una nuova macchina virtuale.

Se si vuole copiare una macchina virtuale esistente in un'altra area, potrebbe essere necessario usare azcopy per [creare una copia di un disco in un'altra area](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Acquisire uno snapshot del disco del sistema operativo

È possibile acquisire uno snapshot di un'intera macchina virtuale (tutti i dischi inclusi) o solo di un singolo disco. I passaggi seguenti illustrano come acquisire uno snapshot del disco del sistema operativo della macchina virtuale con il cmdlet [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot). 

Per prima cosa, impostare alcuni parametri. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Ottenere l'oggetto macchina virtuale.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Ottenere il nome del disco del sistema operativo.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Creare la configurazione dello snapshot. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Ottenere lo snapshot.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Per usare questo snapshot per creare una macchina virtuale che deve essere a prestazioni elevate, aggiungere il parametro `-AccountType Premium_LRS` al comando New-AzSnapshotConfig. Questo parametro crea lo snapshot in modo tale che venga archiviato come un disco gestito Premium. Poiché i dischi gestiti Premium sono più costosi di quelli Standard, assicurarsi che il disco Premium sia effettivamente necessario prima di usare questo parametro.

### <a name="create-a-new-disk-from-the-snapshot"></a>Creare un nuovo disco dallo snapshot

Creare un disco gestito dallo snapshot usando [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Questo esempio usa *myOSDisk* come nome del disco.

Creare un nuovo gruppo di risorse per la nuova VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Impostare il nome del disco del sistema operativo. 

```powershell
$osDiskName = 'myOsDisk'
```

Creare il disco gestito.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Creare la nuova VM 

Creare le risorse di rete e le altre risorse da usare nella nuova VM.

### <a name="create-the-subnet-and-virtual-network"></a>Creare la subnet e la rete virtuale

Creare la [rete virtuale](../../virtual-network/virtual-networks-overview.md) e la subnet per la macchina virtuale.

1. Creare la subnet. In questo esempio viene creata una subnet denominata *mySubNet* nel gruppo di risorse *myDestinationResourceGroup* e il prefisso dell'indirizzo della subnet viene impostato su *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Creare la rete virtuale. In questo esempio il nome della rete virtuale è *myVnetName*, la località specificata è *Stati Uniti occidentali* e il prefisso dell'indirizzo per la rete virtuale è *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creare il gruppo di sicurezza di rete e una regola RDP
Per poter accedere alla macchina virtuale con RDP (Remote Desktop Protocol), sarà necessario avere una regola di sicurezza che consenta l'accesso RDP sulla porta 3389. Nell'esempio il disco rigido virtuale per la nuova macchina virtuale è stato creato da una macchina virtuale specializzata esistente, quindi è possibile usare un account esistente nella macchina virtuale di origine per RDP.

In questo esempio il nome del gruppo di sicurezza di rete impostato è *myNsg*, mentre il nome della regola RDP è *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Per altre informazioni sugli endpoint e sulle regole dei gruppi di sicurezza di rete, vedere [Apertura di porte in una macchina virtuale tramite PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Creare un indirizzo IP pubblico e NIC
Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, saranno necessari un [indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Creare l'IP pubblico. In questo esempio, il nome dell'indirizzo IP pubblico è *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Creare la scheda NIC. In questo esempio, il nome specificato della scheda NIC è *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Impostare il nome e le dimensioni della macchina virtuale

Questo esempio imposta il nome della macchina virtuale su *myVM* e le dimensioni su *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Aggiungere la scheda di interfaccia di rete
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Aggiungere il disco del sistema operativo 

Aggiungere il disco del sistema operativo alla configurazione usando [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). In questo esempio le dimensioni del disco vengono impostate su *128 GB* e viene collegato il disco gestito come disco del sistema operativo *Windows*.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Completare la VM 

Creare la macchina virtuale usando [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) con le configurazioni appena create.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Se il comando ha esito positivo, viene visualizzato un output simile al seguente:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verificare che la VM sia stata creata
La macchina virtuale appena creata verrà visualizzata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **Macchine virtuali** oppure usando i comandi di PowerShell seguenti.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Passaggi successivi
Accedere alla nuova macchina virtuale. Per altre informazioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](connect-logon.md).

