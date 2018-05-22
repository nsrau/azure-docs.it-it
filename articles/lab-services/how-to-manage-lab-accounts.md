---
title: Gestire account lab in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab in una sottoscrizione di Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 09303a4d4fc730aae6fc7c04148d64b0f57e69ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>Gestire gli account lab in Azure Lab Services (in precedenza Azure DevTest Labs)
Questo articolo descrive come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab.

## <a name="prerequisites"></a>prerequisiti
Azure Lab Services è attualmente in anteprima controllata. Per creare un account lab, [effettuare l'iscrizione per l'anteprima](https://aka.ms/azlabspreviewsignup).

## <a name="create-a-lab-account"></a>Creare un account lab
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu principale sul lato sinistro selezionare **Crea una risorsa** (nella parte superiore dell'elenco), scegliere **Strumenti di sviluppo** e fare clic su **Lab Services (anteprima)**.
1. Nella finestra **Create a lab account** (Crea un account lab) selezionare **Crea**.
2. Nella finestra **Lab account** (Account lab) eseguire queste operazioni: 
    1. In **Lab account name** (Nome account lab) immettere un nome. 
    2. Selezionare la **sottoscrizione di Azure** in cui creare l'account lab.
    3. In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
    4. In **Posizione** selezionare una posizione o un'area in cui si vuole creare l'account lab. 
    5. Selezionare **Create**. 

        ![Finestra Create a lab account (Crea un account lab)](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Se non viene visualizzata la pagina dell'account lab, selezionare il pulsante delle **notifiche** e quindi fare clic su **Vai alla risorsa** in Notifiche. 

    ![Finestra Create a lab account (Crea un account lab)](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Verrà visualizzata la pagina dell'**account lab** seguente:

    ![Pagina dell'account lab](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Aggiungere un utente al ruolo di creatore di lab
Per fornire ai docenti l'autorizzazione per creare lab per le proprie classi, aggiungerli al ruolo di creatore di lab:

1. Nella pagina **Lab Account** (Account lab) selezionare **Controllo di accesso (IAM)** e fare clic su **+ Aggiungi** sulla barra degli strumenti. 

    ![Pagina dell'account lab](./media/tutorial-setup-lab-account/access-control.png)
2. Nella pagina **Aggiungi autorizzazioni** selezionare **Lab Creator** (Creatore di lab) per **Ruolo**, scegliere l'utente che si vuole aggiungere al ruolo di creatore del lab e fare clic su **Salva**. 

    ![Aggiungere un utente al ruolo di creatore di lab](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>Visualizzare gli account lab
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutte le risorse** dal menu. 
3. Selezionare **Lab Services** per il **tipo**. 
    È anche possibile filtrare per sottoscrizione, gruppo di risorse, posizioni e tag. 

## <a name="delete-a-lab-account"></a>Eliminare un account lab
Seguire le istruzioni della sezione precedente che visualizza gli account lab in un elenco. Per eliminare un account lab seguire questa procedura: 

1. Selezionare l'**account lab** da eliminare. 
2. Selezionare **Elimina** dalla barra degli strumenti. 
3. Digitare **Sì** per confermare.
4. Selezionare **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi
Introduzione alla configurazione di un lab usando Azure Lab Services:

- [Configurare un lab per le classi](tutorial-setup-classroom-lab.md)
- [Configurare un lab personalizzato](tutorial-create-custom-lab.md)
