---
title: Creare un lab personalizzato in Azure DevTest Labs | Microsoft Docs
description: Questa guida introduttiva descrive come creare un lab personalizzato tramite Azure DevTest Labs.
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
ms.openlocfilehash: 3a4f964fe4f103e970a6edb3e9ba716fd72b8c79
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362244"
---
# <a name="tutorial-set-up-a-custom-lab-by-using-azure-devtest-labs"></a>Esercitazione: Configurare un lab personalizzato usando Azure DevTest Labs
Questa esercitazione descrive come creare un lab personalizzato usando il portale di Azure. Un amministratore del lab configura un lab in un'organizzazione, crea le V M nel lab e configura i criteri. Gli utenti del lab (ad esempio, gli sviluppatori e i tester) richiedono le VM nel lab, si connettono a tali VM e le usano. 

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Creare un lab personalizzato
> * Aggiungere le macchine virtuali (VM) al lab
> * Aggiungere un utente al ruolo di utente di lab

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-custom-lab"></a>Creare un lab personalizzato
La procedura seguente illustra come usare il portale di Azure per creare un lab in Azure DevTest Labs. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu principale sul lato sinistro selezionare **Crea una risorsa** (nella parte superiore dell'elenco), scegliere **Strumenti di sviluppo** e fare clic su **DevTest Labs**. 

    ![Nuovo menu DevTest Labs](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. Nella finestra **Crea un'istanza di DevTest Labs** eseguire queste operazioni: 
    1. In **Nome del lab** immettere un nome per il lab. 
    2. In **Sottoscrizione** selezionare la sottoscrizione in cui creare il lab. 
    3. In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse. 
    4. In **Posizione** selezionare la posizione o l'area in cui si vuole creare il lab. 
    5. Selezionare **Create**. 
    6. Selezionare **Aggiungi al dashboard**. Dopo la creazione, il lab viene visualizzato nel dashboard. 

        ![Creare una sezione di lab di DevTest Labs](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)

## <a name="add-a-vm-to-the-lab"></a>Aggiungere una VM al lab

1. Nella pagina **DevTest Labs** selezionare **+ Aggiungi** sulla barra degli strumenti. 

    ![Pulsante Aggiungi](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Nella pagina **Scegli una base** cercare con la parola chiave **Ubuntu**, quindi scegliere una delle immagini di base nell'elenco. 
1. Nella pagina **Macchina virtuale** eseguire queste operazioni: 
    1. In **Nome macchina virtuale** immettere un nome per la macchina virtuale. 
    2. In **Nome utente** immettere un nome per l'utente che dispone dell'accesso alla macchina virtuale. 
    3. In **Identificatore** immettere la password dell'utente. 
    4. Selezionare **Impostazioni avanzate**.
    5. In **Make this machine claimable** (Rendi attestabile questa macchina) selezionare **Sì**.
    6. Verificare che **Conteggio istanze** sia impostato su **1**. Se questa opzione è impostata su **2**, vengono create 2 VM con i nomi: `<base image name>00' and <base image name>01`. Ad esempio: `win10vm00` e `win10vm01`. 
    7. Per chiudere la pagina **Avanzate** fare clic su **OK**. 
    8. Selezionare **Create**. 

        ![Scegli una base](./media/tutorial-create-custom-lab/new-virtual-machine.png)
    9. Viene visualizzato lo stato della VM nell'elenco delle **Macchine virtuali richiedibili**. Per la creazione della macchina virtuale possono essere necessari circa 25 minuti. La VM viene creata in un gruppo di risorse di Azure separato, il cui nome inizia con il nome del gruppo di risorse corrente con il lab. Ad esempio, se il lab è in `labrg`, la VM può essere creata nel gruppo di risorse `labrg3988722144002`. 

        ![Stato della creazione della VM](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. Dopo la creazione, la VM viene visualizzata nell'elenco delle **Macchine virtuali richiedibili**. 

## <a name="add-a-user-to-the-lab-user-role"></a>Aggiungere un utente al ruolo di utente di lab

1. Nel menu a sinistra selezionare **Configurazione e criteri**. 

    ![Configurazione e criteri](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. Selezionare **Controllo di accesso (IAM)** nel menu e fare clic su **+ Aggiungi** sulla barra degli strumenti. 

    ![Controllo di accesso - Aggiungi utente](./media/tutorial-create-custom-lab/access-control-add.png)
1. Nella pagina **Aggiungi autorizzazioni** eseguire queste operazioni:
    1. In **Ruolo** selezionare **Utente DevTest Labs**. 
    2. Selezionare l'**utente** che si vuole aggiungere. 
    3. Selezionare **Salva**.

        ![Aggiungere autorizzazioni](./media/tutorial-create-custom-lab/add-lab-user.png)
4. Per chiudere **Configurazione e criteri - Controllo di accesso (IAM)**, selezionare **X** nell'angolo a destra. 

## <a name="cleanup-resources"></a>Risorse di pulizia
La prossima esercitazione descrive come un utente di lab può richiedere e connettersi a una VM nel lab. Se non si vuole eseguire tale esercitazione e si vuole pulire le risorse create come parte di questa esercitazione, seguire questi passaggi: 

1. Nel portale di Azure fare clic sul menu **Gruppi di risorse**. 
2. Selezionare il gruppo di risorse in cui è stato creato il lab. 
3. Selezionare **Elimina gruppo di risorse** sulla barra degli strumenti. L'eliminazione di un gruppo di risorse eliminerà tutte le risorse nel gruppo, compreso il lab. 
4. Ripetere questi passaggi per eliminare il gruppo di risorse aggiuntive creato con il nome `<your resource group name><random numbers>`. Ad esempio: `splab3988722144001`. Le VM vengono create in questo gruppo di risorse anziché nel gruppo di risorse in cui si trova il lab. 

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come creare un lab personalizzato con una VM e come assegnare a un utente l'accesso al lab. Per informazioni su come accedere al lab come utente di lab, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Esercitazione: Accesso al lab personalizzato](tutorial-use-custom-lab.md)

