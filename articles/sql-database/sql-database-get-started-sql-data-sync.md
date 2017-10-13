---
title: Introduzione all'anteprima di sincronizzazione dati SQL di Azure | Documentazione Microsoft
description: Questa esercitazione fornisce informazioni per iniziare a usare l'anteprima di sincronizzazione dati SQL di Azure.
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: douglasl
ms.openlocfilehash: 1943e87ffd991d099ab655af5a7d16d7f1608d22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-sql-data-sync-preview"></a>Introduzione all'anteprima di sincronizzazione dati di SQL Azure
In questa esercitazione si imparerà a configurare sincronizzazione dati SQL di Azure creando un gruppo di sincronizzazione ibrido che contiene sia istanze del database SQL di Azure che istanze di SQL Server. Il nuovo gruppo di sincronizzazione ha una configurazione completa ed esegue la sincronizzazione in base alla pianificazione impostata.

Per questa esercitazione è necessario avere già un certo livello di esperienza precedente con il database SQL di Azure e SQL Server. 

Per una panoramica di sincronizzazione dati SQL, vedere [Sincronizzare i dati](sql-database-sync-data.md).

Per esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL, vedere gli articoli seguenti:
-   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!NOTE]
> La documentazione tecnica completa impostata per la sincronizzazione dati SQL di Azure, che si trovava in precedenza su MSDN, è ora disponibile in formato PDF. Scaricarla [qui](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true).

## <a name="step-1---create-sync-group"></a>Passaggio 1 - Creare un gruppo di sincronizzazione

### <a name="locate-the-data-sync-settings"></a>Individuare le impostazioni di sincronizzazione dati

1.  Nel browser passare al portale di Azure.

