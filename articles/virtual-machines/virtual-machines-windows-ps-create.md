---
title: Creare una VM di Azure usando PowerShell | Microsoft Docs
description: Usare Azure PowerShell e Azure Resource Manager per creare facilmente una VM che esegue Windows Server.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.topic: get-started-article
ms.date: 02/14/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 8d8dfb9b165d82e8567f6b5577d46d562f9f8db3
ms.openlocfilehash: 89e306d3e3312531878da088575c7429a941d34f
ms.lasthandoff: 02/23/2017

---

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Creare una VM Windows con Resource Manager e PowerShell

Questo articolo illustra come creare rapidamente una macchina virtuale di Azure che esegue Windows Server e le risorse necessarie usando [Resource Manager](../azure-resource-manager/resource-group-overview.md) e Azure PowerShell.  

Per creare una macchina virtuale sono necessari tutti i passaggi di questo articolo e l'esecuzione di queste operazioni richiede circa 30 minuti. Sostituire i valori dei parametri di esempio nei comandi con nomi significativi per l'ambiente di riferimento.

## <a name="step-1-install-azure-powershell"></a>Passaggio 1: installare Azure PowerShell

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Per utilizzare la funzionalità descritta in questo articolo potrebbe essere necessario reinstallare Azure PowerShell. Le funzionalità dei dischi gestiti presentano la versione 3.5 e versioni successive.
> 
> 

## <a name="step-2-create-a-resource-group"></a>Passaggio 2: Creare un gruppo di risorse

Tutte le risorse devono essere contenute in un gruppo di risorse, che deve pertanto essere creato per primo.  

1. Ottenere un elenco di località disponibili in cui possono essere create le risorse.
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. Impostare il percorso per le risorse. Questo comando imposta il percorso su **centralus**.
   
    ```powershell
    $location = "centralus"
    ```

3. Creare un gruppo di risorse. Questo comando crea il gruppo di risorse denominato **myResourceGroup** nel percorso impostato.
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-optional-create-a-storage-account"></a>Passaggio 3: (facoltativo) Creare un account di archiviazione

È attualmente possibile usare [Azure Managed Disks](../storage/storage-managed-disks-overview.md) o i dischi non gestiti quando si crea una macchina virtuale. Se si sceglie di usare un disco non gestito, è necessario creare un [account di archiviazione](../storage/storage-introduction.md) per archiviare il disco rigido virtuale usato dalla macchina virtuale creata. Se si sceglie di usare un disco gestito, l'account di archiviazione non è necessario. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.

1. Verificare l'univocità del nome dell'account di archiviazione. Questo comando verifica il nome **myStorageAccount**.
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    Se questo comando restituisce **True**, il nome proposto è univoco in Azure. 

2. Creare quindi l'account di archiviazione.
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>Passaggio 4: Creare una rete virtuale

Tutte le macchine virtuali fanno parte di una [rete virtuale](../virtual-network/virtual-networks-overview.md).

1. Creare una subnet per la rete virtuale. Questo comando crea una subnet denominata **mySubnet** con un prefisso di indirizzo di 10.0.0.0/24.
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```

2. Creare quindi la rete virtuale. Questo comando crea una rete virtuale denominata **myVnet** tramite la subnet creata e un prefisso di indirizzo di **10.0.0.0/16**.
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Passaggio 5: Creare un indirizzo IP pubblico e un'interfaccia di rete

Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Creare l'indirizzo IP pubblico. Questo comando crea un indirizzo IP pubblico denominato **myPublicIp** con un metodo di allocazione **Dynamic**.
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. Creare l'interfaccia di rete. Questo comando crea un'interfaccia di rete denominata **myNIC**.
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>Passaggio 6: Creare una macchina virtuale

Ora che è tutto pronto, è possibile creare la macchina virtuale. È possibile creare una macchina virtuale usando un'[immagine del Marketplace](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), un'[immagine generalizzata personalizzata (con sysprep)](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o un'[immagine specializzata personalizzata (senza sysprep)](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Questo esempio usa un'immagine di Windows Server dal Marketplace. 

1. Eseguire il comando per impostare il nome account e la password amministratore per la macchina virtuale.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    La password deve avere una lunghezza compresa tra 12 e 123 caratteri e includere almeno una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale.

2. Creare l'oggetto di configurazione per la macchina virtuale. Questo comando crea un oggetto di configurazione denominato **myVmConfig** che definisce il nome e le dimensioni della VM.
   
    ```powershell
    $myVM = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    Per un elenco di dimensioni disponibili per una macchina virtuale, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

3. Configurare le impostazioni del sistema operativo per la VM. Questo comando imposta il nome del computer, il tipo di sistema operativo e le credenziali dell'account per la VM.
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Definire l'immagine da usare per il provisioning della VM. Questo comando definisce l'immagine di Windows Server da usare per la VM. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```

5. Aggiungere l'interfaccia di rete creata alla configurazione.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```

6. Se si usa un disco non gestito, eseguire questo comando per definire il nome e la posizione del disco rigido della macchina virtuale. In caso contrario, ignorare questo passaggio. Il file del disco rigido virtuale per un disco non gestito è archiviato in un contenitore. Questo comando consente di creare il disco in un contenitore denominato **vhds/myOsDisk1.vhd** nell'account di archiviazione creato.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```

7. Aggiungere le informazioni sul disco del sistema operativo alla configurazione della VM. Questo comando crea un disco denominato **myOsDisk1**.
   
    Se si usa un disco gestito, eseguire questo comando per impostare il disco del sistema operativo nella configurazione:

    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
    ```

    Se si usa un disco non gestito, eseguire questo comando per impostare il disco del sistema operativo nella configurazione:

    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```

8. Creare infine la macchina virtuale.
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>Passaggi successivi

* Se si sono verificati problemi con la distribuzione, è consigliabile vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
* Per informazioni su come gestire la macchina virtuale creata, vedere [Gestire macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per usare un modello per creare una macchina virtuale, vedere le informazioni contenute nell'articolo [Creare una macchina virtuale Windows con un modello di Resource Manager](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


