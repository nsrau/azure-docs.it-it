---
title: Configurare un lab per le classi con Azure Lab Services | Microsoft Docs
description: Questa esercitazione descrive come configurare un lab da usare in una classe.
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
ms.date: 10/05/2018
ms.author: spelluru
ms.openlocfilehash: 6696d6e7e53e98dfab2a65c7c66825936020f33c
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856639"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Esercitazione: Configurare un lab per le classi 
Questa esercitazione descrive come configurare un lab per le classi con macchine virtuali usate dagli studenti nella classe.  

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Creare un lab per le classi
> * Configurare il lab per le classi
> * Inviare un collegamento di registrazione agli studenti

## <a name="prerequisites"></a>Prerequisiti
Per configurare un lab per le classi in un account del lab, è necessario essere un membro del ruolo **Autore di lab** nell'account del lab. L'account usato per creare un account del lab viene aggiunto automaticamente a questo ruolo. Un proprietario del lab può aggiungere altri utenti al ruolo Autore di laboratori seguendo i passaggi nell'articolo seguente: [Aggiungere un utente al ruolo di Autore di laboratori](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). 
2. Selezionare **Accedi** e immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab. 
    2. Specificare il **numero di utenti** massimo consentito nel lab. 
    6. Selezionare **Salva** (Save).

        ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Nella pagina **Select virtual machine specifications** (Seleziona le specifiche delle macchine virtuali) eseguire le operazioni seguenti:
    1. Selezionare le **dimensioni** per le macchine virtuali create nel lab. 
    2. Selezionare l'**area** in cui si vogliono creare le macchine virtuali. 
    3. Selezionare l'**immagine della macchina virtuale** da usare per creare macchine virtuali nel lab. 
    4. Selezionare **Next** (Avanti).

        ![Immettere le specifiche delle macchine virtuali](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Nella pagina **Set credentials** (Imposta le credenziali) specificare le credenziali predefinite per tutte le macchine virtuali del lab. 
    1. Specificare il **nome dell'utente** per tutte le macchine virtuali del lab.
    2. Specificare la **password** dell'utente. 

        > [!IMPORTANT]
        > Prendere nota del nome utente e della password perché non verranno più visualizzati.
    3. Selezionare **Create**. 

        ![Impostare le credenziali](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Nella pagina **Configure template** (Configura modello) viene visualizzato lo stato del processo di creazione del lab. La creazione del modello nel lab richiede fino a 20 minuti. 

    ![Configurare il modello](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Dopo aver completato la configurazione del modello verrà visualizzata la pagina seguente: 

    ![Pagina di configurazione del modello al termine dell'operazione](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. I passaggi seguenti sono facoltativi in questa esercitazione: 
    1. Avviare la macchina virtuale modello selezionando **Start** (Avvia).
    2. Connettersi alla macchina virtuale modello selezionando **Connect** (Connetti). 
    3. Installare e configurare il software nella macchina virtuale modello. 
    4. **Arrestare** la VM.  
    5. Immettere una **descrizione** per il modello.

        ![Pulsante Next (Avanti) nella pagina di configurazione del modello](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Selezionare **Next** (Avanti) nella pagina del modello. 
10. Nella pagina **Publish the template** (Pubblica il modello) eseguire le azioni seguenti. 
    1. Per pubblicare immediatamente il modello, selezionare la casella di controllo *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Sono consapevole che il modello non sarà modificabile dopo la pubblicazione. Questo processo può essere eseguito una sola volta e richiede fino a un'ora), quindi selezionare **Publish** (Pubblica).  

        > [!WARNING]
        > L'operazione di pubblicazione non può essere annullata. 
    2. Per eseguire la pubblicazione in un secondo momento, selezionare **Save for later** (Salva per dopo). È possibile pubblicare la macchina virtuale modello al termine della procedura guidata. Per informazioni dettagliate su come configurare e pubblicare il modello al termine della procedura guidata, vedere la sezione [Pubblicare il modello](how-to-manage-classroom-labs.md#publish-the-template) nell'articolo [Gestire i lab per le classi in Azure Lab Services](how-to-manage-classroom-labs.md).

        ![Pubblicare il modello](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Al termine dell'operazione di pubblicazione del modello, viene visualizzata la pagina seguente. Selezionare **Done** (Fine).

    ![Pubblicazione del modello - operazione completata](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verrà visualizzato il **dashboard** del lab. 
    
    ![Dashboard del lab per le classi](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Passare alla pagina **Virtual machines** (Macchine virtuali) e verificare che vengano visualizzate macchine virtuali con lo stato **Unassigned** (Non assegnato). Queste macchine virtuali non sono ancora assegnate agli studenti. Devono essere nello stato **Stopped** (Arrestato). In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>Inviare un collegamento di registrazione agli studenti

1. Passare alla visualizzazione **Dashboard** selezionando **Dashboard** nel menu a sinistra. 
2. Selezionare il riquadro **User registration** (Registrazione utente).

    ![Collegamento di registrazione dello studente](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Copia**. Il collegamento viene copiato negli Appunti. Incollarlo in un editor di posta elettronica e inviare un messaggio di posta elettronica allo studente. 

    ![Collegamento di registrazione dello studente](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Close** (Chiudi). 
3. Condividere il collegamento di registrazione con uno studente in modo che possa eseguire la registrazione alla classe. 


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come creare un lab per le classi e come configurare il lab. Per informazioni su come uno studente può accedere a una VM nel lab usando il collegamento di registrazione, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Connettersi a una VM nel lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)

