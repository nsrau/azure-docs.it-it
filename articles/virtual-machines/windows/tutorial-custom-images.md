---
title: 'Esercitazione: creare immagini personalizzate della macchina virtuale con Azure PowerShell | Microsoft Docs'
description: In questa esercitazione viene descritto come usare Azure PowerShell per creare un'immagine personalizzata della macchina virtuale in Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f2372382f36ba5a4ad801b4781bc7e04fa38694b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437607"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Esercitazione: Creare un'immagine personalizzata di una macchina virtuale di Azure con Azure PowerShell

Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. In questa esercitazione viene creata un'immagine personalizzata di una macchina virtuale di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Eseguire Sysprep e generalizzare le macchine virtuali
> * Creare un'immagine personalizzata
> * Creare una VM da un'immagine personalizzata
> * Elencare tutte le immagini nella sottoscrizione
> * Eliminare un'immagine

## <a name="before-you-begin"></a>Prima di iniziare

La procedura riportata di seguito illustra come prendere una VM esistente e convertirla in un'immagine personalizzata riutilizzabile che è possibile usare per creare nuove istanze di VM.

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, questo [script di esempio](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) può crearne una appositamente. Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle VM dove necessario.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo AzureRM versione 5.7.0 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="prepare-vm"></a>Preparare la VM

Per creare un'immagine di una macchina virtuale, è necessario preparare la VM generalizzandola, eseguendo la deallocazione e contrassegnando la VM di origine come generalizzata in Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizzare la macchina virtuale Windows con Sysprep

Sysprep rimuove anche tutte le informazioni sull'account personale e prepara la VM da usare come immagine. Per altre informazioni su Sysprep, vedere [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Introduzione all'uso di Sysprep).


1. Connettersi alla macchina virtuale.
2. Aprire la finestra del prompt dei comandi come amministratore. Cambiare la directory in *%windir%\system32\sysprep* e quindi eseguire *sysprep.exe*.
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare *Passare alla Configurazione guidata* e verificare che la casella di controllo *Generalizza* sia selezionata.
4. In **Opzioni di arresto** selezionare *Arresta il sistema* e fare clic su **OK**.
5. Al termine, Sysprep arresta la macchina virtuale. **Non riavviare la VM**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Deallocare e contrassegnare la VM come generalizzata

Per creare un'immagine, la VM deve essere deallocata e contrassegnata come generalizzata in Azure.

Deallocare la VM usando [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Impostare lo stato della macchina virtuale su `-Generalized` usando [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Creare l'immagine

A questo punto è possibile creare un'immagine della VM usando [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) e [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). Nell'esempio seguente viene creata un'immagine denominata *myImage* dalla VM denominata *myVM*.

Trovare la macchina virtuale. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Creare la configurazione dell'immagine.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Creare l'immagine.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Creare VM dall'immagine

Ora che si dispone di un'immagine, è possibile creare una o più nuove VM dall'immagine. La creazione di una macchina virtuale da un'immagine personalizzata è un'operazione simile alla creazione di una macchina virtuale con un'immagine del Marketplace. Quando si usa un'immagine del Marketplace, è necessario immettere le informazioni sull'immagine, il provider dell'immagine, l'offerta, lo SKU e la versione. Usando il parametro semplificato impostato per il cmdlet [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm), è sufficiente specificare il nome dell'immagine personalizzata, purché sia nello stesso gruppo di risorse. 

Questo esempio crea una VM denominata *myVMfromImage* dall'immagine denominata *myImage* in *myResourceGroup*.


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>Gestione delle immagini 

Di seguito sono riportati alcuni esempi di attività comuni di immagini gestite e della relativa modalità di completamento tramite PowerShell.

Elencare tutte le immagini per nome.

```azurepowershell-interactive
$images = Get-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Eliminare un'immagine. Questo esempio elimina l'immagine denominata *myImage* da *myResourceGroup*.

```azurepowershell-interactive
Remove-AzureRmImage `
    -ImageName myImage `
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



