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
ms.date: 01/18/2019
ms.author: spelluru
ms.openlocfilehash: b5abb8d4aad7c58bf673aa578255efe12d32ad4b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422899"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Esercitazione: Accedere a un lab in Azure DevTest Labs
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

    > [!NOTE] 
    > Per connettersi a una VM Linux, è necessario abilitare l'accesso SSH e/o RDP alla macchina virtuale. Per la procedura di connessione a una VM Linux tramite RDP, vedere [Installare e configurare Desktop remoto per connettersi a una VM Linux di Azure](../virtual-machines/linux/use-remote-desktop.md). 


## <a name="unclaim-the-vm"></a>Annullare la richiesta della VM
Dopo aver terminato di usare la VM, annullarne la richiesta seguendo questa procedura: 

1. Nella pagina della macchina virtuale selezionare **Annulla richiesta** sulla barra degli strumenti. 

    ![Annullare la richiesta della VM](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. La VM viene arrestata prima che venga annullata la richiesta. È possibile controllare lo stato dell'operazione nelle notifiche.  
3. Per tornare alla pagina del DevTest Lab, fare clic sul nome del lab nel menu di navigazione nella parte superiore. 
    
    ![Tornare al lab](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Verificare che sia visibile la VM nell'elenco delle **Macchine virtuali richiedibili** in basso.

    
## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come accedere a un lab creato tramite Azure DevTest Labs e come usarlo. Per altre informazioni sull'accesso e sull'uso delle macchine virtuali in un lab, vedere 

> [!div class="nextstepaction"]
> [Procedura: Usare le VM in un lab](devtest-lab-add-vm.md)

