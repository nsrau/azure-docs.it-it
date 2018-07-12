---
title: Creare un'immagine personalizzata di Azure DevTest Labs da una VM | Documentazione Microsoft
description: Informazioni su come creare un'immagine personalizzata in Azure DevTest Labs da una VM predisposta usando il portale di Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 22f1579b2df2acdc736ed4c1d5cee64d096c320a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635338"
---
# <a name="create-a-custom-image-from-a-vm"></a>Creare un'immagine personalizzata da una VM

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Istruzioni dettagliate

È possibile creare un'immagine personalizzata da una macchina virtuale predisposta e successivamente utilizzare tale immagine per creare macchine virtuali identiche. I passaggi seguenti illustrano come creare un'immagine personalizzata da una macchina virtuale:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.  

1. Nel riquadro principale del lab selezionare **Macchine virtuali personali**.
 
1. Nel riquadro **Macchine virtuali personali** selezionare la macchina virtuale da cui creare l'immagine personalizzata.

1. Nel riquadro di gestione della macchina virtuale selezionare **Crea immagine personalizzata (disco rigido virtuale)**.

    ![Voce di menu Crea immagine personalizzata](./media/devtest-lab-create-template/create-custom-image.png)

1. Nel riquadro **Immagine personalizzata** immettere un nome e una descrizione per l'immagine personalizzata. Queste informazioni vengono visualizzate nell'elenco delle immagini di base quando si crea una VM. L'immagine personalizzata includerà il disco del sistema operativo e tutti i dischi dati collegati alla macchina virtuale.

    ![Riquadro Crea immagine personalizzata](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Indicare se è stato eseguito sysprep nella macchina virtuale. Se non è stato eseguito sysprep nella VM, specificare se si desidera eseguirlo quando si crea una VM da questa immagine personalizzata.

1. Selezionare **OK** al termine per creare l'immagine personalizzata.

Dopo alcuni minuti, l'immagine personalizzata viene creata e archiviata nell'account di archiviazione del lab. Quando un utente del lab vuole creare una nuova macchina virtuale, l'immagine è disponibile nell'elenco delle immagini di base.

![Immagine personalizzata disponibile nell'elenco delle immagini di base](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati

- [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copia di immagini personalizzate tra istanze di Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere una macchina virtuale all'ambiente lab](devtest-lab-add-vm.md)
