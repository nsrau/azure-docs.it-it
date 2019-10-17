---
title: Usare una raccolta di immagini condivise in Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un account Lab per l'uso di una raccolta di immagini condivise in modo che un utente possa condividere un'immagine con altri utenti e un altro utente possa usare l'immagine per creare una macchina virtuale modello nel Lab.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: e9073e7cba97c4f5e2d550e5e49a6655ec76abbe
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332027"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usare una raccolta di immagini condivise in Azure Lab Services
Questo articolo illustra in che modo i docenti e gli amministratori del Lab possono salvare un'immagine di macchina virtuale modello affinché venga riutilizzata da altri. Queste immagini vengono salvate in una [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md)di Azure. Come primo passaggio, l'amministratore del Lab connette una raccolta di immagini condivise esistente all'account Lab. Una volta collegata la raccolta di immagini condivise, i Lab creati nell'account Lab possono salvare le immagini nella raccolta di immagini condivise. Altri docenti possono selezionare questa immagine dalla raccolta di immagini condivise per creare un modello per le classi. 

## <a name="prerequisites"></a>Prerequisiti
- Creare una raccolta di immagini condivise usando [Azure PowerShell](../../virtual-machines/windows/shared-images.md) o l' [interfaccia](../../virtual-machines/linux/shared-images.md)della riga di comando di Azure.
- La raccolta di immagini condivise è stata associata all'account Lab. Per istruzioni dettagliate, vedere la pagina relativa [alla modalità di collegamento o scollegamento di una raccolta di immagini condivise](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvare un'immagine nella raccolta immagini condivise
Una volta collegata una raccolta di immagini condivise, un amministratore dell'account Lab o un insegnante può salvare un'immagine nella raccolta di immagini condivise in modo che possa essere riutilizzata da altri docenti. 

1. Nella pagina **modello** per il Lab selezionare **Esporta in raccolta immagini condivise** sulla barra degli strumenti.

    ![Pulsante Salva immagine](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Nella finestra **di dialogo Esporta in raccolta immagini condivise** immettere un **nome per l'immagine**e quindi selezionare **Esporta**. 

    ![Finestra di dialogo Esporta nella raccolta immagini condivise](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. È possibile visualizzare lo stato di avanzamento di questa operazione nella pagina **modello** . Questa operazione può richiedere qualche minuto. 

    ![Esportazione in corso](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Quando l'operazione di esportazione ha esito positivo, viene visualizzato il messaggio seguente:

    ![Esportazione completata](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

È anche possibile caricare un'immagine nella raccolta di immagini condivise all'esterno del contesto di un Lab. Per altre informazioni, vedere [Cenni preliminari sulla raccolta di immagini condivise](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usare un'immagine dalla raccolta immagini condivise
Un insegnante/professore può scegliere un'immagine personalizzata disponibile nella raccolta di immagini condivise per il modello durante la creazione di un nuovo Lab.

![Usare l'immagine della macchina virtuale dalla raccolta](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccolte immagini condivise, vedere [raccolta immagini condivise](../../virtual-machines/windows/shared-image-galleries.md).
