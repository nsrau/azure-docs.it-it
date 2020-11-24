---
title: 'Esercitazione: caricare dati relativi ai taxi di New York'
description: L'esercitazione USA portale di Azure e SQL Server Management Studio per caricare i dati dei taxi di New York da un BLOB di Azure per la sinapsi SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bd5c56ef74fbe0c60a9d395a7b8a0fbc496e773c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95534841"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Esercitazione: caricare il set di dati del taxi di New York

Questa esercitazione usa l' [istruzione Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) per caricare il set di dati del taxi di New York da un account di archiviazione BLOB di Azure. Questa esercitazione usa il [portale di Azure](https://portal.azure.com) e [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) per:

> [!div class="checklist"]
>
> * Creare un utente designato per il caricamento dei dati
> * Creare le tabelle per il set di dati di esempio 
> * Usare l'istruzione T-SQL COPY per caricare i dati nel data warehouse
> * Visualizzare lo stato di avanzamento dei dati durante il caricamento

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare questa esercitazione, scaricare e installare la versione più recente di [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).  

In questa esercitazione si presuppone che sia già stato creato un pool dedicato a SQL nell' [esercitazione](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/create-data-warehouse-portal#connect-to-the-server-as-server-admin)seguente.

## <a name="create-a-user-for-loading-data"></a>Creare un utente per il caricamento dei dati

L'account amministratore del server ha la funzione di eseguire operazioni di gestione e non è appropriato per l'esecuzione di query sui dati degli utenti. Il caricamento di dati è un'operazione a elevato utilizzo di memoria. I valori massimi di memoria sono definiti in base alle [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) e alla [classe di risorse](resource-classes-for-workload-management.md) configurata.

È consigliabile creare un account di accesso e un utente dedicato per il caricamento dei dati. Quindi aggiungere l'utente con il compito di caricare i dati a una [classe di risorse](resource-classes-for-workload-management.md) che consente un'allocazione di memoria massima appropriata.

Connettersi come amministratore del server in modo che sia possibile creare account di accesso e utenti. Usare questa procedura per creare un account di accesso e un utente denominato **LoaderRC20**. Quindi assegnare l'utente alla classe di risorse **staticrc20**.

