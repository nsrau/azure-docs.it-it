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
ms.openlocfilehash: 5af348bfdccb9392948af962cf582e33ebd40872
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services-formerly-azure-devtest-labs"></a>Esercitazione: Configurare un account lab con Azure Lab Services (precedentemente Azure DevTest Labs)
Questa esercitazione descrive come un amministratore del lab può creare un account lab con Azure Lab Services. Quindi, fornire ai docenti l'autorizzazione per creare lab per le proprie classi nell'account lab. Un docente può impostare un lab per una classe usando il [sito Web di Azure Lab Services](https://labs.azure.com).   

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Creare un account lab
> * Aggiungere un utente al ruolo di creatore di lab

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Azure Lab Services è attualmente in anteprima controllata. Per creare un account lab, [eseguire l'iscrizione all'anteprima](https://aka.ms/azlabspreviewsignup).

## <a name="create-a-lab-account"></a>Creare un account lab
La procedura seguente illustra come usare il portale di Azure per creare un account lab con Azure Lab Services. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu principale sul lato sinistro selezionare **Crea una risorsa** (nella parte superiore dell'elenco), scegliere **Strumenti di sviluppo** e fare clic su **Lab Services (anteprima)**.
1. Nella finestra **Create a lab account** (Crea un account lab) selezionare **Crea**.
2. Nella finestra **Lab account** (Account lab) eseguire queste operazioni: 
    1. In **Lab account name** (Nome account lab) immettere un nome. 
    2. Selezionare la **sottoscrizione di Azure** in cui creare l'account lab.
    3. In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
    4. In **Posizione** selezionare una posizione o un'area in cui si vuole creare l'account lab. 
    5. Selezionare **Crea**. 

        ![Finestra Crea un account lab](./media/tutorial-setup-lab-account/lab-account-settings.png)
5. Se non viene visualizzata la pagina dell'account lab, selezionare il pulsante delle **notifiche** e quindi fare clic su **Vai alla risorsa** in Notifiche. 

    ![Finestra Crea un account lab](./media/tutorial-setup-lab-account/notification-go-to-resource.png)    
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

