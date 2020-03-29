---
title: Usare una raccolta di immagini condivise in Azure Lab Services. Documenti Microsoft
description: Informazioni su come configurare un account lab per usare una raccolta di immagini condivise in modo che un utente possa condividere un'immagine con altri utenti e un altro utente possa usare l'immagine per creare una macchina virtuale modello nel lab.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190450"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usare una raccolta di immagini condivise in Azure Lab ServicesUse a shared image gallery in Azure Lab Services
Questo articolo illustra come gli insegnanti/amministratore di laboratorio possono salvare un'immagine di macchina virtuale modello per poter essere riutilizzata da altri utenti. Queste immagini vengono salvate in una [raccolta di immagini condivise di](../../virtual-machines/windows/shared-image-galleries.md)Azure. Come primo passaggio, l'amministratore del lab allega una raccolta di immagini condivise esistente all'account lab. Una volta allegata la raccolta di immagini condivise, i lab creati nell'account lab possono salvare le immagini nella raccolta di immagini condivise. Altri insegnanti possono selezionare questa immagine dalla galleria di immagini condivise per creare un modello per le loro classi. 

> [!NOTE]
> Attualmente, Azure Lab Services supporta la creazione di macchine virtuali modello basate solo su immagini di macchine virtuali **generalizzate** (immagini non specializzate) in una raccolta di immagini condivise. 

## <a name="prerequisites"></a>Prerequisiti
- Creare una raccolta di immagini condivise usando Azure PowerShell o [l'interfaccia della](../../virtual-machines/linux/shared-images.md)riga di comando di Azure.Create a shared image gallery by using either [Azure PowerShell](../../virtual-machines/windows/shared-images.md) or Azure CLI .
- La raccolta di immagini condivise è stata collegata all'account lab. Per istruzioni dettagliate, vedere [Come allegare o scollegare](how-to-attach-detach-shared-image-gallery.md)una raccolta immagini condivise.


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvare un'immagine nella raccolta immagini condivisa
Dopo aver allegato una raccolta di immagini condivise, l'amministratore di un account lab o un insegnante può salvare un'immagine nella raccolta immagini condivisa in modo che possa essere riutilizzata da altri insegnanti. 

1. Nella pagina **Modello** per il lab selezionare **Esporta in raccolta immagini condivise** sulla barra degli strumenti.

    ![Pulsante Salva immagine](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Nella finestra di dialogo Esporta in **raccolta immagini condivise** immettere un nome per **l'immagine**e quindi selezionare **Esporta**. 

    ![Finestra di dialogo Esporta in Raccolta immagini condivise](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. È possibile visualizzare lo stato di avanzamento di questa operazione nella pagina **Modello.** Questa operazione può richiedere più tempo. 

    ![Esportazione in corso](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Quando l'operazione di esportazione ha esito positivo, viene visualizzato il seguente messaggio:

    ![Esportazione completata](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

È anche possibile caricare un'immagine nella raccolta di immagini condivise all'esterno del contesto di un lab. Per ulteriori informazioni, consultate [Panoramica della raccolta immagini condivise.](../../virtual-machines/windows/shared-images.md) 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usare un'immagine dalla raccolta immagini condivisa
Un insegnante/professore può scegliere un'immagine personalizzata disponibile nella galleria di immagini condivise per il modello durante la creazione di un nuovo laboratorio.

![Usare l'immagine della macchina virtuale dalla raccoltaUse virtual machine image from the gallery](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle raccolte di immagini condivise, consultate [Raccolta immagini condivise.](../../virtual-machines/windows/shared-image-galleries.md)
