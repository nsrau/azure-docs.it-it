---
title: Configurare le impostazioni di utilizzo nel lab per le classi di Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare il numero di utenti per il lab, registrarli al lab, controllare il numero di ore in cui possono usare la macchina virtuale e altro ancora.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 67faf268d265fd045c21b75b6f64840511a371d3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067285"
---
# <a name="configure-usage-settings-and-policies"></a>Configurare le impostazioni e i criteri di utilizzo
Questo articolo descrive come aggiungere utenti al lab, registrarli al lab, controllare il numero di ore in cui possono usare la macchina virtuale e altro ancora. 


## <a name="add-users-to-the-lab"></a>Aggiungere utenti al lab
Se è stata abilitata l'opzione **Limitare l'accesso**, aggiungere gli utenti (indirizzi di posta elettronica) all'elenco.

1. Selezionare **Utenti** nel menu a sinistra.
2. Selezionare **Aggiungi utenti** sulla barra degli strumenti. 

    ![Pulsante Aggiungi utenti](../media/how-to-configure-student-usage/add-users-button.png)
1. Nella pagina **Aggiungi utenti** immettere gli indirizzi di posta elettronica degli utenti in righe separate o in una singola riga, separati da punti e virgola. 

    ![Aggiungere gli indirizzi di posta elettronica degli utenti](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selezionare **Salva**. Gli indirizzi di posta elettronica degli utenti e i relativi stati (registrati o no) saranno visualizzati nell'elenco. 

    ![Elenco utenti](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Collegamento di iscrizione di condivisione con gli studenti
Per inviare il collegamento di iscrizione agli studenti, usare uno dei metodi seguenti. Il primo metodo illustra come inviare messaggi di posta elettronica per gli studenti con un messaggio facoltativo e il collegamento di iscrizione. Il secondo metodo viene illustrato come ottenere il collegamento di registrazione che è possibile condividere con altri utenti come che vuoi. 

Se l'opzione **Limita l'accesso** è abilitata per il lab, solo gli utenti inclusi nell'elenco di utenti possono usare il collegamento di registrazione per eseguire la registrazione al lab. Questa opzione è abilitata per impostazione predefinita. 

### <a name="send-email-to-users"></a>Invia messaggio di posta elettronica agli utenti
Azure Lab Services consente ai docenti di inviti lab a tutti di posta elettronica o selezionati studenti senza dover usare un altro client di posta elettronica. Gli insegnanti possono passare il mouse sul singolo studente nell'elenco per visualizzare l'icona di messaggio di posta elettronica per ogni studente o studenti selezionare uno o più e utilizzare **Invia invito** sulla barra degli strumenti. Questa funzionalità Invia un messaggio di posta elettronica con un collegamento di registrazione e un messaggio (se presente) aggiunto dall'insegnante. Dopo che viene inviato l'invito, lo stato di invito diventa **invito inviato** in modo che insegnanti possono tenere traccia di quali gli studenti hanno già ricevuto il collegamento di registrazione e la data di invio.

1. Passare alla vista **Utenti** se non si è già nella pagina. 
2. Selezionare utenti specifici o tutti gli utenti nell'elenco. Per selezionare utenti specifici, selezionare le caselle di controllo nella prima colonna dell'elenco. Per selezionare tutti gli utenti, selezionare la casella di controllo accanto al titolo della prima colonna (**Nome**) oppure tutte le caselle di controllo per tutti gli utenti nell'elenco. È possibile visualizzare lo **stato dell'invito** in questo elenco.  Nella figura seguente lo stato dell'invito per tutti gli studenti è impostato su **Invito non inviato**. 

    ![Selezionare gli studenti](../media/tutorial-setup-classroom-lab/select-students.png)
1. Selezionare l'**icona del messaggio di posta elettronica (busta)** in una delle righe oppure selezionare **Invia invito** sulla barra degli strumenti. È anche possibile passare il puntatore del mouse sul nome di uno studente nell'elenco e quindi selezionare l'icona del messaggio di posta elettronica. 

    ![Inviare un collegamento per la registrazione tramite posta elettronica](../media/tutorial-setup-classroom-lab/send-email.png)
4. Nella pagina **Send registration link by email** (Invia collegamento registrazione per posta elettronica) seguire questa procedura: 
    1. Digitare un **messaggio facoltativo** da inviare agli studenti. Il messaggio di posta elettronica include automaticamente il collegamento per la registrazione. 
    2. Nella pagina **Send registration link by email** (Invia collegamento registrazione per posta elettronica) selezionare **Invia**. Lo stato dell'invito cambia in **Invio dell'invito** e quindi in **Invito inviato**. 
        
        ![Inviti inviati](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>Ottiene il collegamento di registrazione
1. Passare alla visualizzazione **Utenti** selezionando **Utenti** nel menu a sinistra. 
2. Selezionare il riquadro **Get registration link** (Ottieni collegamento registrazione).

    ![Collegamento di registrazione dello studente](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Copia**. Il collegamento viene copiato negli Appunti. Incollarlo in un editor di posta elettronica e inviare un messaggio di posta elettronica allo studente. 

    ![Collegamento di registrazione dello studente](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Chiudi**. 
4. Condividi i **collegamento di iscrizione** con uno studente in modo che gli studenti possono registrarsi per la classe. 

## <a name="view-users-registered-with-the-lab"></a>Visualizzare gli utenti che hanno eseguito la registrazione per il lab

Selezionare **Utenti** nel menu a sinistra per visualizzare l'elenco di utenti registrati al lab. 

![Elenco di utenti registrati al lab](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>Impostare le quote per utente
È possibile impostare quote per utente usando la procedura seguente: 

1. Selezionare **Utenti** nel menu a sinistra.
2. Selezionare **Quota di ogni utente:** sulla barra degli strumenti. 
3. Nel **Quota di ogni utente** , specificare il numero di ore da assegnare a ogni utente (studente): 
    1. **0 ore (solo schedule)** . Gli utenti possono usare le proprie macchine virtuali solo durante l'orario pianificato oppure quando è il proprietario del lab consente di attivare le macchine virtuali per loro.

        ![Zero ore - solo l'ora pianificata](../media/how-to-configure-student-usage/zero-hours.png)
    1. **Numero totale di ore di laboratorio per ogni utente**. Gli utenti possono usare le proprie macchine virtuali per il numero di ore (specificato per questo campo) impostato **oltre all'ora pianificata**. Se si seleziona questa opzione, immettere il **numero di ore** nella casella di testo. 

        ![Numero di ore per utente](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Selezionare **Salva**. 
5. Nella barra degli strumenti verranno visualizzati ora i valori modificati: **Quota per user (Quota per utente): &lt;numero di ore&gt;** . 

    ![Quota per user (Quota per utente)](../media/how-to-configure-student-usage/quota-per-user.png)



> [!IMPORTANT]
> Prima di inviare il collegamento di iscrizione agli studenti, docenti devono impostare la pianificazione per la classe se selezionare 0 ore di quota o specificare le ore di quota per l'ambiente lab.
>
> Il [tempo di esecuzione pianificato delle macchine virtuali](how-to-create-schedules.md) non interferisce con la quota assegnata a un utente. La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali. 

### <a name="add-users-by-uploading-a-csv-file"></a>Aggiungere gli utenti caricando un file CSV
È anche possibile aggiungere gli utenti caricando un file CSV con gli indirizzi di posta elettronica degli utenti.

1. Creare un file CSV con gli indirizzi di posta elettronica degli utenti in un'unica colonna.

    ![Quota per user (Quota per utente)](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Nella pagina **Utenti** dell'esercitazione, selezionare **Carica CSV** sulla barra degli strumenti.

    ![Pulsante Carica CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Selezionare il file CSV con gli indirizzi di posta elettronica degli utenti. Se si seleziona **Apri** dopo aver selezionato il file CSV, verrà visualizzata la finestra **Aggiungi utenti** seguente. L'elenco di indirizzi di posta elettronica viene riempito con gli indirizzi di posta elettronica contenuti nel file CSV. 

    ![Finestra Aggiungi utenti popolata con gli indirizzi di posta elettronica del file CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Nella finestra **Aggiunti utenti** selezionare **Salva**. 
5. Verificare che nell'elenco di utenti siano presenti gli utenti corretti. 

    ![Elenco di utenti aggiunti](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Gestire le macchine virtuali utente
Dopo che gli studenti si sono registrati al servizio Azure Lab Services usando il collegamento di registrazione che gli viene fornito, è possibile visualizzare le VM assegnate agli studenti nella scheda **Macchine virtuali** . 

![Macchine virtuali assegnate agli studenti](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

È possibile eseguire le attività seguenti sulla VM di uno studente: 

- Arrestare una VM se è in esecuzione. 
- Avviare una VM se è stata arrestata. 
- Connettersi alla macchina virtuale. 
- Eliminare la macchina virtuale. 
- Visualizzare il numero di ore in cui gli utenti hanno usato la macchina virtuale. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Aggiornare il numero di macchine virtuali nel lab
Per aggiornare il numero di macchine virtuali nel lab, eseguire questa procedura nella pagina **Macchine virtuali**:

1. Scegliere **Macchine virtuali** dal menu a sinistra. 
2. Selezionare **Lab capacity: &lt;numero&gt; machines** (Capacità lab: numero macchine virtuali) sulla barra degli strumenti. 
3. Immettere il **numero** di macchine virtuali.
4. Selezionare **Salva**.

    ![Macchine virtuali nel lab](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
