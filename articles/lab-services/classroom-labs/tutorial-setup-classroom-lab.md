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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 5bf8aea05855d81e88face1dd507f0006cc19cab
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73483887"
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
Per configurare un lab per le classi in un account del lab, è necessario essere un membro dei ruoli seguenti nell'account del lab: Proprietario, Autore di laboratori o Collaboratore. L'account usato per creare un account del lab viene aggiunto automaticamente al ruolo di Proprietario.

Un proprietario del lab può aggiungere altri utenti al ruolo **Autore di laboratori**. Ad esempio, un proprietario del lab può aggiungere docenti al ruolo Autore di laboratori. Quindi, i docenti creano lab con macchine virtuali per le classi. Gli studenti usano il collegamento di registrazione inviato dai docenti per registrarsi al lab. Una volta registrati, potranno usare le macchine virtuali nei lab per eseguire le attività in classe e a casa. Per istruzioni dettagliate sull'aggiunta di utenti al ruolo di Autore di laboratori, vedere [Aggiungere un utente al ruolo di creatore di lab](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
2. Selezionare **Accedi** e immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Selezionare **New lab** (Nuovo lab). 
    
    ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab e selezionare **Avanti**.  

        ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        Se si seleziona un'immagine di Linux, viene visualizzata un'opzione per abilitare la connessione Desktop remoto. Per informazioni, vedere [Abilitare Connessione desktop remoto per Linux](how-to-enable-remote-desktop-linux.md).
    2. Nella pagina **Virtual machine credentials** (Credenziali macchina virtuale) specificare le credenziali predefinite per tutte le VM del lab. Specificare il **nome** e la **password** per l'utente, quindi selezionare **Avanti**.  

        ![Finestra New lab](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Prendere nota del nome utente e della password perché non verranno più visualizzati.
    3. Nella pagina **Lab policies** (Criteri del lab) immettere il numero di ore allocate per ogni utente (**quota per ogni utente**) al di fuori del tempo pianificato per il lab e quindi selezionare **Fine**. 

        ![Quota per ogni utente](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Dovrebbe essere visualizzata la schermata seguente, che mostra lo stato di creazione della VM modello. La creazione del modello nel lab richiede fino a 20 minuti. 

    ![Stato di creazione della VM modello](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Nella pagina **Template** (Modello) procedere come segue: Questi passaggi sono **facoltativi** per l'esercitazione.

    2. Connettersi alla macchina virtuale modello selezionando **Connect** (Connetti). Se si tratta di una macchina virtuale modello di Linux, scegliere se si vuole effettuare la connessione con SSH o RDP (se RDP è abilitato).
    1. Selezionare **Reset password** (Reimposta password) per reimpostare la password per la macchina virtuale. 
    1. Installare e configurare il software nella macchina virtuale modello. 
    1. **Arrestare** la VM.  
    1. Immettere una **descrizione** per il modello.
10. Sulla barra degli strumenti della pagina **Template** (Modello) selezionare **Publish** (Pubblica). 

    ![Pulsante per la pubblicazione del modello](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > L'operazione di pubblicazione non può essere annullata. 
8. Nella pagina **Publish template** (Pubblica modello) immettere il numero di macchine virtuali da creare nel lab, quindi selezionare **Publish** (Pubblica). 

    ![Pubblicazione del modello: numero di VM](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Nella pagina viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Passare alla pagina **Pool di macchine virtuali** scegliendo Macchine virtuali dal menu sinistro oppure selezionando il riquadro Macchine virtuali. Verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    In questa pagina vengono eseguite le attività seguenti, ma non completarle per questa esercitazione. Questi passaggi hanno uno scopo unicamente informativo: 
    
    1. Per cambiare la capacità del lab, ossia il numero di VM al suo interno, selezionare **Lab capacity** (Capacità del lab) sulla barra degli strumenti.
    2. Per avviare tutte le VM contemporaneamente, selezionare **Start all** (Avvia tutte) sulla barra degli strumenti. 
    3. Per avviare una specifica VM, selezionare la freccia in già in **Status** (Stato) e quindi selezionare **Start** (Avvia). Per avviare una VM, è anche possibile selezionarla nella prima colonna e quindi selezionare **Start** (Avvia) sulla barra degli strumenti.

## <a name="add-users-to-the-lab"></a>Aggiungere utenti al lab

1. Selezionare **Utenti** nel menu a sinistra. Per impostazione predefinita, l'opzione **Limita l'accesso** è abilitata. Quando questa impostazione è attiva, un utente non può registrarsi al lab anche se ha il collegamento di registrazione, a meno che non sia presente nell'elenco degli utenti. Solo gli utenti nell'elenco possono registrarsi al lab utilizzando il collegamento di registrazione inviato. In questa procedura si aggiungono utenti all'elenco. In alternativa, è possibile disattivare **Limita l'accesso**, consentendo agli utenti di registrarsi al lab alla sola condizione di disporre del collegamento di registrazione. 
2. Selezionare **Aggiungi utenti** sulla barra degli strumenti e quindi **Add by email addresses** (Aggiungi per indirizzi di posta elettronica). 

    ![Pulsante Aggiungi utenti](../media/how-to-configure-student-usage/add-users-button.png)
1. Nella pagina **Aggiungi utenti** immettere gli indirizzi di posta elettronica degli utenti in righe separate o in una singola riga, separati da punti e virgola. 

    ![Aggiungere gli indirizzi di posta elettronica degli utenti](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selezionare **Salva**. Gli indirizzi di posta elettronica degli utenti e i relativi stati (registrati o no) saranno visualizzati nell'elenco. 

    ![Elenco utenti](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-a-schedule-for-the-lab"></a>Impostare una pianificazione per il lab
Creare un evento pianificato per il lab in modo che le VM al suo interno vengano automaticamente avviate/arrestate in specifici orari. La quota utente specificata in precedenza corrisponde al tempo aggiuntivo assegnato a ogni utente al di fuori del tempo pianificato. 

1. Passare alla pagina **Schedules** (Pianificazione) e selezionare **Add scheduled event** (Aggiungi evento pianificato) sulla barra degli strumenti. 

    ![Pulsante Aggiungi pianificazione nella pagina Pianificazioni](../media/how-to-create-schedules/add-schedule-button.png)
2. Verificare che per **Event type** (Tipo di evento) sia selezionata l'opzione **Standard**. Selezionare **Start only** (Solo avvio) per specificare solo l'ora di avvio per le VM. Selezionare **Stop only** (Solo arresto) per specificare solo l'ora di arresto per le VM. 
3. Nella sezione **Repeat** (Ripeti) selezionare la pianificazione corrente. 

    ![Pulsante Aggiungi pianificazione nella pagina Pianificazioni](../media/how-to-create-schedules/select-current-schedule.png)
4. Selezionando la pianificazione, viene visualizzata la finestra di dialogo **Repeat** (Ripeti). In questa finestra di dialogo eseguire la procedura seguente:
    1. Verificare che il campo **Repeat** (Ripeti) sia impostato su **every week** (ogni settimana). 
    3. Specificare la **data di inizio**.
    4. Specificare l'**ora di avvio** in cui si vuole che vengano avviate le VM.
    5. Specificare l'**ora di arresto** in cui devono essere arrestate le VM. 
    6. Specificare il **fuso orario** per le ore di avvio e di arresto specificate. 
    2. Selezionare i giorni in cui la pianificazione deve essere effettiva. Nell'esempio seguente sono selezionati i giorni compresi tra lunedì e giovedì. 
    8. Selezionare **Salva**. 

5. Ora nella pagina **Add scheduled event** (Aggiungi evento pianificato) immettere una descrizione per la pianificazione in **Notes (optional)** (Note - facoltativo). 
6. Nella pagina **Add scheduled event** (Aggiungi evento pianificato) selezionare **Save** (Salva). 

    ![Pianificazione settimanale](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-invitation-emails-to-students"></a>Inviare inviti agli studenti tramite posta elettronica

1. Passare alla visualizzazione **Users** (Utenti) se la pagina non è già aperta e selezionare **Invite all** (Invita tutti) sulla barra degli strumenti. 

    ![Selezionare gli studenti](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Nella pagina **Send invitation by email** (Invia invito per posta elettronica) immettere un messaggio facoltativo e quindi selezionare **Send** (Invia). Il messaggio di posta elettronica include automaticamente il collegamento per la registrazione. Per ottenere questo collegamento di registrazione, selezionare **... (puntini di sospensione)** sulla barra degli strumenti e quindi **Registration link** (Collegamento di registrazione). 

    ![Inviare un collegamento per la registrazione tramite posta elettronica](../media/tutorial-setup-classroom-lab/send-email.png)
4. Lo stato dell'**invito** viene visualizzato nell'elenco **Users** (Utenti). Lo stato dovrebbe cambiare in **Sending** (Invio in corso) e quindi in **Sent on &lt;date&gt;** (Inviato in data). 

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come creare un lab per le classi e come configurare il lab. Per informazioni su come uno studente può accedere a una VM nel lab usando il collegamento di registrazione, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Connettersi a una VM nel lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)

