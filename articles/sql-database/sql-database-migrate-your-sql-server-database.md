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
ms.custom: mvc,load & move data
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/27/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 375d3ea0230e7d3fd0fc02ca7e0b8a7a76c24a27
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017


---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migrare un database SQL Server in un database SQL di Azure

La migrazione di un database SQL Server a un database SQL di Azure è un processo in tre parti che prevede in primo luogo la preparazione, poi l'esportazione e infine l'importazione del database. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare un database in un'istanza di SQL Server per la migrazione a un database SQL di Azure tramite [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA)
> * Esportare il database in un file BACPAC
> * Importare il file BACPAC in un database SQL di Azure

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

- La versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Installando SQL Server Management Studio viene installata anche la versione più recente di SQLPackage, un'utilità della riga di comando utilizzabile per automatizzare varie attività di sviluppo di database. 
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- È avvenuta l'identificazione e si ha avuto accesso a un database per eseguire la migrazione. Questa esercitazione usa il [database OLTP SQL Server 2008R2 AdventureWorks](https://msftdbprodsamples.codeplex.com/releases/view/59211) in un'istanza di SQL Server 2008 R2 o versione più recenti, ma è possibile usare qualsiasi database di propria scelta. Per risolvere i problemi di compatibilità, usare [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).

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

