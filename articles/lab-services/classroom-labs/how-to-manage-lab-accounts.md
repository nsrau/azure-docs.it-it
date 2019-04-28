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
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: f1194d8385d1e7ddcb906d0c8c3a2b56648e2547
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60696345"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gestire account lab in Azure Lab Services 
In Azure Lab Services, un account del lab è un contenitore per i tipi di lab gestiti, ad esempio labs in aula. Un amministratore configura un account lab con Azure Lab Services e fornisce l'accesso ai proprietari del lab autorizzati a creare lab nell'account. Questo articolo descrive come creare un account lab, visualizzare tutti gli account lab o eliminare un account lab.

## <a name="create-a-lab-account"></a>Creare un account lab
La procedura seguente illustra come usare il portale di Azure per creare un account lab con Azure Lab Services. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. Selezionare **Account Lab** nella sezione **DEVOPS**. Se si seleziona l'asterisco (`*`) accanto ad **Account Lab**, l'opzione verrà aggiunta alla sezione **PREFERITI** nel menu a sinistra. Da questo momento in poi, selezionare **Account Lab** in **PREFERITI**.

    ![Tutti i servizi -> Account Lab](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Nella pagina **Account Lab** selezionare **Aggiungi** sulla barra degli strumenti. 

    ![Selezionare Aggiungi nella pagina Account Lab](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Nella pagina **Account Lab** seguire questa procedura: 
    1. In **Lab account name** (Nome account lab) immettere un nome. 
    2. Selezionare la **sottoscrizione di Azure** in cui creare l'account lab.
    3. In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
    4. In **Posizione** selezionare una posizione o un'area in cui si vuole creare l'account lab. 
    5. Per **Rete virtuale peer** selezionare una rete virtuale peer per la rete lab. I lab creati in questo account sono connessi alla rete virtuale selezionata e hanno accesso alle risorse nella rete virtuale selezionata. 
    7. Per il campo **Consenti all'autore del lab di selezionare la località del lab**, specificare se si vuole consentire agli autori di selezionare una località per il lab. L'opzione è disabilitata per impostazione predefinita. Quando l'opzione è disattivata, gli autori del lab non possono specificare una località per il lab che si sta creando. I lab vengono creati nella località geografica più vicina all'account lab. Quando l'opzione è abilitata, un autore può selezionare una località al momento della creazione di un lab.      
    8. Selezionare **Create**. 

        ![Finestra Create a lab account (Crea un account lab)](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Selezionare l'**icona del campanello** sulla barra degli strumenti (**Notifiche**), verificare se la distribuzione è riuscita e quindi selezionare **Vai alla risorsa**. 

    In alternativa, selezionare **Aggiorna** nella pagina **Account Lab** e selezionare l'account lab creato. 

    ![Finestra Create a lab account (Crea un account lab)](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Verrà visualizzata la pagina dell'**account lab** seguente:

    ![Pagina dell'account lab](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Aggiungere un utente al ruolo di creatore di lab
Per configurare un lab per le classi in un account del lab, l'utente deve essere un membro del ruolo **Autore di laboratori** nell'account del lab. L'account usato per creare l'account del lab viene automaticamente aggiunto a questo ruolo. Se si prevede di usare lo stesso account utente per creare un lab per le classi, è possibile ignorare questo passaggio. Per usare un altro account utente per creare un lab per le classi, procedere come segue: 

Per fornire ai docenti l'autorizzazione per creare lab per le proprie classi, aggiungerli al ruolo **Autore di laboratori**:

1. Nella pagina **Account Lab** selezionare **Controllo di accesso (IAM)** e fare clic su **+ Aggiungi assegnazione di ruolo** sulla barra degli strumenti. 

    ![Fare clic su Controllo di accesso -> Aggiungi un'assegnazione di ruolo](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Nella pagina **Aggiungi un'assegnazione di ruolo** selezionare **Autore di lab** per **Ruolo**, scegliere l'utente che si vuole aggiungere a questo ruolo e fare clic su **Salva**. 

    ![Aggiungere l'autore del lab](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="specify-marketplace-images-available-to-lab-creators"></a>Specificare le immagini del Marketplace disponibili per gli autori di lab
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

## <a name="configure-the-lab-account"></a>Configurare l'account del lab
1. Nel **Account del Lab** pagina, selezionare **configurazione Labs** nel menu a sinistra.

    ![Pagina di configurazione del lab](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Per la **rete virtuale Peer**, selezionare **Enabled** oppure **disabilitato**. Il valore predefinito è **disabilitato**. Per abilitare la rete virtuale peer, procedere come segue: 
    1. Selezionare **Enabled**.
    2. Selezionare il **VNet** nell'elenco a discesa. 
    3. Sulla barra degli strumenti selezionare **Salva**. 
    
        Lab creato in questo account connessi alla rete virtuale selezionata. Possono accedere alle risorse nella rete virtuale selezionata. 
3. Per il **Consenti creatore di lab selezionare percorsi lab**, selezionare **abilitato** se si desidera che il creatore di lab sia in grado di selezionare un percorso per l'ambiente lab. Se è disabilitato, i laboratori vengono creati automaticamente nello stesso percorso in cui si trova l'account del lab. 

## <a name="view-lab-accounts"></a>Visualizzare gli account lab
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutte le risorse** dal menu. 
3. Selezionare **Account Lab** per il **tipo**. 
    È anche possibile filtrare per sottoscrizione, gruppo di risorse, posizioni e tag. 

    ![Tutte le risorse -> Account Lab](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

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

    ![Conferma dell'eliminazione del lab](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Eliminare un account lab
Seguire le istruzioni della sezione precedente che visualizza gli account lab in un elenco. Per eliminare un account lab seguire questa procedura: 

1. Selezionare l'**account lab** da eliminare. 
2. Selezionare **Elimina** dalla barra degli strumenti. 

    ![Account Lab -> Pulsante Elimina](../media/how-to-manage-lab-accounts/delete-button.png)
1. Digitare **Sì** per confermare.
1. Selezionare **Elimina**. 

    ![Eliminazione dell'account lab - Conferma](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)



## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)