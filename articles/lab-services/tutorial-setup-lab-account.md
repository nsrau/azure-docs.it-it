---
title: Configurare un account lab con Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un account lab con Azure Lab Services, aggiungere un autore di lab e specificare le immagini del Marketplace da usare nei lab nell'account lab.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: fba4dbc5386407bd796606d86a5b7bdc7c10fd61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445067"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Esercitazione: Configurare un account lab con Azure Lab Services
In Azure Lab Services l'account lab funge da account centrale in cui vengono gestiti tutti i lab dell'organizzazione. Nell'account lab si concedono ad altri utenti le autorizzazioni alla creazione di lab e di impostano criteri che si applicano a tutti i lab all'interno dell'account lab. Questa esercitazione illustra come creare un account lab. 

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Creare un account lab
> * Aggiungere un utente al ruolo di creatore di lab

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-lab-account"></a>Creare un account lab
La procedura seguente illustra come usare il portale di Azure per creare un account lab con Azure Lab Services. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. Selezionare **DevOps** in **Categorie**. Selezionare quindi **Lab Services**. Se si seleziona l'asterisco (`*`) accanto a **Lab Services**, l'opzione verrà aggiunta alla sezione **PREFERITI** nel menu a sinistra. Da questo momento in poi, selezionare **Lab Services** in **PREFERITI**.

    ![Tutti i servizi -> Lab Services](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Nella pagina **Lab Services** selezionare **Aggiungi** sulla barra degli strumenti oppure selezionare il pulsante **Crea un account lab** nella pagina. 

    ![Selezionare Aggiungi nella pagina Account Lab](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Nella scheda **Informazioni di base** della pagina **Crea un account lab** eseguire le operazioni seguenti: 
    1. In **Lab account name** (Nome account lab) immettere un nome. 
    2. Selezionare la **sottoscrizione di Azure** in cui creare l'account lab.
    3. Per **Gruppo di risorse**, selezionare un gruppo di risorse esistente oppure scegliere **Crea nuovo** e immettere un nome.
    4. Per **Località**, selezionare una località/area in cui creare l'account lab. 

        ![Account lab - pagina Informazioni di base](./media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Selezionare **Rivedi e crea**.
    6. Esaminare il riepilogo, quindi selezionare **Crea**. 

        ![Rivedi e crea -> Crea](./media/tutorial-setup-lab-account/create-button.png)    
5. Una volta completata la distribuzione, espandere **Passaggi successivi** e selezionare **Vai alla risorsa**. 

    ![Passare alla pagina dell'account lab](./media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Verificare che venga visualizzata la pagina **Account lab**. 

    ![Pagina dell'account lab](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Aggiungere un utente al ruolo di creatore di lab
Per configurare un lab per le classi in un account del lab, l'utente deve essere un membro del ruolo **Autore di laboratori** nell'account del lab. Per fornire ai docenti l'autorizzazione per creare lab per le proprie classi, aggiungerli al ruolo **Autore di laboratori**:

> [!NOTE]
> L'account usato per creare l'account del lab viene automaticamente aggiunto a questo ruolo. Se si prevede di usare lo stesso account utente per creare un lab per le classi in questa esercitazione, ignorare questo passaggio. 

1. Nella pagina **Account Lab** selezionare **Controllo di accesso (IAM)** , selezionare **+ Aggiungi** sulla barra degli strumenti e quindi selezionare **+ Aggiungi assegnazione di ruolo** sulla barra degli strumenti. 

    ![Fare clic su Controllo di accesso -> Aggiungi un'assegnazione di ruolo](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Nella pagina **Aggiungi un'assegnazione di ruolo** selezionare **Autore di lab** per **Ruolo**, scegliere l'utente che si vuole aggiungere a questo ruolo e fare clic su **Salva**. 

    ![Aggiungere l'autore del lab](./media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come creare un account lab. Per altre informazioni su come creare un lab per le classi in qualità di docente, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Configurare un lab per le classi](tutorial-setup-classroom-lab.md)

