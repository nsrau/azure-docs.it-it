---
title: Configurare un account lab con Azure Lab Services | Microsoft Docs
description: Questa esercitazione descrive come configurare un account lab con Azure Lab Services.
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
ms.openlocfilehash: 600be7518bc526d3f147bb16377677854b676f63
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823130"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Esercitazione: Configurare un account lab con Azure Lab Services
In Azure Lab Services l'account lab funge da account centrale in cui vengono gestiti tutti i lab dell'organizzazione. Nell'account lab si concedono ad altri utenti le autorizzazioni alla creazione di lab e di impostano criteri che si applicano a tutti i lab all'interno dell'account lab. Questa esercitazione illustra come creare un account lab come amministratore del lab. 

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

        ![Finestra Create a lab account (Crea un account lab)](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Se non viene visualizzata la pagina dell'account lab, selezionare il pulsante delle **notifiche** e quindi fare clic su **Vai alla risorsa** in Notifiche. 

    ![Finestra Create a lab account (Crea un account lab)](../media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. Verrà visualizzata la pagina dell'**account lab** seguente:

    ![Pagina dell'account lab](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Aggiungere un utente al ruolo di creatore di lab
Per fornire ai docenti l'autorizzazione per creare lab per le proprie classi, aggiungerli al ruolo di creatore di lab:

1. Nella pagina **Lab Account** (Account lab) selezionare **Controllo di accesso (IAM)** e fare clic su **+ Aggiungi** sulla barra degli strumenti. 

    ![Pagina dell'account lab](../media/tutorial-setup-lab-account/access-control.png)
2. Nella pagina **Aggiungi autorizzazioni** selezionare **Lab Creator** (Creatore di lab) per **Ruolo**, scegliere l'utente che si vuole aggiungere al ruolo di creatore del lab e fare clic su **Salva**. 

    ![Aggiungere un utente al ruolo di creatore di lab](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come creare un account lab. Per altre informazioni su come creare un lab per le classi in modo professionale, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Configurare un lab per le classi](tutorial-setup-classroom-lab.md)

