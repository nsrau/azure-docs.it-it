---
title: Scaricare un disco rigido virtuale Linux da Azure
description: Scaricare un disco rigido virtuale Linux usando l'interfaccia della riga di comando di Azure e il portale di Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 897cae53e589f4058e5499c0e6e941d4f1d9bb2f
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87761059"
---
# <a name="download-a-linux-vhd-from-azure"></a>Scaricare un disco rigido virtuale Linux da Azure

Questo articolo illustra come scaricare un file di disco rigido virtuale (VHD) Linux da Azure usando il portale di Azure. 

## <a name="stop-the-vm"></a>Arrestare la VM

Un disco rigido virtuale non può essere scaricato da Azure se è collegato a una macchina virtuale in esecuzione. È necessario arrestare la macchina virtuale per scaricare il disco rigido virtuale. 

1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Nel menu a sinistra selezionare **macchine virtuali**.
3.  Selezionare la VM dall'elenco.
4.  Nella pagina della macchina virtuale selezionare **Arresta**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Mostra il pulsante di menu per arrestare la macchina virtuale.":::

## <a name="generate-sas-url"></a>Generare l'URL SAS

Per scaricare il file VHD, è necessario generare un URL di [firma di accesso condiviso (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json). Quando viene generato, all'URL viene assegnata una scadenza.

1. Scegliere **dischi**dal menu della pagina per la macchina virtuale.
2. Selezionare il disco del sistema operativo per la macchina virtuale e quindi selezionare **esportazione disco**.
1. Se necessario, aggiornare il valore di **URL scade in (secondi)** per fornire tempo sufficiente per il completamento del download. Il valore predefinito è 3600 secondi (un'ora).
3. Selezionare **genera URL**.
 
      
## <a name="download-vhd"></a>Scaricare il disco rigido virtuale

1.  In URL generato selezionare **scaricare il file VHD**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Consente di visualizzare il pulsante per il download del disco rigido virtuale.":::

2.  Potrebbe essere necessario selezionare **Salva** nel browser per avviare il download. Il nome predefinito per il file VHD è *abcd*.

## <a name="next-steps"></a>Passaggi successivi

- Apprendere come [caricare e creare una macchina virtuale Linux da un disco personalizzato usando l'interfaccia della riga di comando di Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gestire i dischi di Azure con l'interfaccia della riga di comando di Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
