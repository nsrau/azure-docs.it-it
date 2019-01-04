---
title: Configurare la sincronizzazione dati SQL di Azure | Microsoft Docs
description: Questa esercitazione illustra come configurare la sincronizzazione dati SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: 9175ed0b4f362a40e1d29a20a8378854b5f4cc81
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310387"
---
# <a name="tutorial-set-up-sql-data-sync-to-sync-data-between-azure-sql-database-and-sql-server-on-premises"></a>Esercitazione: Configurare la sincronizzazione dati SQL per sincronizzare i dati tra il database SQL di Azure e SQL Server in locale

In questa esercitazione si imparerà a configurare sincronizzazione dati SQL di Azure creando un gruppo di sincronizzazione ibrido che contiene sia istanze del database SQL di Azure che istanze di SQL Server. Il nuovo gruppo di sincronizzazione ha una configurazione completa ed esegue la sincronizzazione in base alla pianificazione impostata.

Per questa esercitazione è necessario avere già un certo livello di esperienza precedente con il database SQL di Azure e SQL Server.

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md).

Per esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL, vedere gli articoli seguenti:

- [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
- [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Passaggio 1 - Creare un gruppo di sincronizzazione

### <a name="locate-the-data-sync-settings"></a>Individuare le impostazioni di sincronizzazione dati

1. Nel browser passare al portale di Azure.

2. Nel portale individuare i database SQL dal dashboard o dall'icona Database SQL sulla barra degli strumenti.

    ![Elenco dei database SQL di Azure](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3. Nel pagina **Database SQL** selezionare il database SQL esistente da usare come database hub per la sincronizzazione dati. Verrà aperta la pagina Database SQL.

    Il database hub è l'endpoint centrale della topologia di sincronizzazione in cui un gruppo di sincronizzazione ha più endpoint del database. Tutti gli altri endpoint del database nello stesso gruppo di sincronizzazione, ovvero tutti i database membri, eseguono la sincronizzazione con il database hub.

4. Nella pagina Database SQL per il database selezionato selezionare **Sincronizza con altri database**. Verrà aperta la pagina Sincronizzazione dati.

    ![Opzione Sincronizza con altri database](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Creare un nuovo gruppo di sincronizzazione

1. Nel pagina Sincronizzazione dati selezionare **Nuovo gruppo di sincronizzazione**. Verrà aperta la pagina **Nuovo gruppo di sincronizzazione** con evidenziato il passaggio 1 **Crea gruppo di sincronizzazione**. Verrà aperta anche la pagina **Crea gruppo di sincronizzazione dati**.

2. Nella pagina **Crea gruppo di sincronizzazione dati** eseguire le operazioni seguenti:

   1. Nel campo **Nome gruppo di sincronizzazione** immettere un nome per il nuovo gruppo di sincronizzazione.

   2. Nella sezione **Database dei metadati di sincronizzazione** scegliere se creare un nuovo database (scelta consigliata) o usare un database esistente.

        > [!NOTE]
        > Microsoft consiglia di creare un nuovo database vuoto da usare come database dei metadati di sincronizzazione. La sincronizzazione dati crea tabelle in questo database ed esegue un carico di lavoro frequente. Questo database viene automaticamente condiviso come database dei metadati di sincronizzazione per tutti i gruppi di sincronizzazione nell'area selezionata. Non è possibile modificare il database dei metadati di sincronizzazione o il nome di questo senza rimuovere tutti i gruppi e gli agenti di sincronizzazione nell'area.

        Se si sceglie **Nuovo database** selezionare **Crea nuovo database**. Verrà aperta la pagina **Database SQL**. Nella pagina **Database SQL** specificare un nome per il nuovo database e configurarlo. Selezionare **OK**.

        Se si sceglie **Utilizza database esistente**, selezionare il database nell'elenco.

   3. Nella sezione **Sincronizzazione automatica** selezionare innanzitutto **Sì** o **No**.

        Se si sceglie **Sì**, nella sezione **Frequenza sincronizzazione** immettere un numero e selezionare Secondi, Minuti, Ore o Giorni.

        ![Specificare la frequenza di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

   4. Nella sezione **Risoluzione dei conflitti** selezionare "Priorità hub" o "Priorità client".

        "Priorità hub" significa che, quando si verifica un conflitto, i dati nel database hub sovrascrivono i dati in conflitto nel database membro. "Priorità client" significa che, quando si verifica un conflitto, i dati nel database membro sovrascrivono i dati in conflitto nel database hub.

       ![Specificare come vengono risolti i conflitti](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

   5. Selezionare **OK** e attendere che il nuovo gruppo di sincronizzazione venga creato e distribuito.

## <a name="step-2---add-sync-members"></a>Passaggio 2 - Aggiungere i membri di sincronizzazione

Al termine della creazione e distribuzione del nuovo gruppo di sincronizzazione, nella pagina **Nuovo gruppo di sincronizzazione** viene evidenziato il passaggio 2 **Aggiungi membri di sincronizzazione**.

Nella sezione **Database hub** immettere le credenziali esistenti per il server di database SQL in cui si trova il database hub. Non immettere *nuove* credenziali in questa sezione.

![Database hub aggiunto al gruppo di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Aggiungere un database SQL di Azure

Nella sezione **Database membro** aggiungere facoltativamente un database SQL di Azure al gruppo di sincronizzazione selezionando **Aggiungi un database di Azure**. Verrà aperta la pagina **Configura database di Azure**.

Nella pagina **Configura database di Azure** eseguire le operazioni seguenti:

1. Nel campo **Nome membro di sincronizzazione** specificare un nome per il nuovo membro di sincronizzazione. Questo nome è distinto dal nome del database stesso.

2. Nel campo **Sottoscrizione** selezionare la sottoscrizione di Azure associata per la fatturazione.

3. Nel campo **Server di Azure SQL** selezionare il server di database SQL esistente.

4. Nel campo **Database SQL di Azure** selezionare il database SQL esistente.

5. Nel campo **Direzioni sincronizzazione** selezionare Sincronizzazione bidirezionale, Verso l'hub e Dall'hub.

    ![Aggiungere un nuovo membro di sincronizzazione del database SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6. Nei campi **Nome utente** e **Password** immettere le credenziali esistenti per il server di database SQL in cui si trova il database membro. Non immettere *nuove* credenziali in questa sezione.

7. Selezionare **OK** e attendere che il nuovo membro di sincronizzazione venga creato e distribuito.

    ![Il nuovo membro di sincronizzazione del database SQL è stato aggiunto](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Aggiungere un database SQL Server locale

Nella sezione **Database membro** aggiungere facoltativamente un'istanza di SQL Server locale al gruppo di sincronizzazione selezionando **Aggiungi un database locale**. Verrà aperta la pagina **Configura database locale**.

Nella pagina **Configura database locale** eseguire le operazioni seguenti:

1. Selezionare **Scegliere il gateway dell'agente di sincronizzazione**. Verrà aperta la pagina **Seleziona agente di sincronizzazione**.

    ![Scegliere il gateway dell'agente di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2. Nella pagina **Scegliere il gateway dell'agente di sincronizzazione** specificare se si vuole usare un agente esistente o crearne uno nuovo.

    Se si sceglie **Agenti esistenti**, selezionare l'agente esistente nell'elenco.

    Se si sceglie **Consente di creare un nuovo agente**, eseguire le operazioni seguenti:

   1. Scaricare il software dell'agente di sincronizzazione client dal collegamento specificato e installarlo nel computer in cui si trova SQL Server. È anche possibile scaricare l'agente di sincronizzazione dei dati direttamente da [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

        > [!IMPORTANT]
        > È necessario aprire la porta TCP 1433 in uscita nel firewall per consentire all'agente client di comunicare con il server.

   2. Immettere un nome per l'agente.

   3. Selezionare **Crea e genera chiave**.

   4. Copiare la chiave dell'agente negli Appunti.

        ![Creazione di un nuovo agente di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

   5. Selezionare **OK** per chiudere la pagina **Seleziona agente di sincronizzazione**.

   6. Nel computer SQL Server individuare ed eseguire l'app dell'agente di sincronizzazione client.

        ![App dell'agente client di sincronizzazione dati](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

   7. Nell'app dell'agente di sincronizzazione selezionare **Submit Agent Key** (Invia chiave agente). Verrà aperta la finestra di dialogo **Sync Metadata Database Configuration** (Configurazione del database dei metadati di sincronizzazione).

   8. Nella finestra di dialogo **Sync Metadata Database Configuration** (Configurazione del database dei metadati di sincronizzazione) incollare la chiave dell'agente copiata dal portale di Azure. Specificare anche le credenziali esistenti per il server di database SQL di Azure in cui si trova il database dei metadati. (Se è stato creato un nuovo database dei metadati, il database si trova nello stesso server del database hub.) Selezionare **OK** e attendere che la configurazione venga completata.

        ![Immettere la chiave dell'agente e le credenziali del server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Se viene visualizzato un errore del firewall a questo punto, è necessario creare una regola del firewall in Azure per consentire il traffico in ingresso dal computer SQL Server. È possibile creare manualmente la regola nel portale, ma può risultare più semplice crearla in SQL Server Management Studio (SSMS). In SSMS provare a connettersi al database hub in Azure. Immettere il nome nel formato <nome_database_hub>.database.windows.net. Per configurare la regola del firewall di Azure, seguire i passaggi nella finestra di dialogo. Tornare quindi all'app dell'agente di sincronizzazione client.

   9. Nell'app dell'agente di sincronizzazione client fare clic su **Registra** per registrare un database di SQL Server con l'agente. Verrà aperta la finestra di dialogo **Configurazione di SQL Server**.

        ![Aggiungere e configurare un database di SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

   10. Nella finestra di dialogo **Configurazione di SQL Server** scegliere se effettuare la connessione mediante l'autenticazione di SQL Server o l'autenticazione di Windows. Se si sceglie l'autenticazione di SQL Server, immettere le credenziali esistenti. Specificare il nome dell'istanza di SQL Server e il nome del database che si vuole sincronizzare. Fare clic su **Test connessione** per testare le impostazioni. Selezionare quindi **Salva**. Il database registrato verrà visualizzato nell'elenco.

        ![Il database di SQL Server è ora registrato](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

   11. È ora possibile chiudere l'app dell'agente di sincronizzazione client.

   12. Nella pagina **Configura database locale** del portale selezionare **Selezionare il database**. Verrà aperta la pagina **Seleziona database**.

   13. Nella pagina **Seleziona database**, nel campo **Nome membro di sincronizzazione**, specificare un nome per il nuovo membro di sincronizzazione. Questo nome è distinto dal nome del database stesso. Selezionare il database nell'elenco. Nel campo **Direzioni sincronizzazione** selezionare Sincronizzazione bidirezionale, Verso l'hub e Dall'hub.

        ![Selezionare il database locale](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

   14. Selezionare **OK** per chiudere la finestra **Seleziona database**. Selezionare quindi **OK** per chiudere la pagina **Configura database locale** e attendere che il nuovo membro di sincronizzazione venga creato e distribuito. Fare infine clic su **OK** per chiudere la pagina **Selezionare i membri di sincronizzazione**.

        ![Database locale aggiunto al gruppo di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3. Per connettersi a sincronizzazione dati SQL e all'agente locale, aggiungere il proprio nome utente al ruolo `DataSync_Executor`. Sincronizzazione dati crea questo ruolo nell'istanza di SQL Server.

## <a name="step-3---configure-sync-group"></a>Passaggio 3 - Configurare il gruppo di sincronizzazione

Dopo avere creato e distribuito i nuovi membri del gruppo di sincronizzazione, nella pagina **Nuovo gruppo di sincronizzazione** viene evidenziato il passaggio 3 **Configura gruppo di sincronizzazione**.

1. Nella pagina **Tabelle** selezionare un database nell'elenco dei membri del gruppo di sincronizzazione e quindi selezionare **Aggiorna schema**.

2. Nell'elenco delle tabelle disponibili selezionare le tabelle che si vuole sincronizzare.

    ![Selezionare le tabelle da sincronizzare](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3. Per impostazione predefinita vengono selezionate tutte le colonne nella tabella. Se non si vogliono sincronizzare tutte le colonne, disabilitare la casella di controllo per le colonne che non si vuole sincronizzare. Assicurarsi di lasciare selezionata la colonna chiave primaria.

    ![Selezionare i campi da sincronizzare](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4. Infine, selezionare **Salva**.

## <a name="faq-about-setup-and-configuration"></a>Domande frequenti sull'installazione e la configurazione

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Con quale frequenza viene eseguita la sincronizzazione dati?

La durata minima tra le attivazioni delle sincronizzazioni è di cinque minuti.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>La sincronizzazione dati SQL crea tabelle e ne esegue il provisioning?

Se le tabelle dello schema di sincronizzazione non sono state ancora create nel database di destinazione, la sincronizzazione dati SQL crea tali tabelle usando le colonne selezionate dall'utente. Questo comportamento tuttavia non produce uno schema pienamente fedele per i motivi seguenti:

- Nella tabella di destinazione vengono create solo le colonne selezionate. Se alcune colonne nelle tabelle di origine non appartengono al gruppo di sincronizzazione, non verrà eseguito il provisioning di tali colonne nelle tabelle di destinazione.
- Gli indici vengono creati solo per le colonne selezionate. Se gli indici della tabella di origine presentano colonne che non appartengono al gruppo di sincronizzazione, non verrà eseguito il provisioning di tali indici nelle tabelle di destinazione.
- Non viene eseguito il provisioning degli indici nelle colonne di tipo XML.
- Non viene eseguito il provisioning dei vincoli CHECK.
- Non viene eseguito il provisioning dei trigger esistenti nelle tabelle di origine.
- Nel database di destinazione non vengono create viste e stored procedure.

A causa di questi limiti, è consigliabile eseguire le operazioni seguenti:

- Per gli ambienti di produzione, eseguire manualmente il provisioning dello schema di massima fedeltà.
- Per provare il servizio, si può usare la funzionalità di provisioning automatico della sincronizzazione dati SQL.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Perché vengono visualizzate tabelle che non risultano essere state create?

Sincronizzazione dati crea tabelle laterali nel database utente per il rilevamento delle modifiche. Non eliminarle in quanto sono richieste per il funzionamento di sincronizzazione dati.

### <a name="is-my-data-convergent-after-a-sync"></a>I dati risultano convergenti dopo una sincronizzazione?

Non necessariamente. In un gruppo di sincronizzazione con un hub e tre spoke (A, B e C), le sincronizzazioni sono da hub ad A, hub a B e hub a C. Se si apporta una modifica al database A *dopo* la sincronizzazione da hub ad A, tale modifica viene scritta nel database B o C solo dopo la successiva attività di sincronizzazione.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Come si applicano a un gruppo di sincronizzazione le modifiche apportate allo schema?

È necessario apportare e propagare manualmente tutte le modifiche dello schema.

1. Replicare manualmente le modifiche dello schema nell'hub e in tutti i membri di sincronizzazione.
2. Aggiornare lo schema di sincronizzazione.

**Aggiunta di nuove tabelle e colonne**.

Le nuove tabelle e colonne non influiscono sulla sincronizzazione corrente. La sincronizzazione dei dati ignora le nuove tabelle e colonne fino a quando non vengono aggiunte allo schema di sincronizzazione. Quando si aggiungono nuovi oggetti di database, questa è la migliore sequenza da seguire:

1. Aggiungere le nuove tabelle o colonne all'hub e a tutti i membri di sincronizzazione.
2. Aggiungere le nuove tabelle o colonne allo schema di sincronizzazione.
3. Iniziare a inserire valori nelle nuove tabelle e colonne.

**Modifica del tipo di dati di una colonna**.

Quando si modifica il tipo di dati di una colonna esistente, la sincronizzazione dei dati continua a funzionare, fino a quando i nuovi valori sono conformi al tipo di dati originale definito nello schema di sincronizzazione. Ad esempio, se si modifica il tipo nel database di origine da **int** a **bigint**, la sincronizzazione dei dati continua a funzionare fino a quando non si inserisce un valore che è troppo grande per il tipo di dati **int**. Per completare la modifica, replicare la modifica dello schema manualmente nell'hub e in tutti i membri di sincronizzazione, quindi aggiornare lo schema di sincronizzazione.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Come si esporta e importa un database con la sincronizzazione dati?

Dopo aver esportato un database come file con estensione `.bacpac` e averlo importato per creare un nuovo database, è necessario eseguire le due operazioni seguenti per usare la sincronizzazione dei dati nel nuovo database:

1. Pulire le tabelle e gli oggetti di sincronizzazione dei dati nel **nuovo database** usando [questo script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Questo script elimina tutti gli oggetti di sincronizzazione dei dati dal database.
2. Creare di nuovo il gruppo di sincronizzazione con il nuovo database. Se il gruppo di sincronizzazione precedente non è più necessario, eliminarlo.

## <a name="faq-about-the-client-agent"></a>Domande frequenti sull'agente client

Per le domande frequenti sull'agente client, vedere [Domande frequenti sull'agente](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato creato un gruppo di sincronizzazione che include sia un'istanza di database SQL che un database di SQL Server.

Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   Panoramica: [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md)
-   Configurare la sincronizzazione dati
    - Con PowerShell
        -  [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente di sincronizzazione dei dati: [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL di Azure](sql-database-data-sync-agent.md)
-   Procedure consigliate: [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-database-best-practices-data-sync.md)
-   Monitoraggio: [Monitorare la sincronizzazione dati SQL con Log Analytics](sql-database-sync-monitor-oms.md)
-   Risoluzione dei problemi: [Risolvere i problemi della sincronizzazione dati SQL di Azure](sql-database-troubleshoot-data-sync.md)
-   Aggiornare lo schema di sincronizzazione
    -   Con Transact-SQL: [Automatizzare la replica delle modifiche dello schema nella sincronizzazione dati SQL di Azure](sql-database-update-sync-schema.md)
    -   Con PowerShell: [Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente](scripts/sql-database-sync-update-schema.md)

Per altre informazioni sul database SQL, vedere:

- [Panoramica del database SQL](sql-database-technical-overview.md)
- [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
