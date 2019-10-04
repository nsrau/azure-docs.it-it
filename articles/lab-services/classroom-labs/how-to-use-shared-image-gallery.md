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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 65cc2d9ac2b96822f2c1b740f3180ba1d9eaf98c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389998"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usare una raccolta di immagini condivise in Azure Lab Services
Questo articolo illustra in che modo i docenti e gli amministratori del Lab possono salvare un'immagine di macchina virtuale modello affinché venga riutilizzata da altri. Queste immagini vengono salvate in una [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md)di Azure. Come primo passaggio, l'amministratore del Lab connette una raccolta di immagini condivise esistente all'account Lab. Una volta collegata la raccolta di immagini condivise, i Lab creati nell'account Lab possono salvare le immagini nella raccolta di immagini condivise. Altri docenti possono selezionare questa immagine dalla raccolta di immagini condivise per creare un modello per le classi. 

## <a name="prerequisites"></a>Prerequisiti
- Creare una raccolta di immagini condivise usando [Azure PowerShell](../../virtual-machines/windows/shared-images.md) o l' [interfaccia](../../virtual-machines/linux/shared-images.md)della riga di comando di Azure.
- La raccolta di immagini condivise è stata associata all'account Lab. Per istruzioni dettagliate, vedere la pagina relativa [alla modalità di collegamento o scollegamento di una raccolta di immagini condivise](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvare un'immagine nella raccolta immagini condivise
Una volta collegata una raccolta di immagini condivise, un amministratore dell'account Lab o un insegnante può salvare un'immagine nella raccolta di immagini condivise in modo che possa essere riutilizzata da altri docenti. 

1. Nella home page per il Lab selezionare **Salva immagine** nel riquadro nella sezione **modello** .

    ![Pulsante Salva immagine](../media/how-to-use-shared-image-gallery/save-image-button.png)
2.  Nella finestra **Salva immagine macchina virtuale** immettere un nome per l'immagine e selezionare **Salva**. 

    ![Salva finestra immagine macchina virtuale](../media/how-to-use-shared-image-gallery/save-virtual-machine-image.png)
3. Controllare lo stato nel riquadro Lab. 

    ![Stato dell'operazione di salvataggio dell'immagine](../media/how-to-use-shared-image-gallery/save-image-status.png)
4. Verificare che l'operazione abbia esito positivo.

    ![Operazione di salvataggio immagine riuscita](../media/how-to-use-shared-image-gallery/save-image-successful.png)

È anche possibile caricare un'immagine nella raccolta di immagini condivise all'esterno del contesto di un Lab. Per altre informazioni, vedere [Cenni preliminari sulla raccolta di immagini condivise](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usare un'immagine dalla raccolta immagini condivise
Un insegnante/professore può scegliere un'immagine personalizzata disponibile nella raccolta di immagini condivise per il modello durante la creazione di un nuovo Lab.

![Usare l'immagine della macchina virtuale dalla raccolta](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccolte immagini condivise, vedere [raccolta immagini condivise](../../virtual-machines/windows/shared-image-galleries.md).
