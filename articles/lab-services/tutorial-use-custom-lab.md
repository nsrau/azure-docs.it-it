---
title: Accedere a un lab in Azure DevTest Labs | Microsoft Docs
description: Questa esercitazione descrive come accedere al lab creato usando Azure DevTest Labs, richiedere una macchina virtuale, usarla e quindi annullare la richiesta.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: cd623767c9627810afb64ca9185c991c5c9f3858
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638025"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Accedere a un lab in Azure DevTest Labs
Questa esercitazione descrive come usare il lab creato in [Esercitazione: Creare un lab in Azure DevTest Labs](tutorial-create-custom-lab.md).

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Richiedere una macchina virtuale (VM) nel lab
> * Connettersi alla VM
> * Annullare la richiesta della VM

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="access-the-lab"></a>Accedere al lab

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare **Tutte le risorse**. 
3. Selezionare **DevTest Labs** per tipo di risorsa. 
4. Selezionare il lab. 

    ![Selezionare il lab](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Richiedere una VM

1. Nell'elenco delle **Macchine virtuali richiedibili** selezionare **...** (puntini di sospensione), quindi **Richiedi macchina**.

    ![Richiedere una macchina virtuale](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Verificare che la VM sia disponibile nell'elenco **Macchine virtuali personali**.

    ![Macchine virtuali personali](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Connettersi alla VM

1. Selezionare la VM nell'elenco. Viene visualizzata la **pagina della macchina virtuale** relativa alla VM selezionata. Selezionare **Connetti** sulla barra degli strumenti.

    ![Connettersi alla macchina virtuale](./media/tutorial-use-custom-lab/connect-button.png)
2. Salvare il file **RDP** scaricato nel disco rigido e usarlo per connettersi alla macchina virtuale. Specificare il nome utente e la password indicati quando è stata creata la VM nella sezione precedente. 

## <a name="unclaim-the-vm"></a>Annullare la richiesta della VM
Dopo aver terminato di usare la VM, annullarne la richiesta seguendo questa procedura: 

1. Nella pagina della macchina virtuale selezionare **Annulla richiesta** sulla barra degli strumenti. 

    ![Annullare la richiesta della VM](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. La VM viene arrestata prima che venga annullata la richiesta. 

    ![Stato di annullamento della richiesta](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. Al termine dell'operazione di annullamento della richiesta, la VM viene visualizzata nell'elenco delle **Macchine virtuali richiedibili** nella parte inferiore. 
    
## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come accedere a un lab creato tramite Azure DevTest Labs e come usarlo. Per altre informazioni sull'accesso e sull'uso delle macchine virtuali in un lab, vedere 

> [!div class="nextstepaction"]
> [Procedura: Usare le VM in un lab](devtest-lab-add-vm.md)

