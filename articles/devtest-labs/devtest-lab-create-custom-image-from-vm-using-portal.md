---
title: Creare un'immagine personalizzata di Azure DevTest Labs da una VM | Documentazione Microsoft
description: Informazioni su come creare un'immagine personalizzata in Azure DevTest Labs da una VM predisposta usando il portale di Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 597ef49b667cf613b721c645e09f8e6dcb157011
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85476598"
---
# <a name="create-a-custom-image-from-a-vm"></a>Creare un'immagine personalizzata da una macchina virtuale

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Istruzioni dettagliate

È possibile creare un'immagine personalizzata da una macchina virtuale predisposta e successivamente utilizzare tale immagine per creare macchine virtuali identiche. I passaggi seguenti illustrano come creare un'immagine personalizzata da una macchina virtuale:

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **tutti i servizi**, quindi selezionare **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.  

1. Nel riquadro principale del lab selezionare **Macchine virtuali personali**.
 
1. Nel riquadro **Macchine virtuali personali** selezionare la macchina virtuale da cui creare l'immagine personalizzata.

1. Nel riquadro di gestione della macchina virtuale selezionare **Crea immagine personalizzata** in **operazioni**.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image.png" alt-text="Voce di menu Crea immagine personalizzata":::
1. Nel riquadro **Immagine personalizzata** immettere un nome e una descrizione per l'immagine personalizzata. Queste informazioni vengono visualizzate nell'elenco delle immagini di base quando si crea una VM. L'immagine personalizzata includerà il disco del sistema operativo e tutti i dischi dati collegati alla macchina virtuale.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="Pagina Crea immagine personalizzata":::
1. Indicare se è stato eseguito sysprep nella macchina virtuale. Se non è stato eseguito sysprep nella macchina virtuale, specificare se si vuole che sysprep venga eseguito nella macchina virtuale quando viene creata l'immagine personalizzata.
1. Selezionare **OK** al termine per creare l'immagine personalizzata.

    Dopo alcuni minuti, l'immagine personalizzata viene creata e archiviata nell'account di archiviazione del lab. Quando un utente del lab vuole creare una nuova macchina virtuale, l'immagine è disponibile nell'elenco delle immagini di base.

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="immagine personalizzata disponibile nell'elenco delle immagini di base":::

## <a name="related-blog-posts"></a>Post di blog correlati

- [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copia di immagini personalizzate tra istanze di Azure DevTest Labs)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere una macchina virtuale al lab](devtest-lab-add-vm.md)
