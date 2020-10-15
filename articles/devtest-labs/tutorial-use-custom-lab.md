---
title: Accedere a un lab in Azure DevTest Labs | Microsoft Docs
description: Questa esercitazione descrive come accedere al lab creato usando Azure DevTest Labs, richiedere una macchina virtuale, usarla e quindi annullare la richiesta.
ms.topic: tutorial
ms.date: 06/26/2020
ms.author: spelluru
ms.openlocfilehash: b4477e0b98ef534b8170ee297edf88ac6fa62dd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476445"
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

    Per connettersi a una VM Linux, è necessario abilitare l'accesso SSH e/o RDP alla macchina virtuale. Per la procedura di connessione a una VM Linux tramite RDP, vedere [Installare e configurare Desktop remoto per connettersi a una VM Linux di Azure](../virtual-machines/linux/use-remote-desktop.md). 

    > [!NOTE]
    > Esistono altri modi per accedere alla pagina Macchina virtuale per la VM. Eccone alcuni: 
    > 
    > 1. Cercare tutte le VM nella sottoscrizione. Selezionare la VM nell'elenco di macchine virtuali per passare alla pagina **Macchina virtuale**.
    > 2. Passare alla pagina **Gruppo di risorse** relativa al gruppo di risorse. Quindi selezionare la VM nell'elenco di risorse del gruppo di risorse per passare alla pagina **Macchina virtuale**. 
    >
    > Non usare il pulsante **Connetti** sulla barra degli strumenti nella pagina **Macchina virtuale** a cui si accede usando queste opzioni. Al contrario, passare alla pagina **Macchina virtuale** dalla pagina **DevTest Labs**, come illustrato in questo articolo, quindi usare il pulsante **Connetti** sulla barra degli strumenti.


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

