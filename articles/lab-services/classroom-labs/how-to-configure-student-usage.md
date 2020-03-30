---
title: Configurare le impostazioni di utilizzo nei laboratori in aula di Azure Lab ServicesConfigure usage settings in classroom labs of Azure Lab Services
description: Informazioni su come configurare il numero di studenti per un lab, registrarli con il lab, controllare il numero di ore in cui possono usare la macchina virtuale e altro ancora.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159456"
---
# <a name="add-and-manage-lab-users"></a>Aggiungere e gestire gli utenti del lab

Questo articolo descrive come aggiungere utenti studenti a un lab, registrarli con il lab, controllare il numero di ore aggiuntive che possono usare la macchina virtuale (VM) e altro ancora. 

## <a name="add-users-to-a-lab"></a>Aggiungere utenti a un lab

In questa sezione si aggiungono gli studenti a un lab manualmente o caricando un file CSV. Eseguire le operazioni seguenti:

1. Nel riquadro a sinistra selezionare **Users** (Utenti). 

    Per impostazione predefinita, l'opzione **Limita accesso** è attivata e, a meno che non si trovano nell'elenco degli utenti, gli studenti non possono registrarsi al lab anche se dispongono di un collegamento di registrazione. Solo gli utenti elencati possono registrarsi al lab utilizzando il collegamento di registrazione inviato. In questa procedura si aggiungono utenti all'elenco. In alternativa, è possibile disattivare **Limita accesso**, che consente agli studenti di registrarsi al laboratorio purché dispongano del collegamento di registrazione. 

1. Nella parte superiore del riquadro **Utenti** selezionare **Aggiungi utenti**e quindi Aggiungi per indirizzo di **posta elettronica**. 

    ![Il pulsante "Aggiungi utenti"](../media/how-to-configure-student-usage/add-users-button.png)

