---
title: Creare una macchina virtuale da un'immagine gestita in Azure | Microsoft Docs
description: Creare una macchina virtuale Windows da un'immagine gestita generalizzata usando Azure PowerShell o il portale Azure nel modello di distribuzione Gestione risorse.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 1d543bd9590664e74cff70cf55e8f7bd42f2c6f0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239056"
---
# <a name="create-a-vm-from-a-managed-image"></a>Creare una macchina virtuale da un'immagine gestita

È possibile creare più macchine virtuali da un'immagine gestita tramite PowerShell o il portale Azure. Un'immagine di macchina virtuale gestita contiene le informazioni necessarie per creare una macchina virtuale, inclusi i dischi dati e del sistema operativo. I dischi rigidi virtuali che costituiscono l'immagine, inclusi i dischi del sistema operativo e qualsiasi disco dati, vengono archiviati come dischi gestiti. 

È necessario aver già [creato un'immagine di macchina virtuale gestita](capture-image-resource.md) da usare per creare la nuova macchina virtuale. 

## <a name="use-the-portal"></a>Usare il portale

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare **Tutte le risorse**. È possibile ordinare le risorse per **tipo** per individuare facilmente le immagini.
3. Selezionare l'immagine che si intende usare dall'elenco. Si apre la pagina **Panoramica** delle immagini.
4. Fare clic su **+ Crea VM** dal menu.
5. Immettere le informazioni relative alla macchina virtuale. Il nome utente e la password immessi in questo modulo verranno usati per accedere alla macchina virtuale. Al termine fare clic su **OK**. È possibile creare la nuova macchina virtuale in un gruppo di risorse esistente o scegliere **Crea nuovo** per creare un nuovo gruppo di risorse per archiviare la macchina virtuale.
6. Selezionare una dimensione per la VM. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Supported disk type** (Tipo di disco supportato). 
7. In **impostazioni**, apportare le modifiche in base alle esigenze e fare clic su **OK**. 
8. Nella pagina del riepilogo dovrebbe essere visualizzato il nome dell'immagine nell'elenco **Immagine privata**. Fare clic su **OK** per avviare la distribuzione della macchina virtuale.


## <a name="use-powershell"></a>Usare PowerShell

È possibile utilizzare PowerShell per creare una macchina virtuale da un'immagine utilizzando il parametro semplificato impostato per il [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) cmdlet. L'immagine deve trovarsi nello stesso gruppo di risorse in cui si desidera creare la macchina virtuale.

Questo esempio richiede il modulo AzureRM 5.6.0 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

Il parametro semplificato impostato per New-AzureRmVm richiede solo di fornire un nome, un nome per il gruppo di risorse e il nome per l'immagine da cui creare una macchina virtuale, ma verrà utilizzato il valore del parametro del **-Nome** per denominare tutte le risorse che crea automaticamente. In questo esempio, vengono forniti nomi più dettagliati per ogni risorsa, ma consentendo al cmdlet di crearli automaticamente. È anche possibile creare risorse, come la rete virtuale, in anticipo, e passare il nome nel cmdlet. Se è possibile trovarle in base al nome, userà le risorse esistenti.

Nell'esempio seguente, viene creata una VM denominata *myVMFromImage* nel gruppo di risorse *myResourceGroup* dall’immagine chiamata *myImage*. 


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



## <a name="next-steps"></a>Passaggi successivi
Per gestire la nuova macchina virtuale con Azure PowerShell, vedere [Creare e gestire VM di Windows con il modulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

