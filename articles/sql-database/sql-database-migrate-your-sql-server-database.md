---
title: Migrare un database SQL Server nel database SQL di Azure | Documentazione Microsoft
description: Informazioni sulla migrazione di un database SQL Server a un database SQL di Azure.
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: cf128e84cfa69a259ff529caebb910840dcbaede
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migrare un database SQL Server in un database SQL di Azure

La migrazione di un database SQL Server a un database SQL di Azure è un processo in tre parti che prevede la preparazione, l'esportazione e l'importazione del database. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare un database in un'istanza di SQL Server per la migrazione a un database SQL di Azure tramite [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA)
> * Esportare il database in un file BACPAC
> * Importare il file BACPAC in un database SQL di Azure

Prima di iniziare, verificare la disponibilità di quanto segue:

- La versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Installando SQL Server Management Studio viene installata anche la versione più recente di SQLPackage, un'utilità della riga di comando utilizzabile per automatizzare varie attività di sviluppo di database. 
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- Un database da migrare. Questa esercitazione usa il [database OLTP SQL Server 2008R2 AdventureWorks](https://msftdbprodsamples.codeplex.com/releases/view/59211) in un'istanza di SQL Server 2008 R2 o versione più recenti, ma è possibile usare qualsiasi database di propria scelta. Per risolvere i problemi di compatibilità, usare [Visual Studio Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).

## <a name="prepare-for-migration"></a>Preparare la migrazione

Tutto è pronto per preparare la migrazione. Seguire questi passaggi per usare **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)** per valutare l'idoneità del database alla migrazione nel database SQL Azure.

