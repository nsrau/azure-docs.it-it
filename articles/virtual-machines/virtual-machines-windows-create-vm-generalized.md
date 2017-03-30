---
title: Creare una VM da un disco rigido virtuale generalizzato | Microsoft Docs
description: Informazioni su come creare una macchina virtuale Windows da un&quot;immagine di disco rigido virtuale generalizzato in un account di archiviazione usando Azure PowerShell.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: b4808871-9ef1-49ea-a617-9154d417abb0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 12832620d94226b6cfe391471c22fad2d1e3cf7e
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-vm-from-a-generalized-vhd-image-in-a-storage-account"></a>Creare una VM da un'immagine di disco rigido virtuale generalizzato in un account di archiviazione 

L'argomento descrive la creazione di una macchina virtuale da un disco non gestito generalizzato che si trova in un account di archiviazione. Tutte le informazioni sull'account personale sono state rimosse da un'immagine di disco rigido virtuale generalizzato mediante [Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). È possibile creare un disco rigido virtuale generalizzato eseguendo Sysprep in una VM locale e quindi [caricando il disco rigido virtuale in Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). In alternativa, è possibile eseguire Sysprep in una VM Azure esistente e quindi [copiare il disco rigido virtuale](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Se si vuole creare una VM da un disco rigido virtuale specializzato in un account di archiviazione, vedere [Creare una macchina virtuale da un disco specializzato](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per informazioni sull'uso di Managed Disks anziché di dischi in un account di archiviazione, vedere [Create a managed VM image](virtual-machines-windows-capture-image-resource.md) (Creare un'immagine gestita di una macchina virtuale) e [Create a VM from a managed image](virtual-machines-windows-create-vm-generalized-managed.md) (Creare una VM da un'immagine gestita).

## <a name="prerequisites"></a>Prerequisiti
Se si intende usare un disco rigido virtuale caricato da una VM locale, ad esempio una creata mediante Hyper-V, verificare di seguire le istruzioni riportate in [Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Prima di creare una VM usando questo metodo, è necessario generalizzare sia i dischi rigidi virtuali caricati che i dischi rigidi virtuali della VM di Azure esistenti. Per altre informazioni, vedere [Generalize a Windows virtual machine using Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Generalizzare una macchina virtuale Windows usando Sysprep). 

## <a name="set-the-uri-of-the-vhd"></a>Impostare l'URI del disco rigido virtuale

L'URI del disco rigido virtuale da usare è nel formato seguente: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. In questo esempio il disco rigido virtuale denominato **myVHD** si trova nell'account di archiviazione **mystorageaccount** del contenitore **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare la rete virtuale e la subnet della [rete virtuale](../virtual-network/virtual-networks-overview.md) stessa.

1. Creare la subnet. Nell'esempio seguente viene creata una subnet denominata **mySubnet** nel gruppo di risorse **myResourceGroup** con il prefisso di indirizzo **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Creare la rete virtuale. Nell'esempio seguente viene creata una rete virtuale denominata **myVnet** nell'ubicazione **West US** con il prefisso di indirizzo **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Creare un indirizzo IP pubblico e un'interfaccia di rete
Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Creare un indirizzo IP pubblico. In questo esempio viene creato un indirizzo IP pubblico denominato **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Creare la scheda NIC. In questo esempio viene creata una scheda NIC denominata **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creare il gruppo di sicurezza di rete e una regola RDP
Per essere in grado di accedere alla VM tramite RDP, è necessario disporre di una regola di sicurezza che consenta l'accesso RDP sulla porta 3389. 

In questo esempio viene creato un gruppo di sicurezza di rete denominato **myNsg** contenente una regola denominata **myRdpRule** che consente il traffico RDP sulla porta 3389. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Apertura di porte a una VM tramite PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Creare una variabile per la rete virtuale
Creare una variabile per la rete virtuale realizzata. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Creare la VM
Lo script di PowerShell seguente illustra come impostare le configurazioni della macchina virtuale e usare l'immagine della VM caricata come origine per la nuova installazione.

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Verificare che la VM sia stata creata
Al termine, la VM appena creata dovrebbe essere visualizzata nel [portale di Azure](https://portal.azure.com) in **Browse** (Sfoglia)  > **Macchine virtuali**. In alternativa, è possibile usare i comandi PowerShell seguenti:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passaggi successivi
Per gestire la nuova macchina virtuale con Azure PowerShell, vedere [Gestire macchine virtuali di Azure con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


