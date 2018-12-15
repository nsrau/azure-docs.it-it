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
ms.date: 11/15/2018
ms.author: spelluru
ms.openlocfilehash: 20412efac553458f3028f873bcc6d918a673f261
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838818"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gestire account lab in Azure Lab Services 
In Azure Lab Services, un account lab è un contenitore per lab gestiti, ad esempio, laboratori di classe. Un amministratore configura un account lab con Azure Lab Services e fornisce l'accesso ai proprietari del lab autorizzati a creare lab nell'account. Questo articolo descrive come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab.

## <a name="create-a-lab-account"></a>Creare un account lab
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Creare una risorsa** dal menu principale a sinistra.
3. Cercare **Lab Services** in Azure Marketplace e quindi selezionare **Lab Services** nell'elenco a discesa. 
4. Selezionare **Lab Services (Preview)** nell'elenco filtrato dei servizi. 
5. Nella finestra **Create a lab account** (Crea un account lab) selezionare **Crea**.
7. Nella finestra **Lab account** (Account lab) eseguire queste operazioni: 
    1. In **Lab account name** (Nome account lab) immettere un nome. 
    2. Selezionare la **sottoscrizione di Azure** in cui creare l'account lab.
    3. In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
    4. In **Posizione** selezionare una posizione o un'area in cui si vuole creare l'account lab. 
    5. Selezionare **Create**. 

        ![Finestra Create a lab account (Crea un account lab)](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Se non viene visualizzata la pagina dell'account lab, selezionare il pulsante delle **notifiche** e quindi fare clic su **Vai alla risorsa** in Notifiche. 

    ![Finestra Create a lab account (Crea un account lab)](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Verrà visualizzata la pagina dell'**account lab** seguente:

    ![Pagina dell'account lab](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Aggiungere un utente al ruolo di creatore di lab
Per configurare un lab per le classi in un account del lab, l'utente deve essere un membro del ruolo **Autore di laboratori** nell'account del lab. L'account usato per creare l'account del lab viene automaticamente aggiunto a questo ruolo. Se si prevede di usare lo stesso account utente per creare un lab per le classi, è possibile ignorare questo passaggio. Per usare un altro account utente per creare un lab per le classi, procedere come segue: 

1. Nella pagina **Account Lab** selezionare **Controllo di accesso (IAM)** e fare clic su **+ Aggiungi assegnazione di ruolo** sulla barra degli strumenti. 
2. Nella pagina **Aggiungi autorizzazioni** selezionare **Lab Creator** (Creatore di lab) per **Ruolo**, scegliere l'utente che si vuole aggiungere al ruolo di creatore del lab e fare clic su **Salva**.

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Specificare le immagini del Marketplace disponibili per i proprietari del lab
In quanto proprietario di un account del lab, è possibile specificare le immagini del Marketplace che gli autori di lab possono usare per creare lab nell'account del lab. 

1. Selezionare **Immagini del Marketplace** nel menu a sinistra. Per impostazione predefinita, verrà visualizzato l'elenco completo delle immagini (abilitate e disabilitate). È possibile filtrare l'elenco per visualizzare solo le immagini abilitate o disabilitate selezionando l'opzione **Enabled only**/**Disabled only** (Solo abilitate, Solo disabilitate) nell'elenco a discesa nella parte superiore. 
    
    ![Pagina Immagini del Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Nell'elenco vengono visualizzate solo le immagini del Marketplace che soddisfano le condizioni seguenti:
        
    - Crea una singola macchina virtuale.
    - Usa Azure Resource Manager per il provisioning di macchine virtuali
    - Non richiede l'acquisto di un piano di licenze aggiuntivo
2. Per **disabilitare** un'immagine del Marketplace che è stata abilitata, eseguire una delle azioni seguenti: 
    1. Selezionare **... (puntini di sospensione)** nell'ultima colonna e selezionare **Disable image** (Disabilita immagine). 

        ![Disabilitare un'immagine](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selezionare una o più immagini nell'elenco selezionando le caselle di controllo prima dei nomi delle immagini nell'elenco e selezionare **Disable selected images** (Disabilita immagini selezionate). 

        ![Disabilitare più immagini](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Analogamente, per **abilitare** un'immagine del Marketplace, eseguire una delle azioni seguenti: 
    1. Selezionare **... (puntini di sospensione)** nell'ultima colonna e selezionare **Enable image** (Abilita immagine). 
    2. Selezionare una o più immagini nell'elenco selezionando le caselle di controllo prima dei nomi delle immagini nell'elenco e selezionare **Enable selected images** (Abilita immagini selezionate). 

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

## <a name="view-and-manage-labs-in-the-lab-account"></a>Visualizzare e gestire i lab nell'account lab

1. Nella pagina **Account lab** selezionare **Lab** nel menu a sinistra.

    ![Lab nell'account](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Viene visualizzato un **elenco di lab** nell'account con le informazioni seguenti: 
    1. Nome del lab.
    2. Data di creazione del lab. 
    3. Indirizzo di posta elettronica dell'utente che ha creato il lab. 
    4. Numero massimo di utenti consentiti nel lab. 
    5. Stato del lab. 

## <a name="delete-a-lab-in-the-lab-account"></a>Eliminare un lab nell'account lab
Per visualizzare un elenco dei lab nell'account lab, seguire le istruzioni nella sezione precedente.

1. Selezionare **...** (puntini di sospensione) e quindi **Elimina**. 

    ![Pulsante Elimina un lab](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Selezionare **Sì** nel messaggio di avviso. 



## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)