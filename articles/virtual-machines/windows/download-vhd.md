---
title: Scaricare un disco rigido virtuale Linux da Azure
description: Scaricare un disco rigido virtuale Windows tramite il portale di Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: e87f2436f69abed4c0d0ee415226f99b52f2e804
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085366"
---
# <a name="download-a-windows-vhd-from-azure"></a>Scaricare un disco rigido virtuale Linux da Azure

Questo articolo illustra come scaricare un file di disco rigido virtuale (VHD) Windows da Azure usando il portale di Azure.

## <a name="optional-generalize-the-vm"></a>Facoltativo: generalizzare la VM

Se si desidera utilizzare il disco rigido virtuale come [immagine](tutorial-custom-images.md) per creare altre macchine virtuali, è necessario utilizzare [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) per generalizzare il sistema operativo. 

Per usare il disco rigido virtuale come immagine per creare altre macchine virtuali, generalizzare la VM.

1. Se non è già stato fatto, accedere al [portale di Azure](https://portal.azure.com/).
2. [Connettersi alla macchina virtuale](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. Nella VM aprire la finestra del prompt dei comandi come amministratore.
4. Passare alla directory *%windir%\system32\sysprep* e quindi eseguire sysprep.exe.
5. Nella finestra di dialogo Utilità preparazione sistema selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.
6. In opzioni di arresto selezionare **Arresta**, quindi fare clic su **OK**. 


## <a name="stop-the-vm"></a>Arrestare la VM

Un disco rigido virtuale non può essere scaricato da Azure se è collegato a una macchina virtuale in esecuzione. Per scaricare un disco rigido virtuale è quindi necessario arrestare la macchina virtuale. 

1. Nel menu Hub del portale di Azure fare clic su **Macchine virtuali**.
1. Selezionare la VM dall'elenco.
1. Nel pannello della VM fare clic su **Interrompi**.


## <a name="generate-download-url"></a>Genera URL di download

Per scaricare il file VHD, è necessario generare un URL di [firma di accesso condiviso (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Quando viene generato, all'URL viene assegnata una scadenza.

1. Nella pagina della macchina virtuale fare clic su **dischi** nel menu a sinistra.
1. Selezionare il disco del sistema operativo per la macchina virtuale.
1. Nella pagina del disco selezionare **esportazione disco** dal menu a sinistra.
1. L'ora di scadenza predefinita dell'URL è di *3600* secondi. Aumentare questo a **36000** per i dischi del sistema operativo Windows.
1. Fare clic su **Genera URL**.

> [!NOTE]
> La scadenza viene aumentata rispetto all'impostazione predefinita per fornire un tempo sufficiente a scaricare il file VHD di grandi dimensioni di un sistema operativo Windows Server. A seconda della connessione, il download di un file VHD che contiene il sistema operativo Windows Server può richiedere alcune ore. Se si scarica un disco rigido virtuale per un disco dati, il tempo predefinito è sufficiente. 
> 
> 

## <a name="download-vhd"></a>Scaricare il disco rigido virtuale

1. Nell'URL appena generato fare clic su Scarica il file VHD.
1. Potrebbe essere necessario fare clic su **Salva** nel browser per avviare il download. Il nome predefinito per il file VHD è *abcd*.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [caricare un file VHD in Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Creare dischi gestiti da dischi non gestiti in un account di archiviazione](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Gestire i dischi di Azure con PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