1. In SSMS, fare clic con il pulsante destro del mouse su **Master** per visualizzare un menu a discesa e scegliere **nuova query**. Viene visualizzata una nuova finestra di query.

    ![Nuova query nel master](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Nella finestra della query immettere i comandi T-SQL per creare un account di accesso e un utente denominato LoaderRC20, sostituendo la propria password al valore "a123STRONGpassword!".

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Selezionare **Esegui**.

4. Fare clic con il pulsante destro del mouse su **mySampleDataWarehouse** e scegliere **Nuova query**. Viene visualizzata una nuova finestra della query.  

    ![Nuova query nel data warehouse di esempio](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Immettere i comandi T-SQL seguenti per creare un utente del database denominato LoaderRC20 per l'account di accesso LoaderRC20. La seconda riga concede al nuovo utente autorizzazioni di CONTROLLO sul nuovo data warehouse.  Queste autorizzazioni sono simili a rendere l'utente proprietario del database. La terza riga aggiunge il nuovo utente come membro della [classe di risorse](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Selezionare **Esegui**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Connettersi al server come utente addetto al caricamento

Il primo passo per caricare dati è eseguire l'accesso come LoaderRC20.  

1. In Esplora oggetti selezionare il menu a discesa **Connetti** e selezionare **motore di database**. Viene visualizzata la finestra di dialogo **Connetti al server** .

    ![Connettersi con il nuovo account](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Immettere il nome completo del server e **LoaderRC20** come account di accesso.  Immettere la password per LoaderRC20.

3. Selezionare **Connetti**.

4. Quando la connessione è pronta, si vedranno due connessioni server in Esplora oggetti. Una connessione ServerAdmin e una connessione MedRCLogin.

    ![La connessione riesce](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Creare tabelle per i dati di esempio

Ora è possibile iniziare il processo di caricamento dei dati nel nuovo data warehouse. Questa parte dell'esercitazione illustra come usare l'istruzione COPY per caricare il set di dati CAB di New York City da un BLOB del servizio di archiviazione di Azure. Per riferimento futuro, per informazioni su come ottenere i dati nell'archivio BLOB di Azure o per caricarli direttamente dall'origine, vedere la [Panoramica del caricamento](design-elt-data-loading.md).

Eseguire gli script SQL seguenti e specificare le informazioni sui dati che si desidera caricare. Queste informazioni includono la posizione dei dati, il formato del contenuto dei dati e la definizione della tabella per i dati.

1. Nella sezione precedente è stato eseguito l'accesso al data warehouse come LoaderRC20. In SSMS fare clic con il pulsante destro del mouse sulla connessione LoaderRC20 e selezionare **Nuova query**.  Verrà visualizzata una nuova finestra di query,

    ![Finestra della nuova query di caricamento](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Confrontare la finestra della query all'immagine precedente.  Verificare che la nuova finestra della query sia in esecuzione come LoaderRC20 ed esegua query sul database MySampleDataWarehouse. Usare questa finestra della query per eseguire tutte le operazioni di caricamento.

7. Eseguire le istruzioni T-SQL seguenti per creare le tabelle:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>Caricare i dati nel data warehouse

In questa sezione viene utilizzata l' [istruzione Copy per caricare](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) i dati di esempio da BLOB del servizio di archiviazione di Azure.  

> [!NOTE]
> Questa esercitazione carica i dati direttamente nella tabella finale. In genere si esegue il caricamento in una tabella di staging per i carichi di lavoro di produzione. Mentre i dati si trovano nella tabella di staging è possibile eseguire le trasformazioni eventualmente necessarie. 

1. Eseguire le istruzioni seguenti per caricare i dati:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. Visualizzare i dati man mano che vengono caricati. Si stanno caricando diversi GB di dati e compressi in indici columnstore cluster a elevato livello di prestazioni. Eseguire la query riportata di seguito, che usa le viste a gestione dinamica (DMV) per visualizzare lo stato del caricamento.

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Visualizzare tutte le query di sistema.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. I dati vengono caricati nel data warehouse.

    ![Visualizzare le tabelle caricate](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Le risorse di calcolo e i dati caricati nel data warehouse prevedono un addebito. Questi costi vengono addebitati separatamente.

* Se si vogliono mantenere i dati nelle risorse di archiviazione, è possibile sospendere il calcolo quando il data warehouse non è in uso. Sospendendo il calcolo verrà addebitata solo l'archiviazione dati: sarà possibile riprendere il calcolo quando sarà necessario elaborare i dati.
* Per evitare di ricevere addebiti in futuro, è possibile eliminare il data warehouse.

Seguire questa procedura per pulire le risorse nel modo desiderato.

1. Accedere al [portale di Azure](https://portal.azure.com), selezionare la data warehouse.

    ![Pulire le risorse](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Per sospendere il calcolo, selezionare il pulsante **Pausa**. Quando si sospende il data warehouse, viene visualizzato il pulsante **Avvia**.  Per riprendere il calcolo, selezionare **Avvia**.

3. Per rimuovere il data warehouse in modo che non venga addebitato il calcolo o l'archiviazione, selezionare **Elimina**.

4. Per rimuovere il server creato, selezionare **MyNewServer-20180430.database.Windows.NET** nell'immagine precedente e quindi selezionare **Elimina**.  Prestare attenzione con questa operazione perché eliminando il server saranno eliminati tutti i database assegnati al server.

5. Per rimuovere il gruppo di risorse, selezionare **myResourceGroup** e quindi **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come creare un data warehouse e creare un utente per il caricamento dei dati. È stata usata l' [istruzione Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) semplice per caricare i dati nel data warehouse.

Sono state eseguite queste operazioni:
> [!div class="checklist"]
>
> * È stato creato un data warehouse nel portale di Azure
> * Impostare una regola del firewall a livello di server nel portale di Azure
> * Ci si è connessi al data warehouse con SSMS
> * È stato creato un utente designato per il caricamento dei dati
> * Creazione delle tabelle per i dati di esempio
> * È stata usata l'istruzione COPY T-SQL per caricare i dati nel data warehouse
> * È stato visualizzato lo stato di avanzamento dei dati durante il caricamento

Passare alla panoramica dello sviluppo per informazioni su come eseguire la migrazione di un database esistente ad Azure sinapsi Analytics:

> [!div class="nextstepaction"]
> [Decisioni di progettazione per la migrazione di un database esistente ad Azure sinapsi Analytics](sql-data-warehouse-overview-develop.md)

Per ulteriori informazioni sul caricamento di esempi e riferimenti, vedere la documentazione seguente:

- [Documentazione di riferimento dell'istruzione COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [COPIA esempi per ogni metodo di autenticazione](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [COPIA avvio rapido per una singola tabella](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
