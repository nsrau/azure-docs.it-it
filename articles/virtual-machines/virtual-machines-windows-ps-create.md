---
title: Creare una VM di Azure usando PowerShell | Microsoft Docs
description: Usare Azure PowerShell e Azure Resource Manager per creare facilmente una nuova VM che esegue Windows Server.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 819b40302f158d1d6224878c164cf7ff71947887


---
# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Creare una VM Windows con Resource Manager e PowerShell
Questo articolo illustra come creare rapidamente una macchina virtuale di Azure che esegue Windows Server e le risorse necessarie usando [Resource Manager](../azure-resource-manager/resource-group-overview.md) e PowerShell. 

Per creare una macchina virtuale sono necessari tutti i passaggi di questo articolo e l'esecuzione di queste operazioni richiede circa 30 minuti. Sostituire i valori dei parametri di esempio nei comandi con nomi significativi per l'ambiente di riferimento.

## <a name="step-1-install-azure-powershell"></a>Passaggio 1: installare Azure PowerShell
Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

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

## <a name="step-3-create-a-storage-account"></a>Passaggio 3: Creare un account di archiviazione
È necessario un [account di archiviazione](../storage/storage-introduction.md) per archiviare il disco rigido virtuale usato dalla macchina virtuale creata. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.

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
Ora che è tutto pronto, è possibile creare la macchina virtuale.

1. Eseguire il comando per impostare il nome account e la password amministratore per la macchina virtuale.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    La password deve avere una lunghezza compresa tra 12 e 123 caratteri e includere almeno una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. 
2. Creare l'oggetto di configurazione per la macchina virtuale. Questo comando crea un oggetto di configurazione denominato **myVmConfig** che definisce il nome e le dimensioni della VM.
   
    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
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
   
    Per altre informazioni sulla scelta delle immagini da usare, vedere [Esplorare e selezionare immagini di macchine virtuali Windows in Azure con PowerShell o l'interfaccia della riga di comando](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
5. Aggiungere l'interfaccia di rete creata alla configurazione.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
6. Definire nome e percorso del disco rigido della VM. Il file del disco rigido virtuale è archiviato in un contenitore. Questo comando consente di creare il disco in un contenitore denominato **vhds/myOsDisk1.vhd** nell'account di archiviazione creato.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
7. Aggiungere le informazioni sul disco del sistema operativo alla configurazione della VM. Sostituire il valore di **$diskName** con un nome per il disco del sistema operativo. Creare la variabile e aggiungere le informazioni sul disco alla configurazione.
   
    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
8. Creare infine la macchina virtuale.
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>Passaggi successivi
* Se si sono verificati problemi con la distribuzione, è consigliabile vedere come [risolvere i problemi della distribuzione di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)
* Per informazioni su come gestire la macchina virtuale creata, vedere [Gestire macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per usare un modello per creare una macchina virtuale, vedere le informazioni contenute nell'articolo [Creare una macchina virtuale Windows con un modello di Resource Manager](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Dec16_HO2-->


