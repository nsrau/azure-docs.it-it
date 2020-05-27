---
title: Configurare un lab per le classi con Azure Lab Services | Microsoft Docs
description: In questa esercitazione si usa Azure Lab Services per configurare un lab per le classi con macchine virtuali usate dagli studenti nella classe.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: b3a9c95233c2016dc35f4fcade3e2634e121d252
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591778"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Esercitazione: Configurare un lab per le classi 
Questa esercitazione descrive come configurare un lab per le classi con macchine virtuali usate dagli studenti nella classe.  

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Creare un lab per le classi
> * Aggiungere utenti al lab
> * Impostare una pianificazione per il lab
> * Inviare inviti agli studenti tramite posta elettronica

## <a name="prerequisites"></a>Prerequisiti
In questa esercitazione viene configurato un lab con macchine virtuali per la classe. Per configurare un lab per le classi in un account del lab, è necessario essere un membro dei ruoli seguenti nell'account del lab: Proprietario, Autore di laboratori o Collaboratore. L'account usato per creare un account del lab viene aggiunto automaticamente al ruolo di Proprietario. Quindi, è possibile usare l'account utente usato per creare un account lab per creare un lab per le classi. 

Ecco un tipico flusso di lavoro quando si usa Azure Lab Services:

1. Un autore di account lab aggiunge altri utenti al ruolo **Autore di laboratori**. Ad esempio, l'autore/amministratore dell'account lab aggiunge docenti al ruolo di **Autore del laboratorio** in modo che possano creare lab per le classi. 
2. I docenti creano quindi lab con macchine virtuali per le proprie classi e inviano collegamenti di registrazione agli studenti. 
3. Gli studenti usano il collegamento di registrazione ricevuto dai docenti per registrarsi al lab. Una volta registrati, potranno usare le macchine virtuali nei lab per eseguire le attività in classe e a casa. 