1. Nel riquadro **Aggiungi utenti** immettere gli indirizzi di posta elettronica degli studenti su righe separate o su una singola riga separata da punto e virgola. 

    ![Aggiungere gli indirizzi di posta elettronica degli utenti](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Selezionare **Salva**. 

    Nell'elenco vengono visualizzati gli indirizzi di posta elettronica e gli stati degli utenti correnti, indipendentemente dal fatto che siano registrati o meno nel lab. 

    ![Elenco utenti](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Dopo che gli studenti sono stati registrati nel laboratorio, nell'elenco vengono visualizzati i nomi. Il nome visualizzato nell'elenco viene creato utilizzando il nome e il cognome degli studenti in Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Aggiungere gli utenti caricando un file CSV

Puoi anche aggiungere utenti caricando un file CSV che contiene i loro indirizzi email.

1. In Microsoft Excel creare un file CSV in cui sono elencati gli indirizzi di posta elettronica degli studenti in una colonna.

    ![Elenco di utenti in un file CSV](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. Nella parte superiore del riquadro **Utenti** selezionare **Aggiungi utenti**e quindi **Carica CSV**.

    ![Il pulsante "Carica CSV"](../media/how-to-configure-student-usage/upload-csv-button.png)

1. Selezionare il file CSV contenente gli indirizzi di posta elettronica degli studenti e quindi selezionare **Apri**.

    Nella finestra **Aggiungi utenti** viene visualizzato l'elenco di indirizzi di posta elettronica del file CSV. 

    ![La finestra "Aggiungi utenti" con indirizzi e-mail da file CSV](../media/how-to-configure-student-usage/add-users-window.png)

1. Selezionare **Salva**. 

1. Nel riquadro **Utenti** visualizzare l'elenco degli studenti aggiunti. 

    ![Elenco degli utenti aggiunti nel riquadro "Utenti"](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Inviare inviti agli utenti

Per inviare un collegamento di registrazione ai nuovi utenti, utilizzare uno dei metodi descritti di seguito. 

Se l'opzione **Limita accesso** è abilitata per il lab, solo gli utenti elencati possono utilizzare il collegamento di registrazione per registrarsi al lab. Questa opzione è attivata per impostazione predefinita. 

### <a name="invite-all-users"></a>Invita tutti gli utenti

Questo metodo mostra come inviare un messaggio di posta elettronica con un collegamento di registrazione e un messaggio facoltativo a tutti gli studenti elencati.

1. Nel riquadro **Utenti** selezionare **Invita tutto**. 

    ![Il pulsante "Invita tutti"](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Nella finestra **Invia invito tramite posta elettronica** immettere un messaggio facoltativo e quindi selezionare **Invia**. 

    Il messaggio di posta elettronica include automaticamente il collegamento per la registrazione. Per ottenere e salvare il collegamento di registrazione separatamente, selezionare i conioni ai solchi (**...**) nella parte superiore del riquadro **Utenti,** quindi selezionare **Collegamento registrazione**. 

    ![La finestra "Invia link di registrazione via e-mail"](../media/tutorial-setup-classroom-lab/send-email.png)

    Nella colonna **Invito** dell'elenco **Utenti** viene visualizzato lo stato dell'invito per ogni utente aggiunto. Lo stato deve cambiare in **Invio** e quindi in **Inviato \<in data>**. 

### <a name="invite-selected-users"></a>Invitare utenti selezionati

Questo metodo mostra come invitare solo determinati studenti e ottenere un link di registrazione che puoi condividere con altre persone.

1. Nel riquadro **Utenti** selezionare uno o più studenti nell'elenco. 

1. Nella riga dello studente selezionato selezionare l'icona della **busta** oppure, sulla barra degli strumenti, selezionare **Invita**. 

    ![Invitare utenti selezionati](../media/how-to-configure-student-usage/invite-selected-users.png)

1. Nella finestra **Invia invito tramite posta elettronica** immettere un **messaggio**facoltativo e quindi selezionare **Invia**. 

    ![Inviare messaggi di posta elettronica a utenti selezionati](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Nel riquadro **Utenti** viene visualizzato lo stato dell'operazione nella colonna **Invito** della tabella. L'e-mail di invito include il link di registrazione che gli studenti possono utilizzare per registrarsi al laboratorio.

## <a name="get-the-registration-link"></a>Ottenere il collegamento di registrazioneGet the registration link

In questa sezione, è possibile ottenere il collegamento di registrazione dal portale e inviarlo utilizzando la propria applicazione di posta elettronica. 

1. Nel riquadro **Utenti** selezionare **Collegamento registrazione**.

    ![Collegamento di registrazione dello studente](../media/how-to-configure-student-usage/registration-link-button.png)

1. Nella finestra **Registrazione utente** selezionare **Copia**, quindi **Fatto**. 

    ![La finestra "Registrazione utente"](../media/how-to-configure-student-usage/registration-link.png)

    Il collegamento viene copiato negli Appunti. 
    
1. Nell'applicazione di posta elettronica incollare il collegamento di registrazione e quindi inviare il messaggio di posta elettronica a uno studente in modo che lo studente possa registrarsi per il corso. 

## <a name="view-registered-users"></a>Visualizzare gli utenti registrati

1. Passare al sito Web di [Servizi di laboratorio](https://labs.azure.com) di Azure.Go to the Azure Lab Services website. 
1. Selezionare **Accedi**e quindi immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft.
1. Nella pagina **Lab** personali selezionare il lab di cui si vuole tenere traccia dell'utilizzo. 
1. Nel riquadro sinistro selezionare **Utenti**oppure selezionare il riquadro **Utenti.** 

    Nel riquadro **Utenti** viene visualizzato un elenco di studenti registrati al lab.  

    ![Elenco degli utenti registrati](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Impostare quote per gli utenti

Puoi impostare una quota oraria per ogni studente effettuando le seguenti operazioni: 

1. Nel riquadro **Utenti** selezionare **Quota per utente: \<numero> ore** sulla barra degli strumenti. 
1. Nella finestra **Quota per utente** specificare il numero di ore che si desidera assegnare a ogni studente al di fuori dell'orario pianificato della classe, quindi selezionare **Salva**.

    ![La finestra "Quota per utente"](../media/how-to-configure-student-usage/quota-per-user.png)    

    I valori modificati vengono ora visualizzati nella **quota per utente: \<numero di ore>** pulsante sulla barra degli strumenti e nell'elenco degli utenti, come illustrato di seguito:

    ![Ore di quota per utente](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Il [tempo di esecuzione pianificato delle macchine virtuali](how-to-create-schedules.md) non viene conteggiato rispetto alla quota assegnata a uno studente. La quota è per il tempo al di fuori delle ore pianificate che uno studente passa alle macchine virtuali. 

## <a name="set-additional-quotas-for-specific-users"></a>Impostare quote aggiuntive per utenti specifici

È possibile specificare quote per determinati studenti oltre le quote comuni impostate per tutti gli utenti nella sezione precedente. Ad esempio, se, come istruttore, imposti la quota per tutti gli studenti su 10 ore e imposti una quota aggiuntiva di 5 ore per uno studente specifico, lo studente ottiene 15 (10 x 5) ore di quota. Se modifichi la quota comune in un secondo momento, ad esempio 15, lo studente ottiene 20 (15 x 5) ore di quota. Tenere presente che questa quota complessiva è al di fuori dell'ora pianificata. Il tempo che uno studente trascorre in una macchina virtuale del lab durante l'ora pianificata non viene conteggiato rispetto a questa quota. 

Per impostare quote aggiuntive, eseguire le operazioni seguenti:

1. Nel riquadro **Utenti** selezionare uno studente dall'elenco e quindi selezionare **Regola quota** sulla barra degli strumenti. 

    ![Il pulsante "Regola quota"](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. Nella **>Regola \<quota per l'utente o l'indirizzo **di posta elettronica selezionati immettere il numero di ore di laboratorio aggiuntive che si desidera concedere agli studenti o agli studenti selezionati e quindi selezionare **Applica**. 

    ![La sezione "Regola quota..." Finestra](../media/how-to-configure-student-usage/additional-quota.png)

    Nella colonna **Utilizzo** viene visualizzata la quota aggiornata per gli studenti selezionati. 

    ![Nuovo utilizzo per l'utente](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Account degli studenti

Per aggiungere studenti a un laboratorio della classe, è necessario utilizzare i relativi account di posta elettronica. Gli studenti potrebbero avere i seguenti tipi di account di posta elettronica:

- Un account di posta elettronica per studenti fornito dall'istanza di Azure Active Directory dell'università per Office 365. 
- Account di posta elettronica di dominio Microsoft, ad esempio *outlook.com*, *hotmail.com*, *msn.com*o *live.com*.
- Un account di posta elettronica non Microsoft, ad esempio uno fornito da Yahoo! o Google. Tuttavia, questi tipi di account devono essere collegati a un account Microsoft.
- Un account GitHub. Questo account deve essere collegato a un account Microsoft.

### <a name="use-a-non-microsoft-email-account"></a>Usare un account di posta elettronica non MicrosoftUse a non-Microsoft email account
Gli studenti possono usare account di posta elettronica non Microsoft per registrarsi e accedere a un laboratorio della classe.  Tuttavia, la registrazione richiede che crei prima un account Microsoft collegato al loro indirizzo di posta elettronica non Microsoft.

Molti studenti potrebbero già avere un account Microsoft collegato al proprio indirizzo di posta elettronica non Microsoft. Ad esempio, gli studenti dispongono già di un account Microsoft se hanno usato il proprio indirizzo di posta elettronica con altri prodotti o servizi Microsoft, ad esempio Office, Skype, OneDrive o Windows.  

Quando gli studenti utilizzano il link di registrazione per accedere a una classe, viene richiesto loro di immettere il loro indirizzo e-mail e la password. Gli studenti che tentano di accedere con un account non Microsoft non collegato a un account Microsoft riceveranno il seguente messaggio di errore: 

![Messaggio di errore all'accesso](../media/how-to-configure-student-usage/cant-find-account.png)

Ecco un collegamento per gli studenti per iscriversi a [un account Microsoft.](http://signup.live.com)  

> [!IMPORTANT]
> Quando gli studenti accedono a un laboratorio della classe, non hanno la possibilità di creare un account Microsoft. Per questo motivo, è consigliabile includere http://signup.live.comquesto collegamento di iscrizione, , nell'e-mail di registrazione del laboratorio di classe inviata agli studenti che utilizzano account non Microsoft.

### <a name="use-a-github-account"></a>Usare un account GitHubUse a GitHub account
Gli studenti possono anche usare un account GitHub esistente per registrarsi e accedere a un laboratorio della classe. Se hanno già un account Microsoft collegato al proprio account GitHub, gli studenti possono accedere e fornire la password come mostrato nella sezione precedente. 

Se non hanno ancora collegato il proprio account GitHub a un account Microsoft, possono eseguire le operazioni seguenti:If they haven't yet linked their GitHub account to a Microsoft account, they can do the following:

1. Selezionare il collegamento **Opzioni di accesso,** come illustrato di seguito:

    ![Il collegamento "Opzioni di accesso"](../media/how-to-configure-student-usage/signin-options.png)

1. Nella finestra **Opzioni di** accesso selezionare Accedi **con GitHub**.

    ![Il collegamento "Accedi con GitHub"](../media/how-to-configure-student-usage/signin-github.png)

    Quando richiesto, gli studenti creano quindi un account Microsoft collegato al proprio account GitHub.At the prompt, students then create a Microsoft account that's linked to their GitHub account. Il collegamento avviene automaticamente quando seleziona **Avanti**. Vengono quindi immediatamente connessi e collegati al laboratorio della classe.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Esportare un elenco di utenti in un file CSV

1. Passare al riquadro **Utenti.**
1. Sulla barra degli strumenti selezionare i conividi (**...**) e quindi **selezionare Esporta CSV**. 

    ![Il pulsante "Esporta CSV"](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- Per gli amministratori: [creare e gestire gli account lab](how-to-manage-lab-accounts.md)
- Per i proprietari di laboratori: [creare e gestire lab](how-to-manage-classroom-labs.md) e [Impostare e pubblicare modelli](how-to-create-manage-template.md)
- Per gli utenti del laboratorio: [accedere ai laboratori di classroom](how-to-use-classroom-lab.md)
