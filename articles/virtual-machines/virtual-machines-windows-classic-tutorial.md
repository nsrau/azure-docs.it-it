---
title: Creare una VM nel portale classico | Microsoft Docs
description: Creare una macchina virtuale di Windows nel portale di Azure classico.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: ede78ff35ec6e7007e7a1c7b946c8e17bd425f37


---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Creare una macchina virtuale con Windows nel portale di Azure classico
> [!div class="op_single_selector"]
> * [Portale di Azure classico](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [PowerShell: distribuzione classica](virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br>

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come [eseguire questa procedura con il modello di distribuzione Resource Manager](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) tramite il **nuovo portale di Azure**. 

In questa esercitazione viene illustrato come creare una macchina virtuale (VM) di Azure che esegue Windows nel portale di Azure classico. Si utilizzerà un'immagine di Windows Server come esempio, ma questa è solo una delle molte immagini disponibili in Azure. Si noti che le opzioni dell’immagine dipendono dalla sottoscrizione. Ad esempio, i sottoscrittori MSDN possono usufruire di immagini desktop Windows.

Questa sezione illustra come usare l'opzione **Da raccolta** nel portale di gestione per la creazione di una macchina virtuale. Questa opzione offre un maggior numero di scelte di configurazione rispetto all'opzione **Creazione rapida** . Ad esempio, se si vuole aggiungere una macchina virtuale a una rete virtuale, sarà necessario usare l'opzione **Da raccolta** .

È inoltre possibile creare VM usando le [proprie immagini](virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Per altre informazioni su questo e altri metodi, vedere [Diversi modi per creare una macchina virtuale Windows](virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="video-walkthrough"></a>Procedura dettagliata video
Di seguito è riportata una procedura dettagliata di questa esercitazione.

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Creating-a-Windows-VM-on-Microsoft-Azure-GA-Portal/player]


## <a id="createvirtualmachine"> </a>Creare la macchina virtuale
[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [creare una VM con il modello di distribuzione Resource Manager](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nel nuovo portale di Azure. 
* Accedere alla macchina virtuale. Per istruzioni, vedere [Accedere a una macchina virtuale di Windows tramite il portale di Azure classico](virtual-machines-windows-classic-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Collegare un disco per archiviare i dati. È possibile collegare sia dischi vuoti sia dischi contenenti dati. Per istruzioni, vedere [Collegare un disco dati da una macchina virtuale di Windows creata con il modello di distribuzione classico](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Dec16_HO1-->


