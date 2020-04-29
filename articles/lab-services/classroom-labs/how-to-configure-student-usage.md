---
title: Configurare le impostazioni di utilizzo nei Lab della classe Azure Lab Services
description: Informazioni su come configurare il numero di studenti per un Lab, registrarli con il Lab, controllare il numero di ore per cui è possibile usare la macchina virtuale e altro ancora.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159456"
---
# <a name="add-and-manage-lab-users"></a>Aggiungere e gestire gli utenti del lab

Questo articolo descrive come aggiungere gli utenti degli studenti a un Lab, registrarli con il Lab, controllare il numero di ore aggiuntive che possono usare la macchina virtuale (VM) e altro ancora. 

## <a name="add-users-to-a-lab"></a>Aggiungere utenti a un lab

In questa sezione gli studenti vengono aggiunti a un Lab manualmente o caricando un file CSV. Eseguire le operazioni seguenti:

1. Nel riquadro a sinistra selezionare **Users** (Utenti). 

    Per impostazione predefinita, l'opzione **limita accesso** è attivata e, a meno che non si trovino nell'elenco degli utenti, gli studenti non possono registrarsi nel Lab anche se hanno un collegamento di registrazione. Solo gli utenti elencati possono registrarsi nel Lab usando il collegamento di registrazione inviato. In questa procedura si aggiungono utenti all'elenco. In alternativa, è possibile disabilitare l' **accesso restrict**, che consente agli studenti di registrarsi nel Lab purché dispongano del collegamento di registrazione. 

1. Nella parte superiore del riquadro **utenti** selezionare **Aggiungi utenti**e quindi selezionare **Aggiungi in base all'indirizzo di posta elettronica**. 

    ![Pulsante "Aggiungi utenti"](../media/how-to-configure-student-usage/add-users-button.png)

