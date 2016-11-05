---
title: Creare una VM che esegue Windows nel portale classico | Microsoft Docs
description: Creare una macchina virtuale di Windows nel portale di Azure classico.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Creare una macchina virtuale con Windows nel portale di Azure classico
> [!div class="op_single_selector"]
> * [Portale di Azure classico](virtual-machines-windows-classic-tutorial.md)
> * [PowerShell: distribuzione classica](virtual-machines-windows-classic-create-powershell.md)
> 
> 

<br>

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Informazioni su come [eseguire questa procedura con il modello di distribuzione di Resource Manager](virtual-machines-windows-hero-tutorial.md). Se si vuole usare il nuovo portale di Azure, vedere [Creare la prima macchina virtuale Windows nel portale di Azure](virtual-machines-windows-hero-tutorial.md).

Questa esercitazione illustra la facilità di creazione di una macchina virtuale (VM) di Azure che esegue Windows nel portale di Azure classico. Si utilizzerà un'immagine di Windows Server come esempio, ma questa è solo una delle molte immagini disponibili in Azure. Si noti che le opzioni dell’immagine dipendono dalla sottoscrizione. Ad esempio, i sottoscrittori MSDN possono usufruire di immagini desktop Windows.

Questa sezione illustra come usare l'opzione **Da raccolta** nel portale di gestione per la creazione di una macchina virtuale. Questa opzione offre un maggior numero di scelte di configurazione rispetto all'opzione **Creazione rapida** . Ad esempio, se si vuole aggiungere una macchina virtuale a una rete virtuale, sarà necessario usare l'opzione **Da raccolta** .

È inoltre possibile creare VM usando le [proprie immagini](virtual-machines-windows-classic-createupload-vhd.md). Per altre informazioni su questo e altri metodi, vedere [Diversi modi per creare una macchina virtuale Windows](virtual-machines-windows-creation-choices.md).

## <a name="video-walkthrough"></a>Procedura dettagliata video
Di seguito è riportata una procedura dettagliata di questa esercitazione.

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Creating-a-Windows-VM-on-Microsoft-Azure-GA-Portal/player]


## <a name="<a-id="createvirtualmachine">-</a>create-the-virtual-machine"></a><a id="createvirtualmachine"> </a>Creare la macchina virtuale
[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Passaggi successivi
* Accedere alla macchina virtuale. Per istruzioni, vedere [Accedere a una macchina virtuale di Windows tramite il portale di Azure classico](virtual-machines-windows-classic-connect-logon.md).
* Collegare un disco per archiviare i dati. È possibile collegare sia dischi vuoti sia dischi contenenti dati. Per istruzioni, vedere [Collegare un disco dati da una macchina virtuale di Windows creata con il modello di distribuzione classico](virtual-machines-windows-classic-attach-disk.md).

<!--HONumber=Oct16_HO2-->