1. Aprire **Data Migration Assistant**. È possibile eseguire DMA in qualsiasi computer con connettività all'istanza di SQL Server contenente il database che si intende migrare, non è necessario installarlo nel computer che ospita l'istanza di SQL Server.

     ![aprire Data Migration Assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. Nel menu a sinistra fare clic su **+ Nuovo** per creare un progetto **Valutazione**. Compilare il modulo con un **Nome progetto** (per tutti gli altri campi lasciare i valori predefiniti) e fare clic su **Crea**. Si apre la pagina **Opzioni**.

     ![nuovo progetto di Data Migration Assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3. Nella pagina **Opzioni** fare clic su **Avanti**. La pagina **Seleziona origini**.

     ![nuova migrazione di dati opzioni](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-options.png) 

4. Nella pagina **Seleziona origini** immettere il nome dell'istanza di SQL Server contenente il server da migrare. Modificare gli altri valori di questa pagina come necessario. Fare clic su **Connetti**.

     ![nuova migrazione di dati selezione delle origini](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources.png)

5. Nella sezione **Aggiungi origini** della pagina **Seleziona origini** selezionare le caselle di controllo per verificare la compatibilità dei database. Fare clic su **Aggiungi**.

     ![nuova migrazione di dati selezione delle origini](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources-add.png)

6. Fare clic su **Start Assessment** (Avvia valutazione).

     ![nuova migrazione di dati avvio della valutazione](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-start-assessment.png)

7. Al termine della valutazione prima di tutto controllare se il database è sufficientemente compatibile per la migrazione. Cercare il segno di spunta nel cerchio verde.

     ![nuova migrazione di dati valutazione della compatibilità](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-compatible.png)

8. Esaminare i risultati, a partire da **SQL Server feature parity** (Parità di funzionalità di SQL Server). In particolare esaminare le informazioni sulle funzionalità non supportate e parzialmente supportate e le informazioni fornite sulle azioni consigliate. 

     ![nuova migrazione di dati valutazione della parità](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. Fare clic su **Problemi di compatibilità**. In particolare esaminare le informazioni sui blocchi migrazione, le modifiche di comportamento e le funzionalità obsolete per ogni livello di compatibilità. Per il database AdventureWorks2008R2 rivedere le modifiche alla ricerca Full-Text rispetto a SQL Server 2008 e le modifiche apportate a SERVERPROPERTY('LCID') rispetto a SQL Server 2000. Per i dettagli su queste modifiche sono disponibili alcuni collegamenti ad altre informazioni. Molte opzioni di ricerca e impostazioni per la ricerca Full-Text sono state modificate 

   > [!IMPORTANT] 
   > Dopo la migrazione del database nel database SQL di Azure, è possibile scegliere di far funzionare il database al livello di compatibilità corrente (livello 100 per il database AdventureWorks2008R2) o a un livello superiore. Per altre informazioni sulle implicazioni e le opzioni per il funzionamento di un database a un livello di compatibilità specifico, vedere [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Livello di compatibilità ALTER DATABASE). Vedere anche [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) per informazioni sulle impostazioni a livello di database aggiuntive relative ai livelli di compatibilità.
   >

10. Facoltativamente fare clic su **Esporta report** per salvare il report come file JSON.
11. Chiudere Data Migration Assistant.

## <a name="export-to-bacpac-file"></a>Esportare in un file BACPAC 

Un file BACPAC è un file ZIP con un'estensione bacpac contenente i metadati e dati da un database di SQL Server. Un file BACPAC può essere archiviato nell'archiviazione BLOB di Azure o nell'archiviazione locale a scopo di semplice archiviazione o migrazione, ad esempio da SQL Server al database SQL Azure. Perché un'esportazione sia coerente dal punto di vista delle transazioni, è necessario assicurarsi che non venga eseguita alcuna attività di scrittura durante l'esportazione.

Seguire questi passaggi per usare l'utilità della riga di comando SQLPackage per esportare il database AdventureWorks2008R2 nella risorsa di archiviazione locale.

1. Aprire un prompt dei comandi di Windows e cambiare la directory in una cartella in cui sia disponibile la versione **130** di SQLPackage, ad esempio **C:\Programmi (x86) \Microsoft SQL Server\130\DAC\bin**.

2. Eseguire il comando SQLPackage seguente al prompt dei comandi per esportare il database **AdventureWorks2008R2** da **localhost** ad **AdventureWorks2008R2.bacpac**. Sostituire i valori in base alle esigenze specifiche dell'ambiente.

    ```SQLPackage
    sqlpackage.exe /Action:Export /ssn:localhost /sdn:AdventureWorks2008R2 /tf:AdventureWorks2008R2.bacpac
    ```

    ![esportazione con sqlpackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-export.png)

Una volta completata l'esecuzione, il file BCPAC generato viene archiviato nella directory in cui si trova l'eseguibile sqlpackage. In questo esempio C:\Programmi (x86)\Microsoft SQL Server\130\DAC\bin. 

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.

Accedere al [Portale di Azure](https://portal.azure.com/). L'accesso dal computer da cui si esegue l'utilità della riga di comando SQLPackage semplifica la creazione della regola del firewall al passaggio 5.

## <a name="create-a-sql-database-logical-server"></a>Creare un server logico per il database SQL

Un [server logico per il database SQL di Azure](sql-database-features.md) funge da punto di gestione centrale per più database. Seguire questi passaggi per creare un server logico di database SQL per contenere il database OLTP Adventure Works di SQL Server. 

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Digitare **server** nella finestra di ricerca della pagina **Nuovo** e selezionare **Database SQL (server logico)** dall'elenco filtrato.

    ![Selezionare il server logico](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. Nella pagina **Tutto** fare clic su **SQL Server (server logico)** e quindi fare clic su **Crea** nella pagina **SQL Server (server logico)**.

    ![creare un server logico](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. Compilare il server SQL (server logico) con le informazioni seguenti, come illustrato nell'immagine precedente:     

   - Nome del server: specificare un nome di server globalmente univoco
   - Accesso amministratore server: specificare un nome per l'accesso di amministratore al server
   - Password: specificare la password desiderata
   - Gruppo di risorse: selezionare **Crea nuovo** e specificare **myResourceGroup**
   - Posizione: selezionare il percorso del data center

    ![modulo di creazione del server logico completato](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. Fare clic su **Crea** per effettuare il provisioning del server logico. Il provisioning richiede alcuni minuti. 

## <a name="create-a-server-level-firewall-rule"></a>Creare una regola del firewall a livello di server

Il servizio di database SQL crea un [firewall a livello di server](sql-database-firewall-configure.md) che impedisce alle applicazioni e agli strumenti esterni di connettersi al server o ai database sul server, a meno che non venga creata una regola del firewall per aprire il firewall per indirizzi IP specifici. Seguire questi passaggi per creare una regola del firewall a livello di server del database SQL per l'indirizzo IP del computer da cui si esegue l'utilità della riga di comando SQLPackage. In questo modo SQLPackage può connettersi al server logico del database SQL attraverso il firewall del database SQL di Azure. 

1. Fare clic su **Tutte le risorse** nel menu a sinistra e scegliere il nuovo server nella pagina **Tutte le risorse**. Si apre la pagina di panoramica per il server che visualizza il nome completo del server, ad esempio **mynewserver20170403.database.windows.net**, e offre altre opzioni di configurazione.

     ![panoramica del server logico](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. Fare clic su **Firewall** nel menu a sinistra sotto **Impostazioni** nella pagina della panoramica. Si apre la pagina **Impostazioni del firewall** per il server del database SQL. 

3. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer attualmente in uso e quindi fare clic su **Salva**. Viene creata una regola del firewall a livello di server per questo indirizzo IP.

     ![Impostare la regola del firewall del server](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. Fare clic su **OK**.

È ora possibile connettersi a tutti i database di questo server usando SQL Server Management Studio o un altro strumento di propria scelta da questo indirizzo IP con l'account di amministratore del server creato in precedenza.

> [!NOTE]
> Il database SQL comunica attraverso la porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non sarà possibile connettersi al server del database SQL di Azure, a meno che il reparto IT non apra la porta 1433.
>

## <a name="import-bacpac-file-to-azure-sql-database"></a>Importare un file BACPAC in un database SQL di Azure 

Le versioni più recenti dell'utilità della riga di comando SQLPackage supportano la creazione di un database SQL di Azure per uno specifico [livello di servizio e livello di prestazioni](sql-database-service-tiers.md). Per prestazioni ottimali durante l'importazione, selezionare un livello di prestazioni e un livello di servizio elevati e quindi ridurli immediatamente dopo l'importazione se il livello di prestazioni e il livello di servizio sono superiori al necessario.

Seguire questi passaggi per usare l'utilità della riga di comando SQLPackage per importare il database AdventureWorks2008R2 nel database SQL di Azure.

- Eseguire il comando SQLPackage seguente al prompt dei comandi per importare il database **AdventureWorks2008R2** dalla risorsa di archiviazione locale al server logico del database SQL di Azure creato in precedenza con il nome di database **myMigratedDatabase**, il livello di servizio **Premium** e l'obiettivo del servizio **P6**. Sostituire i tre valori in base alle esigenze specifiche dell'ambiente.

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![importazione con sqlpackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Il server logico del database SQL di Azure è in ascolto sulla porta 1433. Se si sta tentando di connettersi a un server logico del database SQL di Azure dall'interno di un firewall aziendale, questa porta deve essere aperta.
>

## <a name="connect-using-sql-server-management-studio-ssms"></a>Eseguire la connessione usando SQL Server Management Studio (SSMS)

Usare SQL Server Management Studio per stabilire una connessione al server del database SQL di Azure e del nuovo database migrato. Se si esegue SQL Server Management Studio in un computer diverso da quello in cui è stato eseguito SQLPackage, creare una regola del firewall per questo computer con i passaggi della procedura precedente.

1. Aprire SQL Server Management Studio.

2. Nella finestra di dialogo **Connetti al server** immettere le informazioni seguenti:
   - **Tipo di server**: specificare il motore del database
   - **Nome server**: immettere il nome completo del server, ad esempio **mynewserver20170403.database.windows.net**
   - **Autenticazione**: specificare l'Autenticazione di SQL Server
   - **Accesso**: immettere l'account dell'amministratore del server
   - **Password**: immettere la password per l'account dell'amministratore del server
 
    ![connessione con SSMS](./media/sql-database-migrate-your-sql-server-database/connect-ssms.png)

3. Fare clic su **Connetti**. Viene visualizzata la finestra Esplora oggetti. 

4. In Esplora oggetti espandere **Database** e quindi espandere **myMigratedDatabase** per visualizzare gli oggetti disponibili nel database di esempio.

## <a name="change-database-properties"></a>Cambiare le proprietà del database

Con SQL Server Management Studio è possibile cambiare il livello di servizio, il livello di prestazioni e il livello di compatibilità.

1. In Esplora oggetti fare clic con il pulsante destro del mouse su **myMigratedDatabase** e scegliere **Nuova query**. Viene visualizzata una finestra di query connessa al database.

2. Eseguire il comando seguente per impostare il livello di servizio **Standard** e il livello di prestazioni **S1**.

    ```
    ALTER DATABASE myMigratedDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

    ![cambiare il livello di servizio](./media/sql-database-migrate-your-sql-server-database/service-tier.png)

3. Eseguire il comando seguente per cambiare il livello di compatibilità del database in **130**.

    ```
    ALTER DATABASE myMigratedDatabase  
    SET COMPATIBILITY_LEVEL = 130;
    ```

   ![cambiare il livello di compatibilità](./media/sql-database-migrate-your-sql-server-database/compat-level.png)

## <a name="next-steps"></a>Passaggi successivi 
In questa esercitazione è stato illustrato come preparare, esportare e importare un database. Si è appreso come:

> [!div class="checklist"]
> * Preparare un database in un'istanza di SQL Server per la migrazione a un database SQL di Azure
> * Esportare il database in un file BACPAC
> * Importare il file BACPAC in un database SQL di Azure

Passare all'esercitazione successiva per informazioni su come proteggere il database.

> [!div class="nextstepaction"]
> [Proteggere il database SQL di Azure](sql-database-security-tutorial.md)