1. Nel riquadro **Aggiungi utenti** , immettere gli indirizzi di posta elettronica degli studenti su righe separate o su una singola riga separate da punti e virgola. 

    ![Aggiungere gli indirizzi di posta elettronica degli utenti](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Selezionare **Salva**. 

    L'elenco Visualizza gli indirizzi di posta elettronica e gli Stati degli utenti correnti, indipendentemente dal fatto che siano registrati o meno nel Lab. 

    ![Elenco utenti](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Dopo la registrazione degli studenti nel Lab, l'elenco Visualizza i relativi nomi. Il nome visualizzato nell'elenco viene costruito usando il nome e il cognome degli studenti in Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Aggiungere gli utenti caricando un file CSV

È anche possibile aggiungere utenti caricando un file CSV che contiene gli indirizzi di posta elettronica.

1. In Microsoft Excel creare un file CSV in cui sono elencati gli indirizzi di posta elettronica degli studenti in una colonna.

    ![Elenco di utenti in un file CSV](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. Nella parte superiore del riquadro **utenti** selezionare **Aggiungi utenti**, quindi selezionare **carica CSV**.

    ![Pulsante "carica CSV"](../media/how-to-configure-student-usage/upload-csv-button.png)

1. Selezionare il file CSV che contiene gli indirizzi di posta elettronica degli studenti e quindi selezionare **Apri**.

    La finestra **Aggiungi utenti** Visualizza l'elenco di indirizzi di posta elettronica dal file CSV. 

    ![Finestra "Aggiungi utenti" con indirizzi di posta elettronica dal file CSV](../media/how-to-configure-student-usage/add-users-window.png)

1. Selezionare **Salva**. 

1. Nel riquadro **utenti** visualizzare l'elenco degli studenti aggiunti. 

    ![Elenco di utenti aggiunti nel riquadro "utenti"](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Invia inviti agli utenti

Per inviare un collegamento di registrazione ai nuovi utenti, usare uno dei metodi seguenti. 

Se l'opzione **limita accesso** è abilitata per il Lab, solo gli utenti elencati possono usare il collegamento per la registrazione al Lab. Questa opzione è attivata per impostazione predefinita. 

### <a name="invite-all-users"></a>Invita tutti gli utenti

Questo metodo illustra come inviare messaggi di posta elettronica con un collegamento di registrazione e un messaggio facoltativo a tutti gli studenti elencati.

1. Nel riquadro **utenti** selezionare **invite all**. 

    ![Pulsante "invita tutti"](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Nella finestra **Invia invito per messaggio di posta elettronica** immettere un messaggio facoltativo e quindi selezionare **Invia**. 

    Il messaggio di posta elettronica include automaticamente il collegamento per la registrazione. Per ottenere e salvare separatamente il collegamento di registrazione, selezionare i puntini di sospensione (**...**) nella parte superiore del riquadro **utenti** , quindi selezionare **collegamento di registrazione**. 

    ![Finestra "Invia collegamento di registrazione tramite posta elettronica"](../media/tutorial-setup-classroom-lab/send-email.png)

    Nella colonna **invito** dell'elenco **utenti** viene visualizzato lo stato dell'invito per ogni utente aggiunto. Lo stato deve essere modificato in **mittente** e quindi **inviato in \<data>**. 

### <a name="invite-selected-users"></a>Invita gli utenti selezionati

Questo metodo illustra come invitare solo determinati studenti e ottenere un collegamento di registrazione che è possibile condividere con altri utenti.

1. Nel riquadro **utenti** selezionare uno studente o più studenti nell'elenco. 

1. Nella riga per lo studente selezionato selezionare l'icona della **busta** oppure, sulla barra degli strumenti, selezionare **invita**. 

    ![Invita gli utenti selezionati](../media/how-to-configure-student-usage/invite-selected-users.png)

1. Nella finestra **Invia invito per messaggio di posta elettronica** immettere un **messaggio**facoltativo e quindi selezionare **Invia**. 

    ![Invia un messaggio di posta elettronica agli utenti selezionati](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Il riquadro **utenti** Visualizza lo stato di questa operazione nella colonna **invito** della tabella. Il messaggio di posta elettronica di invito include il collegamento di registrazione che gli studenti possono usare per eseguire la registrazione al Lab.

## <a name="get-the-registration-link"></a>Ottenere il collegamento di registrazione

In questa sezione è possibile ottenere il collegamento di registrazione dal portale e inviarlo usando la propria applicazione di posta elettronica. 

1. Nel riquadro **utenti** selezionare il **collegamento di registrazione**.

    ![Collegamento di registrazione dello studente](../media/how-to-configure-student-usage/registration-link-button.png)

1. Nella finestra **registrazione utente** selezionare **copia**, quindi fare clic su **fine**. 

    ![Finestra "registrazione utente"](../media/how-to-configure-student-usage/registration-link.png)

    Il collegamento viene copiato negli Appunti. 
    
1. Nell'applicazione di posta elettronica incollare il collegamento di registrazione e quindi inviare il messaggio di posta elettronica a uno studente in modo che lo studente possa registrarsi per la classe. 

## <a name="view-registered-users"></a>Visualizzare gli utenti registrati

1. Accedere al sito Web [Azure Lab Services](https://labs.azure.com) . 
1. Selezionare **Sign in (accedi**) e immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft.
1. Nella pagina **laboratori personali** selezionare il Lab di cui si vuole tenere traccia dell'utilizzo. 
1. Nel riquadro sinistro selezionare **utenti**oppure selezionare il riquadro **utenti** . 

    Il riquadro **utenti** Visualizza un elenco di studenti che hanno eseguito la registrazione al Lab.  

    ![Elenco di utenti registrati](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Impostare quote per gli utenti

È possibile impostare una quota oraria per ogni studente effettuando le operazioni seguenti: 

1. Nel riquadro **utenti** selezionare **quota per utente: \<numero> ora** /e sulla barra degli strumenti. 
1. Nella finestra **quota per utente** specificare il numero di ore da assegnare a ogni studente al di fuori dell'ora della classe pianificata, quindi selezionare **Salva**.

    ![Finestra "quota per utente"](../media/how-to-configure-student-usage/quota-per-user.png)    

    I valori modificati vengono ora visualizzati nella **quota per utente: \<numero di ore>** pulsante sulla barra degli strumenti e nell'elenco utenti, come illustrato di seguito:

    ![Ore di quota per utente](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Il [tempo di esecuzione pianificato delle macchine virtuali](how-to-create-schedules.md) non viene conteggiato rispetto alla quota assegnata a uno studente. La quota è per il tempo fuori dalle ore pianificate che uno studente impiega per le macchine virtuali. 

## <a name="set-additional-quotas-for-specific-users"></a>Impostare quote aggiuntive per utenti specifici

È possibile specificare le quote per determinati studenti oltre le quote comuni impostate per tutti gli utenti nella sezione precedente. Se, ad esempio, un insegnante imposta la quota per tutti gli studenti su 10 ore e si imposta una quota aggiuntiva di 5 ore per uno studente specifico, lo studente ottiene 15 (10 + 5) ore di quota. Se si modifica la quota comune in un secondo momento, ad Say 15, lo studente avrà 20 (15 + 5) ore di quota. Tenere presente che questa quota complessiva non rientra nell'orario pianificato. Il tempo impiegato da uno studente in una macchina virtuale Lab durante l'orario pianificato non viene conteggiato in base a questa quota. 

Per impostare quote aggiuntive, procedere come segue:

1. Nel riquadro **utenti** selezionare uno studente nell'elenco e quindi selezionare **regola quota** sulla barra degli strumenti. 

    ![Pulsante "modifica quota"](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. Nella **>modificare la quota \<per l'indirizzo di posta elettronica utente o utenti selezionato **immettere il numero di ore di Lab aggiuntive da concedere agli studenti o agli studenti selezionati e quindi selezionare **applica**. 

    !["Adjust quota..." finestra](../media/how-to-configure-student-usage/additional-quota.png)

    Nella colonna **Usage (utilizzo** ) viene visualizzata la quota aggiornata per gli studenti selezionati. 

    ![Nuovo utilizzo per l'utente](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Account per studenti

Per aggiungere studenti a un Lab della classe, è possibile usare gli account di posta elettronica. Gli studenti potrebbero avere i seguenti tipi di account di posta elettronica:

- Un account di posta elettronica per studenti fornito dall'istanza di Azure Active Directory dell'Università per Office 365. 
- Un account di posta elettronica del dominio Microsoft, ad esempio *Outlook.com*, *hotmail.com*, *MSN.com*o *Live.com*.
- Un account di posta elettronica non Microsoft, ad esempio quello fornito da Yahoo! o Google. Tuttavia, questi tipi di account devono essere collegati con un account Microsoft.
- Un account GitHub. Questo account deve essere collegato a un account Microsoft.

### <a name="use-a-non-microsoft-email-account"></a>Usa un account di posta elettronica non Microsoft
Gli studenti possono usare gli account di posta elettronica non Microsoft per registrarsi e accedere a un Lab della classe.  Tuttavia, la registrazione richiede innanzitutto la creazione di un account Microsoft collegato al proprio indirizzo di posta elettronica non Microsoft.

Molti studenti potrebbero avere già un account Microsoft collegato al proprio indirizzo di posta elettronica non Microsoft. Ad esempio, gli studenti hanno già un account Microsoft se hanno usato il proprio indirizzo di posta elettronica con altri prodotti o servizi Microsoft, ad esempio Office, Skype, OneDrive o Windows.  

Quando gli studenti usano il collegamento di registrazione per accedere a una classe, vengono richiesti l'indirizzo di posta elettronica e la password. Gli studenti che tentano di accedere con un account Microsoft non collegato a una account Microsoft riceveranno il messaggio di errore seguente: 

![Messaggio di errore all'accesso](../media/how-to-configure-student-usage/cant-find-account.png)

Ecco un collegamento che consente agli studenti di [iscriversi a una account Microsoft](http://signup.live.com).  

> [!IMPORTANT]
> Quando gli studenti accedono a un Lab della classe, non hanno la possibilità di creare un account Microsoft. Per questo motivo, è consigliabile includere il collegamento di iscrizione, http://signup.live.com, nel messaggio di posta elettronica di registrazione del Lab in aula inviato agli studenti che usano account non Microsoft.

### <a name="use-a-github-account"></a>Usa un account GitHub
Gli studenti possono anche usare un account GitHub esistente per registrarsi e accedere a un Lab della classe. Se si dispone già di un account Microsoft collegato al proprio account GitHub, gli studenti possono accedere e fornire la password come illustrato nella sezione precedente. 

Se non hanno ancora collegato il proprio account GitHub a una account Microsoft, possono eseguire le operazioni seguenti:

1. Selezionare il collegamento **Opzioni di accesso** , come illustrato di seguito:

    ![Collegamento "opzioni di accesso"](../media/how-to-configure-student-usage/signin-options.png)

1. Nella finestra **Opzioni di accesso** selezionare **Accedi con GitHub**.

    ![Collegamento "Accedi con GitHub"](../media/how-to-configure-student-usage/signin-github.png)

    Al prompt degli studenti creare un account Microsoft collegato al proprio account GitHub. Il collegamento si verifica automaticamente quando si seleziona **Avanti**. Vengono quindi immediatamente connessi e connessi al Lab della classe.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Esportare un elenco di utenti in un file CSV

1. Passare al riquadro **utenti** .
1. Sulla barra degli strumenti, selezionare i puntini di sospensione (**...**) e quindi selezionare **Esporta CSV**. 

    ![Pulsante "Esporta CSV"](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- Per gli amministratori: [creare e gestire gli account Lab](how-to-manage-lab-accounts.md)
- Per i proprietari del Lab: [creare e gestire](how-to-manage-classroom-labs.md) Lab e [configurare e pubblicare modelli](how-to-create-manage-template.md)
- Per gli utenti del Lab: [accedere ai laboratori della classe](how-to-use-classroom-lab.md)