2.  Nel portale individuare i database SQL dal dashboard o dall'icona Database SQL sulla barra degli strumenti.

    ![Elenco dei database SQL di Azure](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  Nel pannello **Database SQL** selezionare il database SQL esistente da usare come database hub per la sincronizzazione dati. Verrà aperto il pannello del database SQL.

4.  Nel pannello del database SQL per il database selezionato selezionare **Sincronizza con altri database**. Verrà aperto il pannello di sincronizzazione dati.

    ![Opzione Sincronizza con altri database](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Creare un nuovo gruppo di sincronizzazione

1.  Nel pannello di sincronizzazione dati selezionare **Nuovo gruppo di sincronizzazione**. Verrà aperto il pannello **Nuovo gruppo di sincronizzazione** con evidenziato il passaggio 1 **Crea gruppo di sincronizzazione**. Verrà aperto anche il pannello **Crea gruppo di sincronizzazione dati**.

2.  Nel pannello **Crea gruppo di sincronizzazione dati** eseguire queste operazioni:

    1.  Nel campo **Nome gruppo di sincronizzazione** immettere un nome per il nuovo gruppo di sincronizzazione.

    2.  Nella sezione **Database dei metadati di sincronizzazione** scegliere se creare un nuovo database (scelta consigliata) o usare un database esistente.

        > [!NOTE]
        > Microsoft consiglia di creare un nuovo database vuoto da usare come database dei metadati di sincronizzazione. La sincronizzazione dati crea tabelle in questo database ed esegue un carico di lavoro frequente. Questo database viene automaticamente condiviso come database dei metadati di sincronizzazione per tutti i gruppi di sincronizzazione nell'area selezionata. Non è possibile modificare il database dei metadati di sincronizzazione o il relativo nome senza eliminarlo.

        Se si sceglie **Nuovo database** selezionare **Crea nuovo database**. Verrà aperto il pannello **Database SQL**. Nel pannello **Database SQL** specificare un nome per il nuovo database e configurarlo. Selezionare **OK**.

        Se si sceglie **Utilizza database esistente**, selezionare il database nell'elenco.

    3.  Nella sezione **Sincronizzazione automatica** selezionare innanzitutto **Sì** o **No**.

        Se si sceglie **Sì**, nella sezione **Frequenza sincronizzazione** immettere un numero e selezionare Secondi, Minuti, Ore o Giorni.

        ![Specificare la frequenza di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  Nella sezione **Risoluzione dei conflitti** selezionare "Priorità hub" o "Priorità client".

        ![Specificare come vengono risolti i conflitti](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Selezionare **OK** e attendere che il nuovo gruppo di sincronizzazione venga creato e distribuito.

## <a name="step-2---add-sync-members"></a>Passaggio 2 - Aggiungere i membri di sincronizzazione

Al termine della creazione e distribuzione del nuovo gruppo di sincronizzazione, nel pannello **Nuovo gruppo di sincronizzazione** viene evidenziato il passaggio 2 **Aggiungi membri di sincronizzazione**.

Nella sezione **Database hub** immettere le credenziali esistenti per il server di database SQL in cui si trova il database hub. Non immettere *nuove* credenziali in questa sezione.

![Database hub aggiunto al gruppo di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

## <a name="add-an-azure-sql-database"></a>Aggiungere un database SQL di Azure

Nella sezione **Database membro** aggiungere facoltativamente un database SQL di Azure al gruppo di sincronizzazione selezionando **Aggiungi un database di Azure**. Verrà aperto il pannello **Configura database di Azure**.

Nel pannello **Configura database di Azure** eseguire le operazioni seguenti:

1.  Nel campo **Nome membro di sincronizzazione** specificare un nome per il nuovo membro di sincronizzazione. Questo nome è distinto dal nome del database stesso.

2.  Nel campo **Sottoscrizione** selezionare la sottoscrizione di Azure associata per la fatturazione.

3.  Nel campo **Server di Azure SQL** selezionare il server di database SQL esistente.

4.  Nel campo **Database SQL di Azure** selezionare il database SQL esistente.

5.  Nel campo **Direzioni sincronizzazione** selezionare Sincronizzazione bidirezionale, Verso l'hub e Dall'hub.

    ![Aggiungere un nuovo membro di sincronizzazione del database SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  Nei campi **Nome utente** e **Password** immettere le credenziali esistenti per il server di database SQL in cui si trova il database membro. Non immettere *nuove* credenziali in questa sezione.

7.  Selezionare **OK** e attendere che il nuovo membro di sincronizzazione venga creato e distribuito.

    ![Il nuovo membro di sincronizzazione del database SQL è stato aggiunto](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

## <a name="add-an-on-premises-sql-server-database"></a>Aggiungere un database di SQL Server locale

Nella sezione **Database membro** aggiungere facoltativamente un'istanza di SQL Server locale al gruppo di sincronizzazione selezionando **Aggiungi un database locale**. Verrà aperto il pannello **Configurare database locale**.

Nel pannello **Configura database locale** eseguire le operazioni seguenti:

1.  Selezionare **Scegliere il gateway dell'agente di sincronizzazione**. Verrà aperto il pannello **Seleziona agente di sincronizzazione**.

    ![Scegliere il gateway dell'agente di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  Nel pannello **Scegliere il gateway dell'agente di sincronizzazione** specificare se si vuole usare un agente esistente o crearne uno nuovo.

    Se si sceglie **Agenti esistenti**, selezionare l'agente esistente nell'elenco.

    Se si sceglie **Consente di creare un nuovo agente**, eseguire le operazioni seguenti:

    1.  Scaricare il software dell'agente di sincronizzazione client dal collegamento specificato e installarlo nel computer in cui si trova SQL Server.
 
        > [!IMPORTANT]
        > È necessario aprire la porta TCP 1433 in uscita nel firewall per consentire all'agente client di comunicare con il server.


    2.  Immettere un nome per l'agente.

    3.  Selezionare **Crea e genera chiave**.

    4.  Copiare la chiave dell'agente negli Appunti.
        
        ![Creazione di un nuovo agente di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Selezionare **OK** per chiudere il pannello **Seleziona agente di sincronizzazione**.

    6.  Nel computer SQL Server individuare ed eseguire l'app dell'agente di sincronizzazione client.

        ![App dell'agente client di sincronizzazione dati](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  Nell'app dell'agente di sincronizzazione selezionare **Submit Agent Key** (Invia chiave agente). Verrà aperta la finestra di dialogo **Sync Metadata Database Configuration** (Configurazione del database dei metadati di sincronizzazione).

    8.  Nella finestra di dialogo **Sync Metadata Database Configuration** (Configurazione del database dei metadati di sincronizzazione) incollare la chiave dell'agente copiata dal portale di Azure. Specificare anche le credenziali esistenti per il server di database SQL di Azure in cui si trova il database dei metadati. (Se è stato creato un nuovo database dei metadati, il database si trova nello stesso server del database hub.) Selezionare **OK** e attendere che la configurazione venga completata.

        ![Immettere la chiave dell'agente e le credenziali del server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Se viene visualizzato un errore del firewall a questo punto, è necessario creare una regola del firewall in Azure per consentire il traffico in ingresso dal computer SQL Server. È possibile creare manualmente la regola nel portale, ma può risultare più semplice crearla in SQL Server Management Studio (SSMS). In SSMS provare a connettersi al database hub in Azure. Immettere il nome nel formato \<nome_database_hub\>.database.windows.net. Seguire i passaggi nella finestra di dialogo per configurare la regola del firewall di Azure. Tornare quindi all'app dell'agente di sincronizzazione client.

    9.  Nell'app dell'agente di sincronizzazione client fare clic su **Registra** per registrare un database di SQL Server con l'agente. Verrà aperta la finestra di dialogo **Configurazione di SQL Server**.

        ![Aggiungere e configurare un database di SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. Nella finestra di dialogo **Configurazione di SQL Server** scegliere se effettuare la connessione mediante l'autenticazione di SQL Server o l'autenticazione di Windows. Se si sceglie l'autenticazione di SQL Server, immettere le credenziali esistenti. Specificare il nome dell'istanza di SQL Server e il nome del database che si vuole sincronizzare. Fare clic su **Test connessione** per testare le impostazioni. Selezionare quindi **Salva**. Il database registrato verrà visualizzato nell'elenco.

        ![Il database di SQL Server è ora registrato](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. È ora possibile chiudere l'app dell'agente di sincronizzazione client.

    12. Nel pannello **Configura database locale** del portale selezionare **Selezionare il database**. Verrà aperto il pannello **Seleziona database**.

    13. Nel pannello **Seleziona database**, nel campo **Nome membro di sincronizzazione**, specificare un nome per il nuovo membro di sincronizzazione. Questo nome è distinto dal nome del database stesso. Selezionare il database nell'elenco. Nel campo **Direzioni sincronizzazione** selezionare Sincronizzazione bidirezionale, Verso l'hub e Dall'hub.

        ![Selezionare il database locale](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Selezionare **OK** per chiudere il pannello **Seleziona database**. Selezionare quindi **OK** per chiudere il pannello **Configura database locale** e attendere che il nuovo membro di sincronizzazione venga creato e distribuito. Fare infine clic su **OK** per chiudere il pannello **Selezionare i membri di sincronizzazione**.

        ![Database locale aggiunto al gruppo di sincronizzazione](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Per connettersi a sincronizzazione dati SQL e all'agente locale, aggiungere il proprio nome utente al ruolo `DataSync_Executor`. Sincronizzazione dati crea questo ruolo nell'istanza di SQL Server.

## <a name="step-3---configure-sync-group"></a>Passaggio 3 - Configurare il gruppo di sincronizzazione

Dopo avere creato e distribuito i nuovi membri del gruppo di sincronizzazione, nel pannello **Nuovo gruppo di sincronizzazione** viene evidenziato il passaggio 3 **Configura gruppo di sincronizzazione**.

1.  Nel pannello **Tabelle** selezionare un database nell'elenco dei membri del gruppo di sincronizzazione e quindi selezionare **Aggiorna schema**.

2.  Nell'elenco delle tabelle disponibili selezionare le tabelle che si vuole sincronizzare.

    ![Selezionare le tabelle da sincronizzare](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Per impostazione predefinita vengono selezionate tutte le colonne nella tabella. Se non si vogliono sincronizzare tutte le colonne, disabilitare la casella di controllo per le colonne che non si vuole sincronizzare. Assicurarsi di lasciare selezionata la colonna chiave primaria.

    ![Selezionare i campi da sincronizzare](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Infine, selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
A questo punto è stato creato un gruppo di sincronizzazione che include sia un'istanza di database SQL che un database di SQL Server.

Per altre informazioni sul database SQL e la sincronizzazione dati SQL, vedere:

-   [Scaricare la documentazione tecnica completa di sincronizzazione dati SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)
-   [Scaricare la documentazione dell'API REST di sincronizzazione dati SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)
-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
