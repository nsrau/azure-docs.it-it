---
title: Creare un&quot;immagine non gestita di una macchina virtuale generalizzata in Azure | Microsoft Docs
description: "Creare un&quot;immagine non gestita di una macchina virtuale Windows generalizzata da usare per creare più copie di una macchina virtuale in Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 28fbe7d802a1258dd025b570a264b9306ba11dd6
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---

# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Come creare un'immagine di macchina virtuale non gestita da una macchina virtuale di Azure

In questo articolo illustra l'uso degli account di archiviazione. È consigliabile usare dischi e immagini anziché un account di archiviazione. Per altre informazioni, vedere [Acquisire un'immagine gestita di una macchina virtuale generalizzata in Azure](capture-image-resource.md).

Questo articolo illustra come usare Azure PowerShell per creare un'immagine di una macchina virtuale generalizzata di Azure con un account di archiviazione. È quindi possibile usare l'immagine per creare un'altra VM. Questa immagine include il disco del sistema operativo e i dischi dati collegati alla macchina virtuale. L'immagine non include le risorse della rete virtuale, quindi è necessario configurare queste risorse quando si crea la nuova VM. 

## <a name="prerequisites"></a>Prerequisiti
È necessario che sia installato Azure PowerShell 1.0.x o una versione più recente. Se non è già stato installato PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) per la procedura di installazione.

## <a name="generalize-the-vm"></a>Generalizzare la VM 
Questa sezione illustra come generalizzare la macchina virtuale di Windows da usare come immagine. Generalizzando una VM si rimuovono anche tutte le informazioni personali sull'account e si prepara la macchina da usare come immagine. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://technet.microsoft.com/library/bb457073.aspx).

Assicurarsi che i ruoli server in esecuzione sulla macchina siano supportati da Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se si carica il disco rigido virtuale in Azure per la prima volta, verificare di aver [preparato la macchina virtuale](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) prima di eseguire Sysprep. 
> 
> 

È possibile anche generalizzare una VM Linux tramite `sudo waagent -deprovision+user` e quindi usare PowerShell per acquisire la VM. Per informazioni sull'uso dell'interfaccia della riga di comando per acquisire una macchina virtuale, vedere [Come generalizzare e acquisire una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](../linux/capture-image.md).


1. Accedere alla macchina virtuale Windows.
2. Aprire la finestra del prompt dei comandi come amministratore. Impostare la directory su **%windir%\system32\sysprep**, quindi eseguire `sysprep.exe`.
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.
4. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.
5. Fare clic su **OK**.
   
    ![Avvio di Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Al termine, Sysprep arresta la macchina virtuale. 

> [!IMPORTANT]
> Non riavviare la macchina virtuale fino a quando non viene completato il caricamento del disco rigido virtuale in Azure o la creazione dell'immagine dalla macchina virtuale. Se la macchina virtuale viene riavviata accidentalmente, eseguire Sysprep per generalizzarla nuovamente.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Accedere ad Azure PowerShell
1. Aprire Azure PowerShell e accedere al proprio account di Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Verrà visualizzata una finestra popup in cui immettere le credenziali dell'account Azure.
2. Ottenere l'ID di sottoscrizione per le sottoscrizioni disponibili.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Impostare la sottoscrizione corretta utilizzandone l'ID.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Deallocare la VM e impostare lo stato generalizzato
1. Deallocare le risorse della VM.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Nel portale di Azure lo *stato* della VM passa da **Arrestato** ad **Arrestato (deallocato)**.
2. Impostare lo stato della macchina virtuale su **Generalizzato**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Controllare lo stato della VM. Nella sezione **OSState/generalized** per la VM, il valore **DisplayStatus** dovrebbe essere impostato su **Macchina virtuale generalizzata**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Creare l'immagine

Creare l'immagine della macchina virtuale non gestita nel contenitore di archiviazione di destinazione usando questo comando. L'immagine viene creata nello stesso account di archiviazione della macchina virtuale originale. Il parametro `-Path` salva una copia del modello JSON per la macchina virtuale di origine nel computer locale. Il parametro `-DestinationContainerName` è il nome del contenitore in cui si vuole salvare le immagini. Se il contenitore non esiste, verrà creato.
   
```powershell
Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
È possibile ottenere l'URL dell'immagine dal modello del file JSON. Passare alla sezione **resources** > **storageProfile** > **osDisk** > **image** > **uri** per il percorso completo dell'immagine. L'URL dell'immagine è simile a questo: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
È anche possibile verificare l'URI nel portale. L'immagine viene copiata in un contenitore denominato **system** nell'account di archiviazione. 

## <a name="create-a-vm-from-the-image"></a>Creare una macchina virtuale dall'immagine

A questo punto è possibile creare una o più macchine virtuali dall'immagine non gestita.

### <a name="set-the-uri-of-the-vhd"></a>Impostare l'URI del disco rigido virtuale

L'URI del disco rigido virtuale da usare è nel formato seguente: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. In questo esempio il disco rigido virtuale denominato **myVHD** si trova nell'account di archiviazione **mystorageaccount** del contenitore **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare la rete virtuale e la subnet della [rete virtuale](../../virtual-network/virtual-networks-overview.md) stessa.

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

### <a name="create-a-public-ip-address-and-network-interface"></a>Creare un indirizzo IP pubblico e un'interfaccia di rete
Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

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

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creare il gruppo di sicurezza di rete e una regola RDP
Per essere in grado di accedere alla VM tramite RDP, è necessario disporre di una regola di sicurezza che consenta l'accesso RDP sulla porta 3389. 

In questo esempio viene creato un gruppo di sicurezza di rete denominato **myNsg** contenente una regola denominata **myRdpRule** che consente il traffico RDP sulla porta 3389. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Apertura di porte a una VM tramite PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Creare una variabile per la rete virtuale
Creare una variabile per la rete virtuale realizzata. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Creare la VM
PowerShell riportato di seguito completa le configurazioni della macchina virtuale e usa l'immagine della non gestita come origine per la nuova installazione.

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

### <a name="verify-that-the-vm-was-created"></a>Verificare che la VM sia stata creata
Al termine, la VM appena creata dovrebbe essere visualizzata nel [portale di Azure](https://portal.azure.com) in **Browse** (Sfoglia)  > **Macchine virtuali**. In alternativa, è possibile usare i comandi PowerShell seguenti:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passaggi successivi
Per gestire la nuova macchina virtuale con Azure PowerShell, vedere [Gestire macchine virtuali di Azure con Azure Resource Manager e PowerShell](ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



