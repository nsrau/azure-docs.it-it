---
title: Gestire le formule in Azure DevTest Labs per creare VM | Microsoft Docs
description: Informazioni su come aggiornare e rimuovere le formule di Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fd334dc8cd22990456888146f41389054f2e8b29
ms.lasthandoff: 03/15/2017


---
# <a name="manage-azure-devtest-labs-formulas"></a>Gestire le formule di Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

L'articolo [Create Azure DevTest Labs formulas](devtest-lab-create-formulas.md#create-a-formula) (Creare formule di Azure DevTest Labs) illustra il processo di creazione di una formula in Azure DevTest Labs. Dopo aver creato una formula, seguire questo articolo per capire come gestirla.

## <a name="modify-a-formula"></a>Modificare una formula
Per modificare una formula, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Nel pannello del lab selezionare **Formule (basi riutilizzabili)**.
   
    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Nel pannello **Formule lab** selezionare la formula che si vuole modificare.
6. Apportare le modifiche nel pannello **Update formula** (Aggiorna formula) e selezionare **Update** (Aggiorna).

## <a name="delete-a-formula"></a>Eliminare una formula
Per eliminare una formula, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Nel pannello **Settings** (Impostazioni) del lab selezionare **Formulas** (Formule).
   
    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Nel pannello **Formule lab** selezionare i puntini di sospensione a destra della formula da eliminare.
   
    ![Menu Formula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Selezionare **Elimina**dal menu di scelta rapida della formula.
   
    ![Menu di scelta rapida Formula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selezionare **Sì** nella finestra di dialogo di conferma dell'eliminazione.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati
* [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato una formula da usare durante la creazione di una macchina virtuale, il passaggio successivo consiste nell'[aggiungere una macchina virtuale al lab](devtest-lab-add-vm-with-artifacts.md).


