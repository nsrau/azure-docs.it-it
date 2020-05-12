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
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: 294a270107033590e340db49b85e8a67acbbb701
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116867"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usare una raccolta di immagini condivise in Azure Lab Services
Questo articolo illustra come i docenti e gli amministratori del Lab possono salvare un'immagine di macchina virtuale modello in una [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md) in modo che possa essere usata da altri utenti per creare Lab. 

## <a name="scenarios"></a>Scenari
Ecco i due scenari supportati da questa funzionalità: 

- Un amministratore dell'account Lab connette una raccolta di immagini condivise all'account Lab e carica un'immagine nella raccolta di immagini condivise all'esterno del contesto di un Lab. Quindi, gli autori del Lab possono usare tale immagine dalla raccolta di immagini condivise per creare i Lab. 
- Un amministratore dell'account Lab connette una raccolta di immagini condivise all'account Lab. Un autore del Lab (Instructor) Salva l'immagine personalizzata del suo Lab nella raccolta di immagini condivise. Quindi, altri creatori di Lab possono selezionare questa immagine dalla raccolta di immagini condivise per creare un modello per i propri Lab. 

    Quando un'immagine viene salvata in una raccolta di immagini condivise, Azure Lab Services replica l'immagine salvata in altre aree disponibili nella stessa area [geografica](https://azure.microsoft.com/global-infrastructure/geographies/). Assicura che l'immagine sia disponibile per i Lab creati in altre aree nella stessa area geografica. Il salvataggio di immagini in una raccolta di immagini condivise comporta un costo aggiuntivo, che include il costo per tutte le immagini replicate. Questo costo è separato dal costo di utilizzo del Azure Lab Services. Per altre informazioni sui prezzi di raccolta immagini condivise, vedere [raccolta immagini condivise-fatturazione]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

    > [!NOTE]
    > Azure Lab Services supporta la creazione di macchine virtuali modello basate su immagini **generalizzate** e **specializzate** in una raccolta di immagini condivise. 


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

    > [!NOTE]
    > Dopo avere salvato l'immagine nella raccolta immagini condivise, è possibile usare tale immagine dalla raccolta durante la creazione di un altro Lab. 
    > 
    > È anche possibile caricare un'immagine nella raccolta di immagini condivise all'esterno del contesto di un Lab. Per altre informazioni, vedere [Cenni preliminari sulla raccolta di immagini condivise](../../virtual-machines/windows/shared-images.md). 

    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usare un'immagine dalla raccolta immagini condivise
Un insegnante/professore può scegliere un'immagine personalizzata disponibile nella raccolta di immagini condivise per il modello durante la creazione di un nuovo Lab.

![Usare l'immagine della macchina virtuale dalla raccolta](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccolte immagini condivise, vedere [raccolta immagini condivise](../../virtual-machines/windows/shared-image-galleries.md).
