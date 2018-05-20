---
title: Configurare un account lab con Azure Lab Services | Microsoft Docs
description: Questa esercitazione descrive come configurare un account lab con Azure Lab Services (precedentemente DevTest Labs).
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
ms.date: 04/26/2018
ms.author: spelluru
ms.openlocfilehash: 347b7d183839868f3b52adbdfd00b38cee3f3fbc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services-formerly-azure-devtest-labs"></a>Esercitazione: Configurare un account lab con Azure Lab Services (precedentemente Azure DevTest Labs)
Questa esercitazione descrive come un amministratore del lab può creare un account lab con Azure Lab Services. Quindi, fornire ai docenti l'autorizzazione per creare lab per le proprie classi nell'account lab. Un docente può impostare un lab per una classe usando il [sito Web di Azure Lab Services](https://labs.azure.com).   

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Creare un account lab
> * Aggiungere un utente al ruolo di creatore di lab

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-lab-account"></a>Creare un account lab
La procedura seguente illustra come usare il portale di Azure per creare un account lab con Azure Lab Services. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Creare una risorsa** dal menu principale a sinistra.
3. Cercare **Lab Services** in Azure Marketplace e quindi selezionare **Lab Services** nell'elenco a discesa. 
4. Selezionare **Lab Services (Preview)** nell'elenco filtrato dei servizi. 
1. Nella finestra **Create a lab account** (Crea un account lab) selezionare **Crea**.
2. Nella finestra **Lab account** (Account lab) eseguire queste operazioni: 
    1. In **Lab account name** (Nome account lab) immettere un nome. 
    2. Selezionare la **sottoscrizione di Azure** in cui creare l'account lab.
    3. In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
    4. In **Posizione** selezionare una posizione o un'area in cui si vuole creare l'account lab. 
    5. Selezionare **Create**. 

        ![Finestra Create a lab account (Crea un account lab)](./media/tutorial-setup-lab-account/lab-account-settings.png)
5. Se non viene visualizzata la pagina dell'account lab, selezionare il pulsante delle **notifiche** e quindi fare clic su **Vai alla risorsa** in Notifiche. 

    ![Finestra Create a lab account (Crea un account lab)](./media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. Verrà visualizzata la pagina dell'**account lab** seguente:

    ![Pagina dell'account lab](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Aggiungere un utente al ruolo di creatore di lab
Per fornire ai docenti l'autorizzazione per creare lab per le proprie classi, aggiungerli al ruolo di creatore di lab:

1. Nella pagina **Lab Account** (Account lab) selezionare **Controllo di accesso (IAM)** e fare clic su **+ Aggiungi** sulla barra degli strumenti. 

    ![Pagina dell'account lab](./media/tutorial-setup-lab-account/access-control.png)
2. Nella pagina **Aggiungi autorizzazioni** selezionare **Lab Creator** (Creatore di lab) per **Ruolo**, scegliere l'utente che si vuole aggiungere al ruolo di creatore del lab e fare clic su **Salva**. 

    ![Aggiungere un utente al ruolo di creatore di lab](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come creare un account lab. Per altre informazioni su come creare un lab per le classi in modo professionale, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Configurare un lab per le classi](tutorial-setup-classroom-lab.md)

