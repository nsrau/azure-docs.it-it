---
title: Scaricare un disco rigido virtuale (VHD) Windows da Azure | Microsoft Docs
description: Scaricare un disco rigido virtuale Windows tramite il portale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 7128413e48fdeef9b9284bc6db11649016a06153
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722810"
---
# <a name="download-a-windows-vhd-from-azure"></a>Scaricare un disco rigido virtuale Linux da Azure

Questo articolo illustra come scaricare un file di disco rigido virtuale (VHD) Windows da Azure usando il portale di Azure.

## <a name="stop-the-vm"></a>Arrestare la VM

Un disco rigido virtuale non può essere scaricato da Azure se è collegato a una macchina virtuale in esecuzione. Per scaricare un disco rigido virtuale è quindi necessario arrestare la macchina virtuale. Se si vuole usare un disco rigido virtuale come [immagine](tutorial-custom-images.md) per la creazione di altre macchine virtuali con nuovi dischi, è necessario usare [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) per generalizzare il sistema operativo contenuto nel file e successivamente arrestare la macchina virtuale. Per usare il disco rigido virtuale come disco in cui creare una nuova istanza di un disco dati o di una macchina virtuale esistente, è sufficiente arrestare e deallocare la macchina virtuale.

Per usare il disco rigido virtuale come immagine per la creazione di altre macchine virtuali, seguire questa procedura:

1.  Accedere al [portale di Azure](https://portal.azure.com/), se questa operazione non è già stata eseguita.
2.  [Connettersi alla VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Nella VM aprire la finestra del prompt dei comandi come amministratore.
4.  Passare alla directory *%windir%\system32\sysprep* e quindi eseguire sysprep.exe.
5.  Nella finestra di dialogo Utilità preparazione sistema selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.
6.  In Opzioni di arresto selezionare **Arresta il sistema** e quindi fare clic su **OK**. 

Per usare il disco rigido virtuale come disco in cui creare una nuova istanza di un disco dati o di una macchina virtuale esistente, seguire questa procedura:

1.  Nel menu Hub del portale di Azure fare clic su **Macchine virtuali**.
2.  Selezionare la VM dall'elenco.
3.  Nel pannello della VM fare clic su **Interrompi**.

    ![Arrestare la macchina virtuale](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generare l'URL SAS

Per scaricare il file VHD, è necessario generare un URL di [firma di accesso condiviso (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Quando viene generato, all'URL viene assegnata una scadenza.

1.  Nel menu del pannello della macchina virtuale fare clic su **Dischi**.
2.  Selezionare il disco del sistema operativo per la VM e quindi fare clic su **Esporta**.
3.  Impostare la scadenza dell'URL su *36000*.
4.  Fare clic su **Genera URL**.

    ![Generare l'URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> La scadenza viene aumentata rispetto all'impostazione predefinita per fornire un tempo sufficiente a scaricare il file VHD di grandi dimensioni di un sistema operativo Windows Server. A seconda della connessione, il download di un file VHD che contiene il sistema operativo Windows Server può richiedere alcune ore. Se si scarica un disco rigido virtuale per un disco dati, il tempo predefinito è sufficiente. 
> 
> 

## <a name="download-vhd"></a>Scaricare il disco rigido virtuale

1.  Nell'URL appena generato fare clic su Scarica il file VHD.

    ![Scaricare il disco rigido virtuale](./media/download-vhd/export-download.png)

2.  Potrebbe essere necessario fare clic su **Salva** nel browser per avviare il download. Il nome predefinito per il file VHD è *abcd*.

    ![Fare clic su Salva nel browser.](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [caricare un file VHD in Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Creare dischi gestiti da dischi non gestiti in un account di archiviazione](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Gestire i dischi di Azure con PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