8. Esaminare i risultati. I risultati **SQL Server feature parity** (Parità di funzionalità di SQL Server) mostrati costituiscono i risultati predefiniti da esaminare. In particolare esaminare le informazioni sulle funzionalità non supportate e parzialmente supportate e le informazioni fornite sulle azioni consigliate. 

     ![nuova migrazione di dati valutazione della parità](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. Esaminare i **Problemi di compatibilità** facendo clic su questa opzione in alto a sinistra. In particolare esaminare le informazioni sui blocchi migrazione, le modifiche di comportamento e le funzionalità obsolete per ogni livello di compatibilità. Per il database AdventureWorks2008R2 rivedere le modifiche alla ricerca Full-Text rispetto a SQL Server 2008 e le modifiche apportate a SERVERPROPERTY('LCID') rispetto a SQL Server 2000. Per i dettagli su queste modifiche sono disponibili alcuni collegamenti ad altre informazioni. Molte opzioni di ricerca e impostazioni per la ricerca Full-Text sono state modificate 

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

Dopo aver completato l'esecuzione, il file BCPAC generato viene archiviato nella directory in cui si trova il file eseguibile sqlpackage. In questo esempio C:\Programmi (x86)\Microsoft SQL Server\130\DAC\bin. 

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.

Accedere al [Portale di Azure](https://portal.azure.com/). L'accesso dal computer da cui si esegue l'utilità della riga di comando SQLPackage semplifica la creazione della regola del firewall al passaggio 5.

## <a name="create-a-sql-server-logical-server"></a>Creare un server logico SQL Server

Un [server logico SQL Server](sql-database-features.md) funge da punto di gestione centrale per più database. Seguire questi passaggi per creare un server logico SQL Server che contenga il database OLTP Adventure Works di SQL Server. 

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Digitare **sql server** nella finestra di ricerca della pagina **Nuovo** e selezionare **Database SQL (server logico)** dall'elenco filtrato.

    ![Selezionare il server logico](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. Nella pagina **Tutto** fare clic su **SQL Server (server logico)** e quindi fare clic su **Crea** nella pagina **SQL Server (server logico)**.

    ![creare un server logico](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. Compilare il server SQL (server logico) con le informazioni seguenti, come illustrato nell'immagine precedente:     

   | Impostazione       | Valore consigliato | Descrizione | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Server name** (Nome server) | Inserire qualsiasi nome univoco globale | Per i nomi di server validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). | 
   | **Nome di accesso amministratore server** | Inserire qualsiasi nome valido | Per i nomi di accesso validi, vedere [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificatori di database). |
   | **Password** | Inserire qualsiasi password valida | La password deve almeno 8 caratteri e contenere caratteri inclusi in tre delle categorie seguenti: caratteri maiuscoli, caratteri minuscoli, numeri e caratteri non alfanumerici. |
   | **Sottoscrizione** | Selezionare una sottoscrizione | Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni). |
   | **Gruppo di risorse** | Scegliere un gruppo di risorse esistente o creare un nuovo gruppo, come **myResourceGroup** |  Per i nomi di gruppi di risorse validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). |
   | **Posizione** | Inserire qualsiasi percorso valido per il nuovo server | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |

   ![modulo di creazione del server logico completato](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. Fare clic su **Crea** per effettuare il provisioning del server logico. Il provisioning richiede alcuni minuti. 

> [!IMPORTANT]
> Ricordare il nome del server, il nome dell'account di accesso amministratore server e la password. Più avanti in questa esercitazione saranno necessari questi valori.
>

## <a name="create-a-server-level-firewall-rule"></a>Creare una regola del firewall a livello di server

Il servizio di database SQL crea un [firewall a livello di server](sql-database-firewall-configure.md) che impedisce alle applicazioni e agli strumenti esterni di connettersi al server o ai database sul server, a meno che non venga creata una regola del firewall per aprire il firewall per indirizzi IP specifici. Seguire questi passaggi per creare una regola del firewall a livello di server del database SQL per l'indirizzo IP del computer da cui si esegue l'utilità della riga di comando SQLPackage. In questo modo SQLPackage può connettersi al server logico del database SQL Server attraverso il firewall del database SQL di Azure. 

1. Fare clic su **Tutte le risorse** nel menu a sinistra e scegliere il nuovo server nella pagina **Tutte le risorse**. Verrà visualizzata la pagina Panoramica per il server, con le opzioni per una nuova configurazione.

     ![panoramica del server logico](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. Fare clic su **Firewall** nel menu a sinistra sotto **Impostazioni** nella pagina della panoramica. Si apre la pagina **Impostazioni del firewall** per il server del database SQL. 

3. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer attualmente in uso e quindi fare clic su **Salva**. Viene creata una regola del firewall a livello di server per questo indirizzo IP.

     ![Impostare la regola del firewall del server](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. Fare clic su **OK**.

È ora possibile connettersi a tutti i database di questo server usando SQL Server Management Studio o un altro strumento di propria scelta da questo indirizzo IP con l'account di amministratore del server creato in precedenza.

> [!NOTE]
> Il database SQL comunica attraverso la porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non è possibile connettersi al server del database SQL di Azure, a meno che il reparto IT non apra la porta 1433.
>

## <a name="import-a-bacpac-file-to-azure-sql-database"></a>Importare un file BACPAC in un database SQL di Azure 

Le versioni più recenti dell'utilità della riga di comando SQLPackage supportano la creazione di un database SQL di Azure per uno specifico [livello di servizio e livello di prestazioni](sql-database-service-tiers.md). Per prestazioni ottimali durante l'importazione, selezionare un livello di prestazioni e un livello di servizio elevati e quindi ridurli immediatamente dopo l'importazione se il livello di prestazioni e il livello di servizio sono superiori al necessario.

Seguire questi passaggi per usare l'utilità della riga di comando SQLPackage per importare il database AdventureWorks2008R2 nel database SQL di Azure. Benché sia possibile usare SQL Server Management Studio per questa attività, SQLPackage è il metodo preferito per la maggior parte degli ambienti di produzione poiché offre massima flessibilità e prestazioni ottimali. Vedere [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al Database di SQL di Azure tramite file BACPAC).

- Eseguire il comando SQLPackage seguente al prompt dei comandi per importare il database **AdventureWorks2008R2** dalla risorsa di archiviazione locale al server logico SQL Server creato in precedenza in un nuovo database, un livello di servizio **Premium** e l'obiettivo del servizio **P6**. Sostituire i valori tra parentesi quadre con valori appropriati per il server logico SQL Server e specificare un nome per il nuovo database (sostituire anche le parentesi graffe). È anche possibile scegliere di modificare i valori per l'edizione del database e il servizio objectgive nel modo più adatto all'ambiente in uso. Ai fini di questa esercitazione, il database su cui viene eseguita la migrazione viene denominato **myMigratedDatabase**.

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=<your_server_name>.database.windows.net;Initial Catalog=<your_new_database_name>;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![importazione con sqlpackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Un server logico SQL Server è in ascolto sulla porta 1433. Se si sta tentando di connettersi a un server logico SQL Server dall'interno di un firewall aziendale, questa porta deve essere aperta ai fini del buon esito della connessione.
>

## <a name="connect-using-sql-server-management-studio-ssms"></a>Eseguire la connessione usando SQL Server Management Studio (SSMS)

Usare SQL Server Management Studio per stabilire una connessione al server del database SQL di Azure e del nuovo database migrato, denominato **myMigratedDatabase** in questa esercitazione. Se si esegue SQL Server Management Studio in un computer diverso da quello in cui è stato eseguito SQLPackage, creare una regola del firewall per questo computer con i passaggi della procedura precedente.

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

Con SQL Server Management Studio è possibile cambiare il livello di servizio, il livello di prestazioni e il livello di compatibilità. Durante la fase di importazione, è consigliabile importare in un database con livello di prestazioni superiore per ottenere prestazioni ottimali, ma ridurlo dopo il completamento dell'importazione per risparmiare denaro fino a quando non si è pronti a usare attivamente il database importato. La modifica del livello di compatibilità può comportare prestazioni migliori e l'accesso alle funzionalità più recenti del servizio Database SQL di Azure. Quando si esegue la migrazione di un database meno recente, il livello di compatibilità del database viene mantenuto al livello più basso supportato e compatibile con il database importato. Per altre informazioni, vedere [Miglioramento delle prestazioni delle query con il livello di compatibilità 130 nel database SQL di Azure](sql-database-compatibility-level-query-performance-130.md).

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



