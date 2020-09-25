---
title: Creare immagini di macchina virtuale per il dispositivo GPU Azure Stack Edge Pro
description: Descrive come creare immagini di macchina virtuale Linux o Windows da usare con il dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/04/2020
ms.author: alkohli
ms.openlocfilehash: 745d0df07b6d0d01acf0d564df8c242e16f3f56d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90890987"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>Creare immagini personalizzate di macchina virtuale per il dispositivo Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Per distribuire macchine virtuali nel dispositivo Azure Stack Edge Pro, è necessario poter creare immagini personalizzate utilizzabili per creare macchine virtuali. Questo articolo descrive i passaggi necessari per creare immagini personalizzate di macchine virtuali Linux o Windows utilizzabili per distribuire macchine virtuali nel dispositivo Azure Stack Edge Pro.

## <a name="vm-image-workflow"></a>Flusso di lavoro delle immagini di VM

Il flusso di lavoro richiede la creazione di una macchina virtuale in Azure, la personalizzazione della VM, la generalizzazione e quindi il download del disco rigido virtuale corrispondente a tale VM. Questo disco rigido virtuale generalizzato viene caricato in Azure Stack Edge Pro, da questo disco viene creato un disco gestito da cui viene creata un'immagine che infine viene usata per creare le macchine virtuali.   

Per altre informazioni, vedere [Distribuire una macchina virtuale nel dispositivo Azure Stack Edge Pro con Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Creare un'immagine personalizzata di VM Windows

Seguire questa procedura per creare un'immagine di VM Windows.

1. Creare una macchina virtuale Windows. Per altre informazioni, vedere l'[Esercitazione: Creare e gestire VM Windows con Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Scaricare un disco del sistema operativo esistente.

    - Seguire la procedura descritta in [Scaricare un disco rigido virtuale](../virtual-machines/windows/download-vhd.md).

    - Usare il comando `sysprep` seguente invece di quello indicato nella procedura precedente.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       È anche possibile vedere [Panoramica di Sysprep (preparazione sistema)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Usare questo disco rigido virtuale per creare e distribuire una macchina virtuale nel dispositivo Azure Stack Edge Pro.

## <a name="create-a-linux-custom-vm-image"></a>Creare un'immagine personalizzata di VM Linux

Seguire questa procedura per creare un'immagine di VM Linux.

1. Creare una macchina virtuale Linux. Per altre informazioni, vedere l'[Esercitazione: Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure](../virtual-machines/linux/tutorial-manage-vm.md).

1. Eseguire il deprovisioning della VM. Usare l'agente della macchina virtuale di Azure per eliminare file e dati specifici della macchina. Usare il comando `waagent` con il parametro `-deprovision+user` nella macchina virtuale Linux di origine. Per altre informazioni, vedere [Informazioni e uso dell'agente Linux di Azure](../virtual-machines/extensions/agent-linux.md).

    1. Connettersi alla macchina virtuale Linux con un client SSH.
    2. Nella finestra SSH digitare il comando seguente:
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > Eseguire questo comando solo su una macchina virtuale che si intende acquisire come immagine. Questo comando non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili o che l'immagine sia adatta per la ridistribuzione. Il parametro `+user` rimuove anche l'ultimo account utente di cui è stato effettuato il provisioning. Per mantenere le credenziali dell'account utente nella macchina virtuale, usare solo `-deprovision`.
     
    3. Immettere **y** per continuare. È possibile aggiungere il parametro `-force` per evitare questo passaggio di conferma.
    4. Dopo aver eseguito il comando, digitare **exit** per chiudere il client SSH.  A questo punto la macchina virtuale sarà ancora in esecuzione.


1. [Scaricare un disco del sistema operativo esistente](../virtual-machines/linux/download-vhd.md).

Usare questo disco rigido virtuale per creare e distribuire una macchina virtuale nel dispositivo Azure Stack Edge Pro. È possibile usare le due immagini di Azure Marketplace seguenti per creare immagini personalizzate di Linux:

|Nome elemento  |Descrizione  |Publisher  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu Server è il prodotto Linux più diffuso al mondo per ambienti cloud.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux è una delle distribuzioni Linux più popolari.     |credativ|

Per un elenco completo di immagini di Azure Marketplace che potrebbero funzionare (attualmente non testate), vedere [Elementi di Azure Marketplace disponibili per l'hub di Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910).


## <a name="next-steps"></a>Passaggi successivi

[Distribuire macchine virtuali nel dispositivo Azure Stack Edge Pro](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
