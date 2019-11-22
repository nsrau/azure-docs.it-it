---
title: 'Esercitazione: Creare immagini di VM personalizzate con Azure PowerShell'
description: In questa esercitazione viene descritto come usare Azure PowerShell per creare un'immagine personalizzata della macchina virtuale in Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 906ac3b28a512a866e712cefda4355ad901c258d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74064710"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Esercitazione: Creare un'immagine personalizzata di una macchina virtuale di Azure con Azure PowerShell

Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. È possibile usare immagini personalizzate per eseguire il bootstrap delle distribuzioni e garantire la coerenza tra più VM. In questa esercitazione si crea un'immagine personalizzata di una macchina virtuale di Azure con PowerShell. Si apprenderà come:

> [!div class="checklist"]
> * Eseguire Sysprep e generalizzare le macchine virtuali
> * Creare un'immagine personalizzata
> * Creare una VM da un'immagine personalizzata
> * Elencare tutte le immagini nella sottoscrizione
> * Eliminare un'immagine

È disponibile in anteprima pubblica il servizio [generatore di immagini di VM di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview). È sufficiente descrivere le personalizzazioni in un modello, i passaggi per la creazione dell'immagine di questo articolo verranno gestiti dal servizio. [Provare il generatore di immagini di Azure (anteprima)](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder).

## <a name="before-you-begin"></a>Prima di iniziare

La procedura riportata di seguito illustra come prendere una VM esistente e convertirla in un'immagine personalizzata riutilizzabile che è possibile usare per creare nuove istanze di VM.

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, questo [script di esempio](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) può crearne una appositamente. Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle VM dove necessario.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="prepare-vm"></a>Preparare la VM

Per creare un'immagine di una macchina virtuale, è necessario preparare la VM di origine generalizzandola, eseguendone la deallocazione e quindi contrassegnandola come generalizzata con Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizzare la macchina virtuale Windows con Sysprep

Sysprep rimuove anche tutte le informazioni sull'account personale e prepara la VM da usare come immagine. Per altre informazioni su Sysprep, vedere [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Introduzione all'uso di Sysprep).


1. Connettersi alla macchina virtuale.
2. Aprire la finestra del prompt dei comandi come amministratore. Impostare la directory su *%windir%\system32\sysprep*, quindi eseguire `sysprep.exe`.
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.
4. In **Opzioni di arresto** selezionare **Arresta il sistema** e fare clic su **OK**.
5. Al termine, Sysprep arresta la macchina virtuale. **Non riavviare la VM**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Deallocare e contrassegnare la VM come generalizzata

Per creare un'immagine, la VM deve essere deallocata e contrassegnata come generalizzata in Azure.

Deallocare la VM con [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Impostare lo stato della macchina virtuale su `-Generalized` con [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>Creare l'immagine

A questo punto è possibile creare un'immagine della VM usando [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) e [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). Nell'esempio seguente viene creata un'immagine denominata *myImage* dalla VM denominata *myVM*.

Trovare la macchina virtuale. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

Creare la configurazione dell'immagine.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

Creare l'immagine.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Creare VM dall'immagine

Ora che si dispone di un'immagine, è possibile creare una o più nuove VM dall'immagine. La creazione di una macchina virtuale da un'immagine personalizzata è un'operazione simile alla creazione di una macchina virtuale con un'immagine del Marketplace. Quando si usa un'immagine del Marketplace, è necessario immettere le informazioni sull'immagine, il provider dell'immagine, l'offerta, lo SKU e la versione. Usando il set di parametri semplificato per il cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), è sufficiente specificare il nome dell'immagine personalizzata, a condizione che si trovi nello stesso gruppo di risorse. 

Questo esempio crea una VM denominata *myVMfromImage* dall'immagine *myImage* in *myResourceGroup*.


```azurepowershell-interactive
New-AzVm `
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

È consigliabile limitare il numero delle distribuzioni simultanee a 20 macchine virtuali per singola immagine. Se si pianificano distribuzioni simultanee su larga scala di più di 20 macchine virtuali dalla stessa immagine personalizzata, è consigliabile usare una [Raccolta immagini condivise](shared-image-galleries.md) con più repliche di immagini. 


## <a name="image-management"></a>Gestione delle immagini 

Di seguito sono riportati alcuni esempi di attività comuni di immagini gestite e della relativa modalità di completamento tramite PowerShell.

Elencare tutte le immagini per nome.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Eliminare un'immagine. Questo esempio elimina l'immagine denominata *myImage* da *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
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

Passare all'esercitazione successiva per scoprire come creare macchine virtuali a disponibilità elevata.

> [!div class="nextstepaction"]
> [Creare VM a disponibilità elevata](tutorial-availability-sets.md)



