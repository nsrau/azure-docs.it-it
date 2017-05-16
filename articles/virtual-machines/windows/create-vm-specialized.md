---
title: Creare una macchina virtuale da un disco specializzato in Azure | Documentazione Microsoft
description: Creare una nuova macchina virtuale collegando un disco gestito o non gestito specializzato nel modello di distribuzione di Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 78f993ce9bab6266479cdd121eeea4965724d9bd
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-vm-from-a-specialized-disk"></a>Creare una macchina virtuale da un disco specializzato

Creare una nuova macchina virtuale collegando un disco specializzato come il disco del sistema operativo con Powershell. Un disco specializzato è una copia del disco rigido virtuale proveniente da una macchina virtuale esistente che gestisce gli account utente, le applicazioni e altri dati di stato dalla macchina virtuale originale. È possibile usare un [disco gestito](../../storage/storage-managed-disks-overview.md) o non gestito specializzato per creare la nuova macchina virtuale.

## <a name="before-you-begin"></a>Prima di iniziare
Se si usa PowerShell, verificare di avere la versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Per altre informazioni, vedere [Controllo delle versioni di Azure PowerShell](/powershell/azure/overview).


## <a name="create-the-subnet-and-vnet"></a>Creare la subNet e la vNet

Creare la rete virtuale e la subnet della [rete virtuale](../../virtual-network/virtual-networks-overview.md).

1. Creare la subnet. In questo esempio viene creata una subnet denominata **mySubNet** nel gruppo di risorse **myResourceGroup** e il prefisso dell'indirizzo della subnet viene impostato su **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Creare la rete virtuale. In questo esempio il nome della rete virtuale è **myVnetName**, la posizione specificata è **Stati Uniti occidentali** e il prefisso dell'indirizzo per la rete virtuale è **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>Creare un indirizzo IP pubblico e NIC
Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Creare l'IP pubblico. In questo esempio, il nome dell'indirizzo IP pubblico è **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Creare la scheda NIC. In questo esempio, il nome specificato della scheda NIC è **myNicName**.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creare il gruppo di sicurezza di rete e una regola RDP
Per essere in grado di accedere alla VM tramite RDP, è necessario disporre di una regola di sicurezza che consenta l'accesso RDP sulla porta 3389. Poiché il disco rigido virtuale per la nuova macchina virtuale è stato creato da una VM specializzata esistente, dopo l'avvenuta creazione della macchina virtuale è possibile usare un account esistente dalla VM di origine che aveva l'autorizzazione di accedere tramite RDP.
In questo esempio il nome NSG impostato è **myNsg**, mentre il nome della regola RDP è **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Per altre informazioni sugli endpoint e sulle regole NSG, vedere [Apertura di porte a una VM tramite PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="set-the-vm-name-and-size"></a>Impostare il nome e le dimensioni della macchina virtuale

In questo esempio il nome della macchina virtuale viene impostato su "myVM" e le dimensioni su "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>Aggiungere la scheda di interfaccia di rete
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>Configurare il disco del sistema operativo

Il sistema operativo specializzato potrebbe essere un disco rigido virtuale [caricato in Azure](upload-image.md) o una [copia del disco rigido virtuale da una macchina virtuale di Azure esistente](vhd-copy.md). 

È possibile scegliere una delle due opzioni:
- **Opzione 1**: creare un disco gestito specializzato da un disco rigido virtuale specializzato in un account di archiviazione esistente da usare come disco del sistema operativo.

oppure 

- **Opzione 2**: usare un disco rigido virtuale specializzato archiviato nel proprio account di archiviazione (un disco non gestito). 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>Opzione 1: Creare un disco gestito da un disco specializzato non gestito

1. Creare un disco gestito dal disco rigido virtuale specializzato esistente nell'account di archiviazione. Questo esempio usa **myOSDisk1** come nome del disco, inserisce il disco nell'archiviazione **StandardLRS** usa **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** come URI per il disco rigido virtuale di origine.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreateOption Import `
    -SourceUri https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd) `
    -ResourceGroupName $rgName
    ```

2. Aggiungere il disco del sistema operativo alla configurazione. In questo esempio le dimensioni del disco vengono impostate su **128 GB** e viene collegato il disco gestito come disco del sistema operativo **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

Facoltativo: collegare dischi gestiti aggiuntivi come dischi dati. Questa opzione presuppone che sia stato creato il disco dati gestito tramite [Create managed data disks](create-managed-disk-ps.md) (Creare dischi dati gestiti). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Opzione 2: collegare un disco rigido virtuale che si trova in un account di archiviazione esistente

1. Impostare l'URI per il disco rigido virtuale che si desidera usare. In questo esempio, il file del disco rigido virtuale denominato **myOsDisk.vhd** viene mantenuto in un account di archiviazione denominato **myStorageAccount** all'interno di un contenitore denominato **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Aggiungere il disco del sistema operativo usando l'URL del disco rigido virtuale del sistema operativo copiato. In questo esempio, quando viene creato il disco del sistema operativo, il termine "osDisk" viene collegato al nome della macchina virtuale per creare il nome del disco del sistema operativo. Questo esempio specifica anche che il disco rigido virtuale basato su Windows deve essere collegato alla macchina virtuale come disco del sistema operativo.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Facoltativo: se si dispone di dischi dati da associare alla macchina virtuale, aggiungere i dischi dati tramite gli URL dei dischi rigidi virtuali di dati e il numero di unità logica (LUN) appropriato.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Quando si usa un account di archiviazione, gli URL dei dischi dati e del sistema operativo sono simili al seguente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Per trovarlo nel portale, passare al contenitore di archiviazione di destinazione, fare clic sul disco rigido virtuale del sistema operativo o dei dati copiato e quindi copiare il contenuto dell'URL.


## <a name="create-the-vm"></a>Creare la VM

Creare la macchina virtuale usando le configurazioni appena create.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se il comando ha esito positivo, viene visualizzato un output simile al seguente:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>Verificare che la VM sia stata creata
La VM appena creata verrà visualizzata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **Macchine virtuali** oppure usando i comandi di PowerShell seguenti:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Passaggi successivi
Per accedere alla nuova macchina virtuale, passare alla VM nel [portale](https://portal.azure.com), fare clic su **Connetti**e aprire il file RDP di Desktop remoto. Usare le credenziali dell'account della macchina virtuale originale per accedere alla nuova macchina virtuale. Per altre informazioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](connect-logon.md).


