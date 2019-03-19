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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: eadbaacd4bb860cd09997775b8d77cbcf0ff17f2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181623"
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
1. Passare alla vista **Utenti** se non si è già nella pagina. 
2. Selezionare specifico o tutti gli utenti nell'elenco. Per selezionare utenti specifici, selezionare le caselle di controllo nella prima colonna dell'elenco. Per selezionare tutti gli utenti, selezionare la casella di controllo davanti il titolo della prima colonna (**nome**) oppure selezionare tutte le caselle di controllo per tutti gli utenti nell'elenco.
3. Selezionare **Invia invito** sulla barra degli strumenti. È anche possibile posizionare il mouse su un nome di uno studente nell'elenco e inviare l'icona di messaggio di posta elettronica. 

    ![Invia collegamento di iscrizione tramite posta elettronica](../media/tutorial-setup-classroom-lab/send-email.png)
4. Nel **collegamento di iscrizione di invio tramite posta elettronica** pagina, seguire questa procedura: 
    1. Tipo di un' **messaggio facoltativo** che si desidera inviare agli studenti. Messaggio di posta elettronica include automaticamente il collegamento di iscrizione. 
    2. Nel **collegamento di iscrizione di invio tramite posta elettronica** pagina, selezionare **inviare**. 

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
2. Selezionare **Quota per user: unlimited** (Quota per utente: illimitata) sulla barra degli strumenti. 
3. Nella pagina **Quota per user** (Quota per utente) selezionare una delle opzioni seguenti: 
    1. **Nessuno**. Gli utenti possono usare le macchine virtuali solo durante l'orario pianificato o quando il proprietario del lab attiva le macchine virtuali appositamente per loro.
    2. **Illimitato (impostazione predefinita)**. Gli utenti possono usare le macchine virtuali senza alcun vincolo temporale.
    3. **Specificare il numero di ore per utente**. Gli utenti possono usare le macchine virtuali per il numero di ore specificato (riportato di seguito), in aggiunta all'orario pianificato. Se si seleziona questa opzione, immettere il **numero di ore** nella casella di testo. 

        ![Numero di ore per utente](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Selezionare **Salva**. 
5. Nella barra degli strumenti verranno visualizzati ora i valori modificati: **Quota per user (Quota per utente): &lt;numero di ore&gt;**. 

    ![Quota per user (Quota per utente)](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
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
