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
ms.openlocfilehash: 0ab8d8688c7856eeae7d75527620c2b77ae78029
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584218"
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

    > [!NOTE]
    > Verranno visualizzati i nomi degli utenti nell'elenco dopo che sono stati registrati nel Lab. Il nome visualizzato nell'elenco viene costruito usando il nome e il cognome dell'utente nella Azure Active Directory. 

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
1. Passare alla visualizzazione **utenti** se non è già presente nella pagina e selezionare **invita tutti** sulla barra degli strumenti. 

    ![Selezionare gli studenti](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Nella pagina **Send invito by email** immettere un messaggio facoltativo e quindi selezionare **Send (Invia**). Il messaggio di posta elettronica include automaticamente il collegamento per la registrazione. È possibile ottenere questo collegamento per la registrazione selezionando **... (puntini** di sospensione) sulla barra degli strumenti e sul **collegamento di registrazione**. 

    ![Inviare un collegamento per la registrazione tramite posta elettronica](../media/tutorial-setup-classroom-lab/send-email.png)
4. Viene visualizzato lo stato di **invito** nell'elenco **utenti** . Lo stato deve essere modificato in **mittente** e quindi **inviato in \<data >** . 

    Per ulteriori informazioni sull'aggiunta di studenti a una classe e sulla gestione dell'utilizzo del Lab, vedere [How to configure Student Usage](how-to-configure-student-usage.md).

### <a name="invite-selected-users"></a>Invita gli utenti selezionati

1. Selezionare un utente o più utenti nell'elenco. 
2. Selezionare quindi l'icona della **busta** visualizzata nella riga selezionata oppure selezionare **invita** sulla barra degli strumenti. 

    ![Invita gli utenti selezionati](../media/how-to-configure-student-usage/invite-selected-users.png)
3. Nella finestra **Invia invito per messaggio di posta elettronica** immettere un **messaggio**facoltativo e quindi selezionare **Invia**. 

    ![Invia un messaggio di posta elettronica agli utenti selezionati](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Lo stato di questa operazione viene visualizzato nella colonna **invito** dell'elenco **utenti** . Il messaggio di posta elettronica di invito include il collegamento di registrazione che gli utenti possono usare per effettuare la registrazione al Lab.

1. Passare alla visualizzazione **utenti** , se non è già presente nella pagina. 

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
2. Selezionare **quota per utente: \<numero > ore** sulla barra degli strumenti. 
3. Nella pagina **quota per utente** specificare il numero di ore da assegnare a ogni utente (studente) al di fuori dell'ora della classe pianificata, quindi selezionare **Salva**.

    ![Quota per user (Quota per utente)](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Nella barra degli strumenti sono ora visualizzati i valori modificati: **quota per utente: \<numero di ore >** . 

    ![Quota per utente: dopo](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Il [tempo di esecuzione pianificato delle macchine virtuali](how-to-create-schedules.md) non interferisce con la quota assegnata a un utente. La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali. 

## <a name="set-additional-quota-for-a-specific-user"></a>Impostare una quota aggiuntiva per un utente specifico
È possibile specificare una quota aggiuntiva per un utente. Questa quota è aggiunta al set di quote comune per tutti gli utenti nella sezione precedente. Se, ad esempio, si imposta la quota per tutti gli utenti su 10 ore e si imposta una quota aggiuntiva di 5 ore per un utente specifico, gli utenti ottengono 15 (10 + 5) ore di quota. Se si modifica la quota comune in un secondo momento, ad Say 15, l'utente ottiene 20 (15 + 5) ore di quota. Tenere presente che questa quota complessiva si trova al di fuori dell'ora pianificata. Il tempo impiegato da uno studente in una macchina virtuale Lab durante l'orario di pianificazione non viene conteggiato in base a questa quota. 

A questo scopo, attenersi alla procedura seguente:

1. Selezionare un utente (studente) nell'elenco degli utenti nella pagina **utenti** .
2. Selezionare quindi **Adjust quota** dalla barra degli strumenti. 

    ![Pulsante regola quota](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Immettere il numero di **ore aggiuntive** per l'utente o gli utenti selezionati, quindi selezionare **applica**. 

    ![Quota aggiuntiva per un utente](../media/how-to-configure-student-usage/additional-quota.png)
4. Viene visualizzato l'utilizzo aggiornato per l'utente nella colonna **Usage** . 

    ![Nuovo utilizzo per l'utente](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Account per studenti
Per aggiungere studenti a un Lab della classe, è possibile usare gli account di posta elettronica. Potrebbero essere usati i tipi di account di posta elettronica seguenti:

- Un account di posta elettronica per studenti fornito dall'Ufficio 365 Azure Active Directory (AAD) dell'Università. 
- Un account di posta elettronica Microsoft, ad esempio `@outlook.com`, `@hotmail.com`, `@msn.com`o `@live.com`.
- Un account di posta elettronica non Microsoft, ad esempio uno fornito da Yahoo o Google. Tuttavia, questi tipi di account devono essere collegati con un account Microsoft.
- Un account GitHub. Questo account deve essere collegato a un account Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Uso di un account di posta elettronica non Microsoft
Gli studenti possono usare gli account di posta elettronica non Microsoft per registrarsi e accedere a un Lab della classe.  Tuttavia, la registrazione richiede che gli studenti creino prima un account Microsoft collegato al proprio indirizzo di posta elettronica non Microsoft.

Molti studenti potrebbero avere già un account Microsoft collegato ai propri indirizzi di posta elettronica non Microsoft. Ad esempio, gli studenti hanno già un account Microsoft se hanno usato il proprio indirizzo di posta elettronica con altri prodotti o servizi Microsoft, ad esempio Office, Skype, OneDrive o Windows.  

Quando uno studente fa clic sull'URL di registrazione per accedere a una classe, viene richiesto l'indirizzo di posta elettronica e la password. Se lo studente tenta di accedere con un non account Microsoft che non dispone di una account Microsoft collegata, lo studente riceverà il messaggio di errore seguente: 

![Messaggio di errore](../media/how-to-configure-student-usage/cant-find-account.png)

Per iscriversi a una account Microsoft, gli studenti devono passare a [http://signup.live.com](http://signup.live.com).  

> [!IMPORTANT]
> Quando gli studenti accedono a un Lab della classe, non hanno la possibilità di creare un account Microsoft. È quindi consigliabile includere questo collegamento di iscrizione nel messaggio di posta elettronica di registrazione del Lab in aula inviato agli studenti che usano account non Microsoft.

### <a name="using-a-github-account"></a>Uso di un account GitHub
Gli studenti possono anche usare un account GitHub esistente per registrarsi e accedere a un Lab della classe. Se lo studente dispone già di un account Microsoft collegato al proprio account GitHub, potrà accedere e fornire la password come illustrato nella sezione precedente. Se non hanno ancora collegato il proprio account GitHub a una account Microsoft, devono selezionare le **Opzioni di accesso**:

![Collegamento Opzioni di accesso](../media/how-to-configure-student-usage/signin-options.png)

Nella pagina **Opzioni di accesso** selezionare **Accedi con GitHub**.

![Accedi con collegamento a GitHub](../media/how-to-configure-student-usage/signin-github.png)

Infine, viene richiesto di creare un account Microsoft collegato al proprio account GitHub. Viene eseguita automaticamente quando lo studente sceglie **Avanti**.  Lo studente viene quindi immediatamente connesso e connesso al Lab della classe.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
