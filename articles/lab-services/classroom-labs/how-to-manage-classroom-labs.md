---
title: Gestire i lab per le classi in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare e configurare un lab per le classi, visualizzare tutti i lab per le classi, condividere il collegamento di registrazione con un utente del lab o eliminare un lab.
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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: c6ae28e076d14faa7c2173f3a23d92daad4bd59e
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651124"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gestire i lab per le classi in Azure Lab Services 
Questo articolo descrive come creare ed eliminare un lab per le classi, nonché come visualizzare tutti i lab per le classi in un account lab. 

## <a name="prerequisites"></a>Prerequisiti
Per configurare un lab per le classi in un account del lab, è necessario essere un membro del ruolo **Autore di laboratori** nell'account del lab. L'account usato per creare un account del lab viene aggiunto automaticamente a questo ruolo. Un proprietario del lab può aggiungere altri utenti al ruolo Autore di laboratori seguendo i passaggi descritti nell'articolo seguente: [Aggiungere un utente al ruolo di creatore di lab](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). 
2. Fare clic su **Accedi**. Selezionare o immettere un **ID utente** appartenente al ruolo **Autore di laboratori** nell'account lab e quindi immettere la password. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab. 
    2. Specificare il **numero massimo di macchine virtuali** nel lab. È possibile aumentare o ridurre il numero di macchine virtuali nel lab in un secondo momento. 
    6. Selezionare **Salva**.

        ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Nella pagina **Select virtual machine specifications** (Seleziona le specifiche delle macchine virtuali) eseguire le operazioni seguenti:
    1. Selezionare le **dimensioni** per le macchine virtuali create nel lab. Attualmente **piccola**, **medium**, **grandi**, e **GPU** dimensioni consentite.
    2. Selezionare l'**area** in cui si vogliono creare le macchine virtuali. 
    3. Selezionare l'**immagine della macchina virtuale** da usare per creare macchine virtuali nel lab. Se si seleziona un'immagine Linux, viene visualizzata un'opzione per abilitare connessione desktop remoto per tale. Per informazioni dettagliate, vedere [abilitare connessione desktop remoto per Linux](how-to-enable-remote-desktop-linux.md).
    4. Selezionare **Avanti**.

        ![Immettere le specifiche delle macchine virtuali](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Nella pagina **Set credentials** (Imposta le credenziali) specificare le credenziali predefinite per tutte le macchine virtuali del lab. 
    1. Specificare il **nome dell'utente** per tutte le macchine virtuali del lab.
    2. Specificare la **password** dell'utente. 

        > [!IMPORTANT]
        > Prendere nota del nome utente e della password perché non verranno più visualizzati.
    3. Selezionare **Create**. 

        ![Impostare le credenziali](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Nella pagina **Configure template** (Configura modello) viene visualizzato lo stato del processo di creazione del lab. La creazione del modello nel lab richiede fino a 20 minuti. Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale modello con tutte le caratteristiche che si vuole fornire agli utenti del lab.  

    ![Configurare il modello](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Dopo aver completato la configurazione del modello verrà visualizzata la pagina seguente: 

    ![Pagina di configurazione del modello al termine dell'operazione](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. I passaggi seguenti sono facoltativi in questa esercitazione: 
    1. Avviare la macchina virtuale modello selezionando **Start** (Avvia).
    2. Connettersi alla macchina virtuale modello selezionando **Connect** (Connetti). Se è un modello Linux VM, è scegliere se si desidera connettersi tramite RDP o SSH (se RDP è abilitato).
    3. Installare e configurare il software nella macchina virtuale modello. 
    4. **Arrestare** la VM.  
    5. Immettere una **descrizione** per il modello.

        ![Pulsante Next (Avanti) nella pagina di configurazione del modello](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Selezionare **Next** (Avanti) nella pagina del modello. 
10. Nella pagina **Publish the template** (Pubblica il modello) eseguire le azioni seguenti. 
    1. Per pubblicare immediatamente il modello, selezionare la casella di controllo *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Sono consapevole che il modello non sarà modificabile dopo la pubblicazione. Questo processo può essere eseguito una sola volta e richiede fino a un'ora), quindi selezionare **Publish** (Pubblica).  Pubblicare il modello per rendere disponibili agli utenti del lab le istanze della macchina virtuale modello.

        > [!WARNING]
        > L'operazione di pubblicazione non può essere annullata. 
    2. Per eseguire la pubblicazione in un secondo momento, selezionare **Save for later** (Salva per dopo). È possibile pubblicare la macchina virtuale modello al termine della procedura guidata. Per informazioni dettagliate su come eseguire la configurazione e la pubblicazione al termine della procedura guidata, consultare la sezione Pubblicare il modello dell'articolo [Gestire i lab per le classi](how-to-manage-classroom-labs.md).

        ![Pubblicare il modello](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Al termine dell'operazione di pubblicazione del modello, viene visualizzata la pagina seguente. Selezionare **Operazione completata**.

    ![Pubblicazione del modello - operazione completata](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verrà visualizzato il **dashboard** del lab. 
    
    ![Dashboard del lab per le classi](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Passare alla pagina **Macchine virtuali** e verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="view-all-classroom-labs"></a>Visualizzare tutti i lab per le classi
1. Accedere al [portale di Azure Lab Services](https://labs.azure.com).
2. Fare clic su **Accedi**. Selezionare o immettere un **ID utente** appartenente al ruolo **Autore di laboratori** nell'account lab e quindi immettere la password. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Confermare che sia possibile visualizzare tutti i lab dell'account del lab selezionato. 

    ![Tutti i lab](../media/how-to-manage-classroom-labs/all-labs.png)
3. Dall'elenco a discesa nella parte superiore, selezionare un account del lab differente. Tutti i lab dell'account selezionato sono visibili. 

## <a name="delete-a-classroom-lab"></a>Eliminare un lab per le classi
1. Nel riquadro del lab, selezionare l'icona con tre punti (...) nell'angolo. 

    ![Selezionare il lab](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Selezionare **Elimina**. 

    ![Pulsante Elimina](../media/how-to-manage-classroom-labs/delete-button.png)
3. Nella finestra di dialogo **Delete lab**(Elimina lab), selezionare **Elimina**. 

    ![Finestra di dialogo Elimina](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Passare a un altro lab per le classi
Per passare a un altro lab per le classi, in quello corrente selezionare l'elenco a discesa di lab nell'account del lab nella parte superiore.

![Selezionare il lab dall'elenco a discesa nella parte superiore](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)

