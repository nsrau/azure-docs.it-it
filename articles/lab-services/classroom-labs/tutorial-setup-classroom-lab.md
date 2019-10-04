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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 964ecca015e440439885bbbd85cb720a3abd10a9
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883506"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Esercitazione: Configurare un lab per le classi 
Questa esercitazione descrive come configurare un lab per le classi con macchine virtuali usate dagli studenti nella classe.  

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Creare un lab per le classi
> * Aggiungere utenti al lab
> * Inviare un collegamento di registrazione agli studenti

## <a name="prerequisites"></a>Prerequisiti
Per configurare un lab per le classi in un account del lab, è necessario essere un membro dei ruoli seguenti nell'account del lab: Proprietario, Autore di laboratori o Collaboratore. L'account usato per creare un account del lab viene aggiunto automaticamente al ruolo di Proprietario.

Un proprietario del lab può aggiungere altri utenti al ruolo **Autore di laboratori**. Ad esempio, un proprietario del lab può aggiungere docenti al ruolo Autore di laboratori. Quindi, i docenti creano lab con macchine virtuali per le classi. Gli studenti usano il collegamento di registrazione inviato dai docenti per registrarsi al lab. Una volta registrati, potranno usare le macchine virtuali nei lab per eseguire le attività in classe e a casa. Per istruzioni dettagliate sull'aggiunta di utenti al ruolo di Autore di laboratori, vedere [Aggiungere un utente al ruolo di creatore di lab](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
2. Selezionare **Accedi** e immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab. 
    2. Specificare il **numero massimo di macchine virtuali** nel lab. È possibile aumentare o ridurre il numero di macchine virtuali dopo aver creato il lab o in un lab esistente. Per altre informazioni, vedere [Aggiornare il numero di VM in un lab](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Selezionare **Salva**.

        ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Nella pagina **Select virtual machine specifications** (Seleziona le specifiche delle macchine virtuali) eseguire le operazioni seguenti:
    1. Selezionare le **dimensioni** per le macchine virtuali create nel lab. Attualmente, sono consentite dimensioni **piccole**, **medie**, **medie (virtualizzazione)** , **grandi** e **GPU**.
    3. Selezionare l'**immagine della macchina virtuale** da usare per creare macchine virtuali nel lab. Se si seleziona un'immagine di Linux, viene visualizzata un'opzione per abilitare la connessione Desktop remoto. Per informazioni, vedere [Abilitare Connessione desktop remoto per Linux](how-to-enable-remote-desktop-linux.md).
    4. Selezionare **Avanti**.

        ![Immettere le specifiche delle macchine virtuali](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Nella pagina **Set credentials** (Imposta le credenziali) specificare le credenziali predefinite per tutte le macchine virtuali del lab. 
    1. Specificare il **nome dell'utente** per tutte le macchine virtuali del lab.
    2. Specificare la **password** dell'utente. 

        > [!IMPORTANT]
        > Prendere nota del nome utente e della password perché non verranno più visualizzati.
    3. Selezionare **Create** (Crea). 

        ![Impostare le credenziali](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Nella pagina **Configure template** (Configura modello) viene visualizzato lo stato del processo di creazione del lab. La creazione del modello nel lab richiede fino a 20 minuti. 

    ![Configurare il modello](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Dopo aver completato la configurazione del modello verrà visualizzata la pagina seguente: 

    ![Pagina di configurazione del modello al termine dell'operazione](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Nella pagina **Configure template** (Configura modello) procedere come segue: Questi passaggi sono **facoltativi** per l'esercitazione.
    2. Connettersi alla macchina virtuale modello selezionando **Connect** (Connetti). Se si tratta di una macchina virtuale modello di Linux, scegliere se si vuole effettuare la connessione con SSH o RDP (se RDP è abilitato).
    1. Selezionare **Reset password** (Reimposta password) per reimpostare la password per la macchina virtuale. 
    1. Installare e configurare il software nella macchina virtuale modello. 
    1. **Arrestare** la VM.  
    1. Immettere una **descrizione** per il modello.
9. Selezionare **Next** (Avanti) nella pagina del modello. 
10. Nella pagina **Publish the template** (Pubblica il modello) eseguire le azioni seguenti. 
    1. Per pubblicare il modello immediatamente, selezionare **Publish** (Pubblica).  

        > [!WARNING]
        > L'operazione di pubblicazione non può essere annullata. 
    2. Per eseguire la pubblicazione in un secondo momento, selezionare **Save for later** (Salva per dopo). È possibile pubblicare la macchina virtuale modello al termine della procedura guidata. Per informazioni dettagliate su come configurare e pubblicare il modello al termine della procedura guidata, vedere la sezione [Pubblicare il modello](how-to-create-manage-template.md#publish-the-template-vm) nell'articolo [Gestire i lab per le classi in Azure Lab Services](how-to-manage-classroom-labs.md).

        ![Pubblicare il modello](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Al termine dell'operazione di pubblicazione del modello, viene visualizzata la pagina seguente. Selezionare **Operazione completata**.

    ![Pubblicazione del modello - operazione completata](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verrà visualizzato il **dashboard** del lab. 
    
    ![Dashboard del lab per le classi](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Per passare alla pagina **Macchine virtuali** selezionare Macchine virtuali nel menu a sinistra oppure il riquadro Macchine virtuali. Verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Aggiungere utenti al lab

1. Selezionare **Utenti** nel menu a sinistra. Per impostazione predefinita, l'opzione **Limita l'accesso** è abilitata. Quando questa impostazione è attiva, un utente non può registrarsi al lab anche se ha il collegamento di registrazione, a meno che non sia presente nell'elenco degli utenti. Solo gli utenti nell'elenco possono registrarsi al lab utilizzando il collegamento di registrazione inviato. In questa procedura si aggiungono utenti all'elenco. In alternativa, è possibile disattivare **Limita l'accesso**, consentendo agli utenti di registrarsi al lab alla sola condizione di disporre del collegamento di registrazione. 
2. Selezionare **Aggiungi utenti** sulla barra degli strumenti. 

    ![Pulsante Aggiungi utenti](../media/how-to-configure-student-usage/add-users-button.png)
1. Nella pagina **Aggiungi utenti** immettere gli indirizzi di posta elettronica degli utenti in righe separate o in una singola riga, separati da punti e virgola. 

    ![Aggiungere gli indirizzi di posta elettronica degli utenti](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selezionare **Salva**. Gli indirizzi di posta elettronica degli utenti e i relativi stati (registrati o no) saranno visualizzati nell'elenco. 

    ![Elenco utenti](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Impostare quote per gli utenti
È possibile impostare quote per utente usando la procedura seguente: 

1. Selezionare **Users** (Utenti) nel menu a sinistra se la pagina non è già attiva. 
2. Selezionare **Quota per user (Quota per utente): 10 hours** (10 ore) sulla barra degli strumenti. 
3. Nella pagina **Quote per user** (Quota per utente) specificare il numero di ore da assegnare a ogni utente (studente): 
    1. **Total number of lab hours per user** (Numero totale di ore lab per utente). Gli utenti possono usare le proprie macchine virtuali per il numero di ore specificato (indicato in questo campo), **in aggiunta all'orario pianificato**. Se si seleziona questa opzione, immettere il **numero di ore** nella casella di testo. 

        ![Numero di ore per utente](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 hours (schedule only)** (0 ore - solo pianificazione). Gli utenti possono usare le proprie macchine virtuali durante l'orario pianificato oppure quando il proprietario del lab attiva le macchine virtuali per loro.

        ![Zero ore - solo orario pianificato](../media/how-to-configure-student-usage/zero-hours.png)
    4. Selezionare **Salva**. 
5. Nella barra degli strumenti verranno visualizzati ora i valori modificati: **Quota per user (Quota per utente): &lt;numero di ore&gt;** . 

    ![Quota per user (Quota per utente)](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>Impostare una pianificazione per il lab
Se è stata configurata per l'impostazione della quota l'opzione **0 hours (schedule only)** (0 ore, solo pianificazione), è necessario impostare una pianificazione per il lab. In questa esercitazione viene impostata una pianificazione settimanale ricorrente.

1. Passare alla pagina **Pianificazioni** e selezionare **Aggiungi pianificazione** sulla barra degli strumenti. 

    ![Pulsante Aggiungi pianificazione nella pagina Pianificazioni](../media/how-to-create-schedules/add-schedule-button.png)
2. Nella pagina **Aggiungi pianificazione** passare a **Settimanale** nella parte superiore. 
3. In **Schedule days (required)** (Giorni pianificazione - obbligatori) selezionare i giorni in cui la pianificazione deve essere effettiva. Nell'esempio seguente sono selezionati i giorni compresi tra lunedì e venerdì. 
4. Nel campo **Da** immettere la **data di inizio della pianificazione** o selezionare una data facendo clic sul pulsante del **calendario**. Questo campo è obbligatorio. 
5. In **Schedule end date** (Data fine pianificazione) immettere o selezionare una data di fine in cui arrestare le macchine virtuali. 
6. In **Ora di inizio** selezionare l'ora in cui le macchine virtuali devono essere avviate. L'ora di inizio è obbligatoria se non è impostata l'ora di arresto. Selezionare **Remove start event** (Rimuovi evento di avvio) per specificare solo l'ora di arresto. Se l'**ora di inizio** è disabilitata, selezionare **Add start event** (Aggiungi evento di avvio) accanto all'elenco a discesa per abilitarla. 
7. In **Ora di arresto** selezionare l'ora in cui le macchine virtuali devono arrestate. L'ora di arresto è obbligatoria se non è impostata l'ora di inizio. Selezionare **Remove stop event** (Rimuovi evento di arresto) per specificare solo l'ora di arresto. Se l'**ora di arresto** è disabilitata, selezionare **Add stop event** (Aggiungi evento di arresto) accanto all'elenco a discesa per abilitarla.
8. In **Time zone (required)** (Fuso orario - obbligatorio) selezionare il fuso orario per le ore di inizio e di arresto specificate.  
9. In **Note** immettere la descrizione o le note per la pianificazione. 
10. Selezionare **Salva**. 

    ![Pianificazione settimanale](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>Inviare un messaggio di posta elettronica con il collegamento per la registrazione

1. Passare alla vista **Utenti** se non si è già nella pagina. 
2. Selezionare utenti specifici o tutti gli utenti nell'elenco. Per selezionare utenti specifici, selezionare le caselle di controllo nella prima colonna dell'elenco. Per selezionare tutti gli utenti, selezionare la casella di controllo accanto al titolo della prima colonna (**Nome**) oppure tutte le caselle di controllo per tutti gli utenti nell'elenco. È possibile visualizzare lo **stato dell'invito** in questo elenco.  Nella figura seguente lo stato dell'invito per tutti gli studenti è impostato su **Invito non inviato**. 

    ![Selezionare gli studenti](../media/tutorial-setup-classroom-lab/select-students.png)
1. Selezionare l'**icona del messaggio di posta elettronica (busta)** in una delle righe oppure selezionare **Invia invito** sulla barra degli strumenti. È anche possibile passare il puntatore del mouse sul nome di uno studente nell'elenco e quindi selezionare l'icona del messaggio di posta elettronica. 

    ![Inviare un collegamento per la registrazione tramite posta elettronica](../media/tutorial-setup-classroom-lab/send-email.png)
4. Nella pagina **Send registration link by email** (Invia collegamento registrazione per posta elettronica) seguire questa procedura: 
    1. Digitare un **messaggio facoltativo** da inviare agli studenti. Il messaggio di posta elettronica include automaticamente il collegamento per la registrazione. 
    2. Nella pagina **Send registration link by email** (Invia collegamento registrazione per posta elettronica) selezionare **Invia**. Lo stato dell'invito cambia in **Invio dell'invito** e quindi in **Invito inviato**. 
        
        ![Inviti inviati](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come creare un lab per le classi e come configurare il lab. Per informazioni su come uno studente può accedere a una VM nel lab usando il collegamento di registrazione, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Connettersi a una VM nel lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)

