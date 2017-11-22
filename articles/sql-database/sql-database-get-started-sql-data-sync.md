---
title: Impostare l'anteprima di sincronizzazione dati SQL | Microsoft Docs
description: Questa esercitazione illustra come impostare l'anteprima di sincronizzazione dati SQL di Azure
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: b356bc9db9e883c2514953b516d6dd51c1807610
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="set-up-sql-data-sync-preview"></a>Impostare l'anteprima di sincronizzazione dati SQL
In questa esercitazione si imparerà a configurare sincronizzazione dati SQL di Azure creando un gruppo di sincronizzazione ibrido che contiene sia istanze del database SQL di Azure che istanze di SQL Server. Il nuovo gruppo di sincronizzazione ha una configurazione completa ed esegue la sincronizzazione in base alla pianificazione impostata.

Per questa esercitazione è necessario avere già un certo livello di esperienza precedente con il database SQL di Azure e SQL Server. 

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure (anteprima)](sql-database-sync-data.md).

Per esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL, vedere gli articoli seguenti:
-   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Passaggio 1 - Creare un gruppo di sincronizzazione

### <a name="locate-the-data-sync-settings"></a>Individuare le impostazioni di sincronizzazione dati

1.  Nel browser passare al portale di Azure.

2.  Nel portale individuare i database SQL dal dashboard o dall'icona Database SQL sulla barra degli strumenti.

    ![Elenco dei database SQL di Azure](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  Nel pagina **Database SQL** selezionare il database SQL esistente da usare come database hub per la sincronizzazione dati. Verrà aperta la pagina Database SQL.

4.  Nella pagina Database SQL per il database selezionato selezionare **Sincronizza con altri database**. Verrà aperta la pagina Sincronizzazione dati.

    ![Opzione Sincronizza con altri database](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Creare un nuovo gruppo di sincronizzazione

1.  Nel pagina Sincronizzazione dati selezionare **Nuovo gruppo di sincronizzazione**. Verrà aperta la pagina **Nuovo gruppo di sincronizzazione** con evidenziato il passaggio 1 **Crea gruppo di sincronizzazione**. Verrà aperta anche la pagina **Crea gruppo di sincronizzazione dati**.

2.  Nella pagina **Crea gruppo di sincronizzazione dati** eseguire le operazioni seguenti:

    1.  Nel campo **Nome gruppo di sincronizzazione** immettere un nome per il nuovo gruppo di sincronizzazione.

    2.  Nella sezione **Database dei metadati di sincronizzazione** scegliere se creare un nuovo database (scelta consigliata) o usare un database esistente.

        > [!NOTE]
        > Microsoft consiglia di creare un nuovo database vuoto da usare come database dei metadati di sincronizzazione. La sincronizzazione dati crea tabelle in questo database ed esegue un carico di lavoro frequente. Questo database viene automaticamente condiviso come database dei metadati di sincronizzazione per tutti i gruppi di sincronizzazione nell'area selezionata. Non è possibile modificare il database dei metadati di sincronizzazione o il relativo nome senza eliminarlo.

        Se si sceglie **Nuovo database** selezionare **Crea nuovo database**. Verrà aperta la pagina **Database SQL**. Nella pagina **Database SQL** specificare un nome per il nuovo database e configurarlo. Selezionare **OK**.

        Se si sceglie **Utilizza database esistente**, selezionare il database nell'elenco.

    3.  Nella sezione **Sincronizzazione automatica** selezionare innanzitutto **Sì** o **No**.

        Se si sceglie **Sì**, nella sezione **Frequenza sincronizzazione** immettere un numero e selezionare Secondi, Minuti, Ore o Giorni.

        ![Specificare la frequenza di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  Nella sezione **Risoluzione dei conflitti** selezionare "Priorità hub" o "Priorità client".

        ![Specificare come vengono risolti i conflitti](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Selezionare **OK** e attendere che il nuovo gruppo di sincronizzazione venga creato e distribuito.

## <a name="step-2---add-sync-members"></a>Passaggio 2 - Aggiungere i membri di sincronizzazione

Al termine della creazione e distribuzione del nuovo gruppo di sincronizzazione, nella pagina **Nuovo gruppo di sincronizzazione** viene evidenziato il passaggio 2 **Aggiungi membri di sincronizzazione**.

Nella sezione **Database hub** immettere le credenziali esistenti per il server di database SQL in cui si trova il database hub. Non immettere *nuove* credenziali in questa sezione.

![Database hub aggiunto al gruppo di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Aggiungere un database SQL di Azure

Nella sezione **Database membro** aggiungere facoltativamente un database SQL di Azure al gruppo di sincronizzazione selezionando **Aggiungi un database di Azure**. Verrà aperta la pagina **Configura database di Azure**.

Nella pagina **Configura database di Azure** eseguire le operazioni seguenti:

1.  Nel campo **Nome membro di sincronizzazione** specificare un nome per il nuovo membro di sincronizzazione. Questo nome è distinto dal nome del database stesso.

2.  Nel campo **Sottoscrizione** selezionare la sottoscrizione di Azure associata per la fatturazione.

3.  Nel campo **Server di Azure SQL** selezionare il server di database SQL esistente.

4.  Nel campo **Database SQL di Azure** selezionare il database SQL esistente.

5.  Nel campo **Direzioni sincronizzazione** selezionare Sincronizzazione bidirezionale, Verso l'hub e Dall'hub.

    ![Aggiungere un nuovo membro di sincronizzazione del database SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  Nei campi **Nome utente** e **Password** immettere le credenziali esistenti per il server di database SQL in cui si trova il database membro. Non immettere *nuove* credenziali in questa sezione.

7.  Selezionare **OK** e attendere che il nuovo membro di sincronizzazione venga creato e distribuito.

    ![Il nuovo membro di sincronizzazione del database SQL è stato aggiunto](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Aggiungere un database SQL Server locale

Nella sezione **Database membro** aggiungere facoltativamente un'istanza di SQL Server locale al gruppo di sincronizzazione selezionando **Aggiungi un database locale**. Verrà aperta la pagina **Configura database locale**.

Nella pagina **Configura database locale** eseguire le operazioni seguenti:

1.  Selezionare **Scegliere il gateway dell'agente di sincronizzazione**. Verrà aperta la pagina **Seleziona agente di sincronizzazione**.

    ![Scegliere il gateway dell'agente di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  Nella pagina **Scegliere il gateway dell'agente di sincronizzazione** specificare se si vuole usare un agente esistente o crearne uno nuovo.

    Se si sceglie **Agenti esistenti**, selezionare l'agente esistente nell'elenco.

    Se si sceglie **Consente di creare un nuovo agente**, eseguire le operazioni seguenti:

    1.  Scaricare il software dell'agente di sincronizzazione client dal collegamento specificato e installarlo nel computer in cui si trova SQL Server.
 
        > [!IMPORTANT]
        > È necessario aprire la porta TCP 1433 in uscita nel firewall per consentire all'agente client di comunicare con il server.


    2.  Immettere un nome per l'agente.

    3.  Selezionare **Crea e genera chiave**.

    4.  Copiare la chiave dell'agente negli Appunti.
        
        ![Creazione di un nuovo agente di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Selezionare **OK** per chiudere la pagina **Seleziona agente di sincronizzazione**.

    6.  Nel computer SQL Server individuare ed eseguire l'app dell'agente di sincronizzazione client.

        ![App dell'agente client di sincronizzazione dati](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  Nell'app dell'agente di sincronizzazione selezionare **Submit Agent Key** (Invia chiave agente). Verrà aperta la finestra di dialogo **Sync Metadata Database Configuration** (Configurazione del database dei metadati di sincronizzazione).

    8.  Nella finestra di dialogo **Sync Metadata Database Configuration** (Configurazione del database dei metadati di sincronizzazione) incollare la chiave dell'agente copiata dal portale di Azure. Specificare anche le credenziali esistenti per il server di database SQL di Azure in cui si trova il database dei metadati. (Se è stato creato un nuovo database dei metadati, il database si trova nello stesso server del database hub.) Selezionare **OK** e attendere che la configurazione venga completata.

        ![Immettere la chiave dell'agente e le credenziali del server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Se viene visualizzato un errore del firewall a questo punto, è necessario creare una regola del firewall in Azure per consentire il traffico in ingresso dal computer SQL Server. È possibile creare manualmente la regola nel portale, ma può risultare più semplice crearla in SQL Server Management Studio (SSMS). In SSMS provare a connettersi al database hub in Azure. Immettere il nome nel formato \<nome_database_hub\>.database.windows.net. Per configurare la regola del firewall di Azure, seguire i passaggi nella finestra di dialogo. Tornare quindi all'app dell'agente di sincronizzazione client.

    9.  Nell'app dell'agente di sincronizzazione client fare clic su **Registra** per registrare un database di SQL Server con l'agente. Verrà aperta la finestra di dialogo **Configurazione di SQL Server**.

        ![Aggiungere e configurare un database di SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. Nella finestra di dialogo **Configurazione di SQL Server** scegliere se effettuare la connessione mediante l'autenticazione di SQL Server o l'autenticazione di Windows. Se si sceglie l'autenticazione di SQL Server, immettere le credenziali esistenti. Specificare il nome dell'istanza di SQL Server e il nome del database che si vuole sincronizzare. Fare clic su **Test connessione** per testare le impostazioni. Selezionare quindi **Salva**. Il database registrato verrà visualizzato nell'elenco.

        ![Il database di SQL Server è ora registrato](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. È ora possibile chiudere l'app dell'agente di sincronizzazione client.

    12. Nella pagina **Configura database locale** del portale selezionare **Selezionare il database**. Verrà aperta la pagina **Seleziona database**.

    13. Nella pagina **Seleziona database**, nel campo **Nome membro di sincronizzazione**, specificare un nome per il nuovo membro di sincronizzazione. Questo nome è distinto dal nome del database stesso. Selezionare il database nell'elenco. Nel campo **Direzioni sincronizzazione** selezionare Sincronizzazione bidirezionale, Verso l'hub e Dall'hub.

        ![Selezionare il database locale](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Selezionare **OK** per chiudere la finestra **Seleziona database**. Selezionare quindi **OK** per chiudere la pagina **Configura database locale** e attendere che il nuovo membro di sincronizzazione venga creato e distribuito. Fare infine clic su **OK** per chiudere la pagina **Selezionare i membri di sincronizzazione**.

        ![Database locale aggiunto al gruppo di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Per connettersi a sincronizzazione dati SQL e all'agente locale, aggiungere il proprio nome utente al ruolo `DataSync_Executor`. Sincronizzazione dati crea questo ruolo nell'istanza di SQL Server.

## <a name="step-3---configure-sync-group"></a>Passaggio 3 - Configurare il gruppo di sincronizzazione

Dopo avere creato e distribuito i nuovi membri del gruppo di sincronizzazione, nella pagina **Nuovo gruppo di sincronizzazione** viene evidenziato il passaggio 3 **Configura gruppo di sincronizzazione**.

1.  Nella pagina **Tabelle** selezionare un database nell'elenco dei membri del gruppo di sincronizzazione e quindi selezionare **Aggiorna schema**.

2.  Nell'elenco delle tabelle disponibili selezionare le tabelle che si vuole sincronizzare.

    ![Selezionare le tabelle da sincronizzare](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Per impostazione predefinita vengono selezionate tutte le colonne nella tabella. Se non si vogliono sincronizzare tutte le colonne, disabilitare la casella di controllo per le colonne che non si vuole sincronizzare. Assicurarsi di lasciare selezionata la colonna chiave primaria.

    ![Selezionare i campi da sincronizzare](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Infine, selezionare **Salva**.

## <a name="faq-about-setup-and-configuration"></a>Domande frequenti sull'installazione e la configurazione

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Con quale frequenza sincronizzazione dati sincronizza i dati? 
La frequenza minima è ogni 5 minuti.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>La sincronizzazione dati SQL crea ed esegue il provisioning di tabelle?

Se le tabelle dello schema di sincronizzazione non sono già state create nel database di destinazione, l'anteprima di sincronizzazione dati SQL le crea usando le colonne che seleziona l'utente. Questo comportamento tuttavia non produce uno schema pienamente fedele per i motivi seguenti:

-   Nella tabella di destinazione vengono create solo le colonne selezionate. Se alcune colonne nelle tabelle di origine non appartengono al gruppo di sincronizzazione, non verrà eseguito il provisioning di tali colonne nelle tabelle di destinazione.

-   Gli indici vengono creati solo per le colonne selezionate. Se gli indici della tabella di origine presentano colonne che non appartengono al gruppo di sincronizzazione, non verrà eseguito il provisioning di tali indici nelle tabelle di destinazione.

-   Non viene eseguito il provisioning degli indici nelle colonne di tipo XML.

-   Non viene eseguito il provisioning dei vincoli CHECK.

-   Non viene eseguito il provisioning dei trigger esistenti nelle tabelle di origine.

-   Non vengono create Viste e Stored procedure nel database di destinazione.

A causa di questi limiti, è consigliabile eseguire le operazioni seguenti:
-   Per gli ambienti di produzione, eseguire manualmente il provisioning dello schema di massima fedeltà.
-   Per provare il servizio, si può usare la funzionalità di provisioning automatico dell'anteprima di sincronizzazione dati SQL.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Perché vengono visualizzate tabelle che non risultano essere state create?  
Sincronizzazione dati crea tabelle laterali nel database utente per il rilevamento delle modifiche. Non eliminarle in quanto sono richieste per il funzionamento di sincronizzazione dati.

### <a name="is-my-data-convergent-after-a-sync"></a>I dati risultano convergenti dopo una sincronizzazione?

Non necessariamente. In un gruppo di sincronizzazione con un hub e tre spoke (A, B e C), le sincronizzazioni sono da hub ad A, hub a B e hub a C. Se si apporta una modifica al database A *dopo* la sincronizzazione da hub ad A, tale modifica viene scritta nel database B o C solo dopo la successiva attività di sincronizzazione.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Come si ottengono le modifiche dello schema in un gruppo di sincronizzazione?

È necessario eseguire le modifiche dello schema manualmente.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Come si esporta e importa un database con la sincronizzazione dei dati?
Dopo aver esportato un database come file con estensione `.bacpac` e averlo importato per creare un nuovo database, è necessario eseguire le due operazioni seguenti per usare la sincronizzazione dei dati nel nuovo database:
1.  Pulire le tabelle e gli oggetti di sincronizzazione dei dati nel **nuovo database** usando [questo script](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Questo script elimina tutti gli oggetti di sincronizzazione dei dati dal database.
2.  Creare di nuovo il gruppo di sincronizzazione con il nuovo database. Se il gruppo di sincronizzazione precedente non è più necessario, eliminarlo.

## <a name="faq-about-the-client-agent"></a>Domande frequenti sull'agente client

### <a name="why-do-i-need-a-client-agent"></a>Perché è necessario disporre di un agente client?

Il servizio anteprima di sincronizzazione dati SQL comunica con i database SQL Server tramite l'agente client. Questa funzionalità di sicurezza impedisce la comunicazione diretta con i database protetti da un firewall. Per comunicare con l'agente, il servizio anteprima di sincronizzazione dati SQL usa connessioni crittografate e un token univoco o *chiave dell'agente*. I database SQL Server autenticano l'agente usando la stringa di connessione o la chiave dell'agente. Questa struttura offre un livello elevato di sicurezza per i dati.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Quante istanze dell'interfaccia utente dell'agente locale è possibile eseguire?

È possibile eseguire una sola istanza dell'interfaccia utente.

### <a name="how-can-i-change-my-service-account"></a>In che modo si può modificare l'account del servizio?

Dopo avere installato un agente client, l'unico modo per modificare l'account del servizio consiste nel disinstallarlo e installare un nuovo agente client con un nuovo account di servizio.

### <a name="how-do-i-change-my-agent-key"></a>Come si modifica la chiave dell'agente?

Una chiave dell'agente può essere usata una sola volta da un agente. Non può essere riusata quando si rimuovere l'agente e se ne installa uno nuovo. Non può essere usata neppure con più agenti. Se è necessario creare una nuova chiave per un agente esistente, è necessario assicurarsi che la stessa chiave venga registrata con l'agente client e con il servizio anteprima di sincronizzazione dati SQL.

### <a name="how-do-i-retire-a-client-agent"></a>Come si ritira un agente client?

Per invalidare o ritirare immediatamente un agente, rigenerarne la chiave nel portale, ma non inviarla all'interfaccia utente dell'agente. La rigenerazione di una chiave invalida la chiave precedente, a prescindere che l'agente sia online o offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Come si sposta un agente client in un altro computer?

Se si vuole eseguire l'agente locale da un computer diverso da quello corrente, eseguire le operazioni seguenti:

1. Installare l'agente nel computer desiderato.

2. Accedere al portale di anteprima di sincronizzazione dati SQL e rigenerare una chiave dell'agente per il nuovo agente.

3. Usare l'interfaccia utente del nuovo agente per inviare la nuova chiave dell'agente.

4. Attendere che l'agente client scarichi l'elenco di database locali registrati in precedenza.

5. Immettere le credenziali di database per tutti i database visualizzati come non raggiungibili. Questi database devono essere raggiungibili dal nuovo computer in cui è installato l'agente.

## <a name="next-steps"></a>Passaggi successivi
A questo punto è stato creato un gruppo di sincronizzazione che include sia un'istanza di database SQL che un database di SQL Server.

Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md)
-   [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-database-best-practices-data-sync.md)
-   [Monitorare la sincronizzazione dati SQL di Azure con OMS Log Analytics](sql-database-sync-monitor-oms.md)
-   [Risolvere i problemi della sincronizzazione dati SQL di Azure](sql-database-troubleshoot-data-sync.md)

-   Esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL:
    -   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Scaricare la documentazione dell'API REST di sincronizzazione dati SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
