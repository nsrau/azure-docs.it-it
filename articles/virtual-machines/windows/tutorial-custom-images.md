---
title: Creare immagini di VM personalizzate con Azure PowerShell | Documentazione Microsoft
description: 'Esercitazione: creare un&quot;immagine di VM personalizzata con Azure PowerShell.'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 773b37ec8f775d68f1faca0d252f3064c7de0317
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Creare un'immagine personalizzata di una VM di Azure con PowerShell

Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. In questa esercitazione viene creata un'immagine personalizzata di una macchina virtuale di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Eseguire Sysprep e generalizzare le macchine virtuali
> * Creare un'immagine personalizzata
> * Creare una VM da un'immagine personalizzata
> * Elencare tutte le immagini nella sottoscrizione
> * Eliminare un'immagine

Questa esercitazione richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Prima di iniziare

La procedura riportata di seguito illustra come prendere una VM esistente e convertirla in un'immagine personalizzata riutilizzabile che è possibile usare per creare nuove istanze di VM.

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, questo [script di esempio](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) può crearne una appositamente. Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle VM dove necessario.

## <a name="prepare-vm"></a>Preparare la VM

Per creare un'immagine di una macchina virtuale, è necessario preparare la VM generalizzandola, eseguendo la deallocazione e contrassegnando la VM di origine come generalizzata in Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizzare la macchina virtuale Windows con Sysprep

Sysprep rimuove anche tutte le informazioni sull'account personale e prepara la VM da usare come immagine. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://technet.microsoft.com/library/bb457073.aspx).


1. Connettersi alla macchina virtuale.
2. Aprire la finestra del prompt dei comandi come amministratore. Cambiare la directory in *%windir%\system32\sysprep* e quindi eseguire *sysprep.exe*.
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare *Passare alla Configurazione guidata* e verificare che la casella di controllo *Generalizza* sia selezionata.
4. In **Opzioni di arresto** selezionare *Arresta il sistema* e fare clic su **OK**.
5. Al termine, Sysprep arresta la macchina virtuale. **Non riavviare la VM**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Deallocare e contrassegnare la VM come generalizzata

Per creare un'immagine, la VM deve essere deallocata e contrassegnata come generalizzata in Azure.

Deallocare la VM usando [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Force
```

Impostare lo stato della macchina virtuale su `-Generalized` usando [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Generalized
```


## <a name="create-the-image"></a>Creare l'immagine

A questo punto è possibile creare un'immagine della VM usando [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) e [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). Nell'esempio seguente viene creata un'immagine denominata *myImage* dalla VM denominata *myVM*.

Trovare la macchina virtuale. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroupImages
```

Creare la configurazione dell'immagine.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Creare l'immagine.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroupImages
```    

 
## <a name="create-vms-from-the-image"></a>Creare VM dall'immagine

Ora che si dispone di un'immagine, è possibile creare una o più nuove VM dall'immagine. Creare una VM da un'immagine personalizzata è molto simile a creare una VM usando un'immagine del Marketplace. Quando si usa un'immagine del Marketplace, è necessario fornire informazioni sull'immagine, il provider dell'immagine, l'offerta, lo SKU e la versione. Con un'immagine personalizzata è sufficiente fornire l'ID della risorsa immagine personalizzata. 

Nello script seguente si crea la variabile *$image* per memorizzare le informazioni sull'immagine personalizzata usando [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage) e quindi si usa [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) e si specifica l'ID mediante la variabile *$image* appena creata. 

Lo script crea una VM denominata *myVMfromImage* dall'immagine personalizzata in un nuovo gruppo di risorse denominato *myResourceGroupFromImage* nella posizione *Stati Uniti occidentali*.


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroupImages

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="image-management"></a>Gestione delle immagini 

Di seguito sono riportati alcuni esempi di attività comuni di gestione delle immagini e della relativa modalità di completamento tramite PowerShell.

Elencare tutte le immagini per nome.

```powershell
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Eliminare un'immagine. Questo esempio elimina l'immagine denominata *myOldImage* da *myResourceGroup*.

```powershell
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'immagine di macchina virtuale personalizzata. Si è appreso come:

> [!div class="checklist"]
> * Eseguire Sysprep e generalizzare le macchine virtuali
> * Creare un'immagine personalizzata
> * Creare una VM da un'immagine personalizzata
> * Elencare tutte le immagini nella sottoscrizione
> * Eliminare un'immagine

Passare all'esercitazione successiva per la descrizione delle macchine virtuali a disponibilità elevata.

> [!div class="nextstepaction"]
> [Creare VM a disponibilità elevata](tutorial-availability-sets.md)




