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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324509"
---
# <a name="add-and-manage-lab-users"></a>Aggiungere e gestire gli utenti del Lab
Questo articolo descrive come aggiungere utenti al lab, registrarli al lab, controllare il numero di ore in cui possono usare la macchina virtuale e altro ancora. 


## <a name="add-users-to-the-lab"></a>Aggiungere utenti al lab

1. Selezionare **Utenti** nel menu a sinistra. Per impostazione predefinita, l'opzione **Limita l'accesso** è abilitata. Quando questa impostazione è attiva, un utente non può registrarsi al lab anche se ha il collegamento di registrazione, a meno che non sia presente nell'elenco degli utenti. Solo gli utenti nell'elenco possono registrarsi al lab utilizzando il collegamento di registrazione inviato. In questa procedura si aggiungono utenti all'elenco. In alternativa, è possibile disattivare **Limita l'accesso**, consentendo agli utenti di registrarsi al lab alla sola condizione di disporre del collegamento di registrazione. 
2. Selezionare **Aggiungi utenti** sulla barra degli strumenti e quindi selezionare **Aggiungi per indirizzi di posta elettronica**. 

    ![Pulsante Aggiungi utenti](../media/how-to-configure-student-usage/add-users-button.png)
1. Nella pagina **Aggiungi utenti** immettere gli indirizzi di posta elettronica degli utenti in righe separate o in una singola riga, separati da punti e virgola. 

    ![Aggiungere gli indirizzi di posta elettronica degli utenti](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selezionare **Salva**. Gli indirizzi di posta elettronica degli utenti e i relativi stati (registrati o no) saranno visualizzati nell'elenco. 

    ![Elenco utenti](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>Aggiungere gli utenti caricando un file CSV
È anche possibile aggiungere gli utenti caricando un file CSV con gli indirizzi di posta elettronica degli utenti.

1. Creare un file CSV con gli indirizzi di posta elettronica degli utenti in un'unica colonna.

    ![File CSV con utenti](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Nella pagina **utenti** del Lab selezionare **Aggiungi utenti** sulla barra degli strumenti e quindi selezionare **carica CSV**.

    ![Pulsante Carica CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Selezionare il file CSV con gli indirizzi di posta elettronica degli utenti. Se si seleziona **Apri** dopo aver selezionato il file CSV, verrà visualizzata la finestra **Aggiungi utenti** seguente. L'elenco di indirizzi di posta elettronica viene riempito con gli indirizzi di posta elettronica contenuti nel file CSV. 

    ![Finestra Aggiungi utenti popolata con gli indirizzi di posta elettronica del file CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Nella finestra **Aggiunti utenti** selezionare **Salva**. 
5. Verificare che nell'elenco di utenti siano presenti gli utenti corretti. 

    ![Elenco di utenti aggiunti](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Invia inviti agli utenti
Per inviare il collegamento di registrazione agli studenti, usare uno dei metodi seguenti. Il primo metodo illustra come inviare messaggi di posta elettronica agli studenti con il collegamento di registrazione e un messaggio facoltativo. Il secondo metodo Mostra come ottenere il collegamento di registrazione che è possibile condividere con altri utenti nel modo desiderato. 

Se l'opzione **Limita l'accesso** è abilitata per il lab, solo gli utenti inclusi nell'elenco di utenti possono usare il collegamento di registrazione per eseguire la registrazione al lab. Questa opzione è abilitata per impostazione predefinita. 

### <a name="invite-all-users"></a>Invita tutti gli utenti

1. Passa alla pagina **utenti** del Lab. 
2. Selezionare **invita tutto** dalla barra degli strumenti. 
3. Immettere un **messaggio** per gli utenti. Si tratta di un passaggio facoltativo.
4. Selezionare quindi **Send (Invia**).

    ![Invita tutti gli utenti](../media/how-to-configure-student-usage/invite-all.png)

    Lo stato di questa operazione verrà visualizzato nella colonna **invito** dell'elenco **utenti** . Il messaggio di posta elettronica di invito include il collegamento di registrazione che gli utenti possono usare per effettuare la registrazione al Lab. 

### <a name="invite-selected-users"></a>Invita gli utenti selezionati

1. Selezionare un utente o più utenti nell'elenco. 
2. Selezionare quindi l'icona della **busta** visualizzata nella riga selezionata oppure selezionare **invita** sulla barra degli strumenti. 

    ![Invita gli utenti selezionati](../media/how-to-configure-student-usage/invite-selected-users.png)
3. Nella finestra **Invia invito per messaggio di posta elettronica** immettere un **messaggio**facoltativo e quindi selezionare **Invia**. 

    ![Invia un messaggio di posta elettronica agli utenti selezionati](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Lo stato di questa operazione verrà visualizzato nella colonna **invito** dell'elenco **utenti** . Il messaggio di posta elettronica di invito include il collegamento di registrazione che gli utenti possono usare per effettuare la registrazione al Lab.

1. Passare alla vista **Utenti** se non si è già nella pagina. 

## <a name="get-registration-link"></a>Ottenere il collegamento di registrazione
È anche possibile ottenere il collegamento di registrazione dal portale e inviarlo usando la propria applicazione client di posta elettronica. 

1. Passare alla visualizzazione **Utenti** selezionando **Utenti** nel menu a sinistra. 
2. Seleziona **... (puntini** di sospensione) sulla barra degli strumenti, quindi selezionare **collegamento registrazione**.

    ![Collegamento di registrazione dello studente](../media/how-to-configure-student-usage/registration-link-button.png)
1. Nella finestra di dialogo **registrazione utente** selezionare il pulsante **copia** . Il collegamento viene copiato negli Appunti. Incollarlo in un editor di posta elettronica e inviare un messaggio di posta elettronica allo studente. 

    ![Collegamento di registrazione dello studente](../media/how-to-configure-student-usage/registration-link.png)
2. Nella finestra di dialogo **registrazione utente** selezionare **fine**. 
4. Inviare il **collegamento di registrazione** a uno studente in modo che lo studente possa registrarsi per la classe. 

## <a name="view-users-registered-with-the-lab"></a>Visualizzare gli utenti che hanno eseguito la registrazione per il lab

Selezionare **Utenti** nel menu a sinistra per visualizzare l'elenco di utenti registrati al lab. 

![Elenco di utenti registrati al lab](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>Impostare quote per gli utenti
È possibile impostare quote per utente usando la procedura seguente: 

1. Selezionare **Users** (Utenti) nel menu a sinistra se la pagina non è già attiva. 
2. Selezionare **quota per utente: &lt;number @ no__t-2 ore** sulla barra degli strumenti. 
3. Nella pagina **quota per utente** specificare il numero di ore da assegnare a ogni utente (studente) al di fuori dell'ora della classe pianificata, quindi selezionare **Salva**.

    ![Quota per user (Quota per utente)](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Nella barra degli strumenti sono ora visualizzati i valori modificati: **quota per utente: &lt;number di ore @ no__t-2**. 

    ![Quota per utente-dopo](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Il [tempo di esecuzione pianificato delle macchine virtuali](how-to-create-schedules.md) non interferisce con la quota assegnata a un utente. La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali. 

## <a name="set-additional-quota-for-a-specific-user"></a>Impostare una quota aggiuntiva per un utente specifico
È possibile impostare una quota separata per un utente. A questo scopo, attenersi alla procedura seguente:

1. Selezionare un utente (studente) nell'elenco degli utenti nella pagina **utenti** .
2. Selezionare quindi **Adjust quota** dalla barra degli strumenti. 

    ![Pulsante regola quota](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Immettere il numero di **ore aggiuntive** per l'utente o gli utenti selezionati, quindi selezionare **applica**. 

    ![Quota aggiuntiva per un utente](../media/how-to-configure-student-usage/additional-quota.png)
4. Viene visualizzato l'utilizzo aggiornato per l'utente nella colonna **Usage** . 

    ![Nuovo utilizzo per l'utente](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
