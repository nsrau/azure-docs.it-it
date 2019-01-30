---
title: Creare un lab usando Azure DevTest Labs | Microsoft Docs
description: Questa guida introduttiva descrive come creare un lab tramite Azure DevTest Labs.
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
ms.openlocfilehash: 84a6cdb5e91128bbade43ee9212cfa9658228964
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54423293"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>Esercitazione: Configurare un lab usando Azure DevTest Labs
Questa esercitazione descrive come creare un lab usando il portale di Azure. Un amministratore del lab configura un lab in un'organizzazione, crea le V M nel lab e configura i criteri. Gli utenti del lab (ad esempio, gli sviluppatori e i tester) richiedono le VM nel lab, si connettono a tali VM e le usano. 

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Creare un lab
> * Aggiungere le macchine virtuali (VM) al lab
> * Aggiungere un utente al ruolo di utente di lab

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-lab"></a>Creare un lab
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
2. Verificare che il lab venga creato correttamente esaminando le notifiche. Selezionare **Vai alla risorsa**.  

    ![Notifica](./media/tutorial-create-custom-lab/creation-notification.png)
3. Verificare che sia visualizzata la pagina **DevTest Lab** relativa al proprio lab. 

    ![Home page del lab](./media/tutorial-create-custom-lab/lab-home-page.png)

## <a name="add-a-vm-to-the-lab"></a>Aggiungere una VM al lab

1. Nella pagina **DevTest Labs** selezionare **+ Aggiungi** sulla barra degli strumenti. 

    ![Pulsante Aggiungi](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Nella pagina **Scegli una base** cercare con una parola chiave (ad esempio: Windows, Ubuntu) quindi scegliere una delle immagini di base nell'elenco. 
1. Nella pagina **Macchina virtuale** eseguire queste operazioni: 
    1. In **Nome macchina virtuale** immettere un nome per la macchina virtuale. 
    2. In **Nome utente** immettere un nome per l'utente che dispone dell'accesso alla macchina virtuale. 
    3. Per **Password** immettere la password dell'utente. 

        ![Scegli una base](./media/tutorial-create-custom-lab/new-virtual-machine.png)
1. Selezionare la scheda **Impostazioni avanzate**.
    1. In **Make this machine claimable** (Rendi attestabile questa macchina) selezionare **Sì**.
    2. Verificare che **Conteggio istanze** sia impostato su **1**. Se questa opzione è impostata su **2**, vengono create 2 VM con i nomi: `<base image name>00' and <base image name>01`. Ad esempio: `win10vm00` e `win10vm01`.     
    3. Selezionare **Submit** (Invia). 

        ![Scegli una base](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
    9. Viene visualizzato lo stato della VM nell'elenco delle **Macchine virtuali richiedibili**. Per la creazione della macchina virtuale possono essere necessari circa 25 minuti. La VM viene creata in un gruppo di risorse di Azure separato, il cui nome inizia con il nome del gruppo di risorse corrente con il lab. Ad esempio, se il lab è in `labrg`, la VM può essere creata nel gruppo di risorse `labrg3988722144002`. 

        ![Stato della creazione della VM](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. Dopo la creazione, la VM viene visualizzata nell'elenco delle **Macchine virtuali richiedibili**. 

    > [!NOTE] 
    > Quando si aggiunge una VM Linux a un lab, è possibile abilitare l'accesso RDP ed SSH alla macchina virtuale. Se non si abilita l'accesso durante la creazione della macchina virtuale, è possibile aggiungere manualmente le regole nel gruppo di sicurezza di rete associato alla macchina virtuale per aprire le porte per RDP ed SSH.

## <a name="add-a-user-to-the-lab-user-role"></a>Aggiungere un utente al ruolo di utente di lab

1. Nel menu a sinistra selezionare **Configurazione e criteri**. 

    ![Configurazione e criteri](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. Selezionare **Controllo di accesso (IAM)** nel menu e fare clic su **+ Aggiungi assegnazione di ruolo** sulla barra degli strumenti. 

    ![Aggiungi assegnazione di ruolo - pulsante](./media/tutorial-create-custom-lab/add-role-assignment-button.png)
1. Nella pagina **Aggiungi autorizzazioni** eseguire queste operazioni:
    1. In **Ruolo** selezionare **Utente DevTest Labs**. 
    2. Selezionare l'**utente** che si vuole aggiungere. 
    3. Selezionare **Salva**.

        ![Add user](./media/tutorial-create-custom-lab/add-user.png)

## <a name="clean-up-resources"></a>Pulire le risorse
La prossima esercitazione descrive come un utente di lab può richiedere e connettersi a una VM nel lab. Se non si vuole eseguire tale esercitazione e si vuole pulire le risorse create come parte di questa esercitazione, seguire questi passaggi: 

1. Nel portale di Azure fare clic sul menu **Gruppi di risorse**. 

    ![Gruppi di risorse](./media/tutorial-create-custom-lab/resource-groups.png)
1. Selezionare il gruppo di risorse in cui è stato creato il lab. 
1. Selezionare **Elimina gruppo di risorse** sulla barra degli strumenti. L'eliminazione di un gruppo di risorse eliminerà tutte le risorse nel gruppo, compreso il lab. 

    ![Gruppo di risorse del lab](./media/tutorial-create-custom-lab/lab-resource-group.png)
1. Ripetere questi passaggi per eliminare il gruppo di risorse aggiuntive creato con il nome `<your resource group name><random numbers>`. Ad esempio: `splab3988722144001`. Le VM vengono create in questo gruppo di risorse anziché nel gruppo di risorse in cui si trova il lab. 

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come creare un lab con una VM e come assegnare a un utente l'accesso al lab. Per informazioni su come accedere al lab come utente di lab, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Esercitazione: Accedere al lab](tutorial-use-custom-lab.md)