## <a name="create-a-classroom-lab"></a>Creare un lab per le classi
In questo passaggio viene creato un lab per le classi in Azure. 

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
2. Selezionare **Accedi** e immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Selezionare **New lab** (Nuovo lab). 
    
    ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab e selezionare **Avanti**.  

        ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Nella pagina **Virtual machine credentials** (Credenziali macchina virtuale) specificare le credenziali predefinite per tutte le VM del lab. Specificare il **nome** e la **password** per l'utente, quindi selezionare **Avanti**.  

        ![Finestra New lab](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Prendere nota del nome utente e della password perché non verranno più visualizzati.
    3. Nella pagina **Criteri del lab** selezionare **Fine**. 

        ![Quota per ogni utente](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Dovrebbe essere visualizzata la schermata seguente, che mostra lo stato di creazione della VM modello. Questa operazione richiede fino a 20 minuti. 

    ![Stato di creazione della VM modello](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Nella pagina **Template** (Modello) procedere come segue: Questi passaggi sono **facoltativi** per l'esercitazione.

    1. Connettersi alla macchina virtuale modello selezionando **Connect** (Connetti). Se si tratta di una macchina virtuale modello di Linux, scegliere se si vuole effettuare la connessione con SSH o RDP (se RDP è abilitato).
    3. Installare e configurare il software necessario per la classe nella macchina virtuale modello. 
    4. **Arrestare** la macchina virtuale modello.  

## <a name="publish-the-template-vm"></a>Pubblicare la macchina virtuale modello
In questo passaggio verrà pubblicata la VM modello. Quando si pubblica la macchina virtuale modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Tutte le macchine virtuali hanno la stessa configurazione del modello.

1. Sulla barra degli strumenti della pagina **Modello** selezionare **Pubblica**. 

    ![Pulsante per la pubblicazione del modello](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > L'operazione di pubblicazione non può essere annullata. 
2. Nella pagina **Publish template** (Pubblica modello) immettere il numero di macchine virtuali da creare nel lab, quindi selezionare **Publish** (Pubblica). 

    ![Pubblicazione del modello: numero di VM](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Nella pagina viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Attendere il completamento della pubblicazione e quindi passare alla pagina **Pool di macchine virtuali** scegliendo **Macchine virtuali** dal menu sinistro oppure selezionando il riquadro **Macchine virtuali**. Verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Quando un docente accende una macchina virtuale per studenti, la quota per lo studente rimane invariata. La quota per un utente specifica il numero di ore lab disponibili per l'utente al di fuori dell'orario delle lezioni pianificato. Per altre informazioni sulle quote, vedere [Impostare quote per gli utenti](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Impostare una pianificazione per il lab
Creare un evento pianificato per il lab in modo che le VM al suo interno vengano automaticamente avviate/arrestate in specifici orari. La quota utente (per impostazione predefinita 10 ore) specificata in precedenza corrisponde al tempo aggiuntivo assegnato a ogni utente al di fuori di questo tempo pianificato. 

1. Passare alla pagina **Schedules** (Pianificazione) e selezionare **Add scheduled event** (Aggiungi evento pianificato) sulla barra degli strumenti. 

    ![Pulsante Aggiungi pianificazione nella pagina Pianificazioni](../media/how-to-create-schedules/add-schedule-button.png)
2. Nella pagina **Add scheduled event** (Aggiungi evento pianificato) seguire questa procedura:
    1. Verificare che per **Event type** (Tipo di evento) sia selezionata l'opzione **Standard**.  
    2. Selezionare la **data di inizio** per la classe. 
    4. Selezionare l'**ora di avvio** in cui si vuole che vengano avviate le VM.
    5. Selezionare l'**ora di arresto** in cui devono essere arrestate le VM. 
    6. Selezionare il **fuso orario** per le ore di avvio e di arresto specificate. 
3. Nella stessa pagina **Add scheduled event** (Aggiungi evento pianificato) selezionare la pianificazione corrente nella sezione **Repeat** (Ripeti).  

    ![Pulsante Aggiungi pianificazione nella pagina Pianificazioni](../media/how-to-create-schedules/select-current-schedule.png)
5. Nella finestra di dialogo **Repeat** (Ripeti) procedere come segue:
    1. Verificare che il campo **Repeat** (Ripeti) sia impostato su **every week** (ogni settimana). 
    2. Selezionare i giorni in cui la pianificazione deve essere effettiva. Nell'esempio seguente sono selezionati i giorni compresi tra lunedì e venerdì. 
    3. Selezionare una **data di fine** per la pianificazione.
    8. Selezionare **Salva**. 

        ![Impostare la pianificazione ripetuta](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Ora nella pagina **Add scheduled event** (Aggiungi evento pianificato) immettere una descrizione per la pianificazione in **Notes (optional)** (Note - facoltativo). 
4. Nella pagina **Add scheduled event** (Aggiungi evento pianificato) selezionare **Save** (Salva). 

    ![Pianificazione settimanale](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Passare alla data di inizio nel calendario per verificare che la pianificazione sia impostata.
    
    ![Pianificare nel calendario](../media/how-to-create-schedules/schedule-calendar.png)

    Per altre informazioni sulla creazione e la gestione di pianificazioni per una classe, vedere [Creare e gestire la pianificazione per i lab per la classe](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Aggiungere utenti al lab

1. Selezionare **Utenti** nel menu a sinistra. Per impostazione predefinita, l'opzione **Limita l'accesso** è abilitata. Quando questa impostazione è attiva, un utente non può registrarsi al lab anche se ha il collegamento di registrazione, a meno che non sia presente nell'elenco degli utenti. Solo gli utenti nell'elenco possono registrarsi al lab utilizzando il collegamento di registrazione inviato. In questa procedura si aggiungono utenti all'elenco. In alternativa, è possibile disattivare **Limita l'accesso**, consentendo agli utenti di registrarsi al lab alla sola condizione di disporre del collegamento di registrazione. 
2. Selezionare **Aggiungi utenti** sulla barra degli strumenti e quindi **Aggiungi con indirizzo di posta elettronica**. 

    ![Pulsante Aggiungi utenti](../media/how-to-configure-student-usage/add-users-button.png)
1. Nella pagina **Aggiungi utenti** immettere gli indirizzi di posta elettronica degli utenti in righe separate o in una singola riga, separati da punti e virgola. 

    ![Aggiungere gli indirizzi di posta elettronica degli utenti](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selezionare **Salva**. Gli indirizzi di posta elettronica degli utenti e i relativi stati (registrati o no) saranno visualizzati nell'elenco. 

    ![Elenco utenti](../media/how-to-configure-student-usage/users-list-new.png)

    Nell'elenco verranno visualizzati i nomi degli utenti dopo la registrazione nel lab. 
    

## <a name="send-invitation-emails-to-users"></a>Inviare inviti agli utenti tramite posta elettronica

1. Passare alla visualizzazione **Users** (Utenti) se la pagina non è già aperta e selezionare **Invite all** (Invita tutti) sulla barra degli strumenti. 

    ![Selezionare gli studenti](../media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Nella pagina **Send invitation by email** (Invia invito per posta elettronica) immettere un messaggio facoltativo e quindi selezionare **Send** (Invia). Il messaggio di posta elettronica include automaticamente il collegamento per la registrazione. Per ottenere questo collegamento di registrazione, selezionare **... (puntini di sospensione)** sulla barra degli strumenti e quindi **Registration link** (Collegamento di registrazione). 

    ![Inviare un collegamento per la registrazione tramite posta elettronica](../media/tutorial-setup-classroom-lab/send-email.png)
4. Lo stato dell'**invito** viene visualizzato nell'elenco **Users** (Utenti). Lo stato dovrebbe cambiare in **Sending** (Invio in corso) e quindi in **Sent on &lt;date&gt;** (Inviato in data). 

    Per altre informazioni sull'aggiunta di studenti a una classe e sulla gestione dell'utilizzo del lab, vedere [Come configurare l'utilizzo degli studenti](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato creato un lab per la classe in Azure. Per informazioni su come uno studente può accedere a una VM nel lab usando il collegamento di registrazione, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Connettersi a una VM nel lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)

