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
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 57fbab4194f6cd232e1462ecea9a07d104c6cb51
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205741"
---
# <a name="create-a-vm-from-a-managed-image"></a>Creare una macchina virtuale da un'immagine gestita

È possibile creare più macchine virtuali da un'immagine di macchina virtuale gestita di Azure tramite il portale di Azure o PowerShell. Un'immagine di macchina virtuale gestita contiene le informazioni necessarie per creare una macchina virtuale, inclusi i dischi dati e del sistema operativo. I dischi rigidi virtuali (VHD) che costituiscono l'immagine, inclusi i dischi del sistema operativo e qualsiasi disco dati, vengono archiviati come dischi gestiti. 

Prima di creare una nuova macchina virtuale, dovrai [creare un'immagine di macchina virtuale gestita](capture-image-resource.md) da usare come immagine di origine e concedere l'accesso in lettura sull'immagine per tutti gli utenti devono avere accesso all'immagine. 


## <a name="use-the-portal"></a>Usare il portale

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare **Tutte le risorse**. È possibile ordinare le risorse per **tipo** per individuare facilmente le immagini.
3. Selezionare l'immagine che si intende usare dall'elenco. Si apre la pagina **Panoramica** delle immagini.
4. Scegliere **Crea macchina virtuale** dal menu.
5. Immettere le informazioni relative alla macchina virtuale. Il nome utente e la password immessi in questa posizione verranno usati per accedere alla macchina virtuale. Al termine, selezionare **OK**. È possibile creare la nuova macchina virtuale in un gruppo di risorse esistente o scegliere **Crea nuovo** per creare un nuovo gruppo di risorse per archiviare la macchina virtuale.
6. Selezionare una dimensione per la VM. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Supported disk type** (Tipo di disco supportato). 
7. In **impostazioni** apportare le modifiche in base alle esigenze e selezionare **OK**. 
8. Nella pagina di riepilogo dovrebbe essere visualizzato il nome dell'immagine indicato come **Immagine privata** nell'elenco. Selezionare **OK** per avviare la distribuzione della macchina virtuale.


## <a name="use-powershell"></a>Usare PowerShell

È possibile usare PowerShell per creare una macchina virtuale da un'immagine usando il set di parametri semplificato per il cmdlet [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). L'immagine deve trovarsi nello stesso gruppo di risorse in cui verrà creata la macchina virtuale.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Il set di parametri semplificato per [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) richiede solo di specificare nome, gruppo di risorse e nome per l'immagine da cui creare una macchina virtuale. New-AzVm userà il valore del parametro **-Name** come nome di tutte le risorse create automaticamente. In questo esempio, vengono forniti nomi più dettagliati per ogni risorsa, ma consentendo al cmdlet di crearli automaticamente. È anche possibile creare in anticipo le risorse, ad esempio la rete virtuale, e passare il nome della risorsa nel cmdlet. Se è possibile trovarle in base al nome, New-AzVm userà le risorse esistenti.

Nell'esempio seguente, viene creata una VM denominata *myVMFromImage* nel gruppo di risorse *myResourceGroup* dall’immagine chiamata *myImage*. 


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



## <a name="next-steps"></a>Passaggi successivi
[Creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

