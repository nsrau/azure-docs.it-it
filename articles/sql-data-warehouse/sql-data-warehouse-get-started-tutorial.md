---
title: Azure SQL Data Warehouse - Esercitazione introduttiva | Documentazione Microsoft
description: Esercitazione introduttiva con Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: fe9de0ffad3fe5d4acbf3caf2f08101f6a13daaf
ms.openlocfilehash: 12f500c01671799612b0d1988e0d07bbfda600da


---
# <a name="get-started-with-sql-data-warehouse"></a>Introduzione a SQL Data Warehouse

Esercitazione introduttiva con Azure SQL Data Warehouse. Questa esercitazione illustra i concetti di base del provisioning e del caricamento di dati in un'istanza di SQL Data Warehouse, oltre ad alcuni concetti di base su ridimensionamento, sospensione e ottimizzazione. 

**Tempo previsto per il completamento:** 75 minuti

## <a name="prerequisites"></a>Prerequisiti


### <a name="sign-up-for-microsoft-azure"></a>Iscrizione a Microsoft Azure
Se non si ha già un account Microsoft Azure, è necessario crearne uno per usare questo servizio. Se si ha già un account, è possibile ignorare questo passaggio. 

1. Passare alle pagine dell'account [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. Creare un account Azure gratuito o acquistare un account.
3. Seguire le istruzioni

### <a name="install-appropriate-sql-client-driver-and-tools"></a>Installare il driver del client SQL e gli strumenti appropriati

La maggior parte degli strumenti del client SQL può connettersi ad Azure SQL Data Warehouse usando JDBC, ODBC o ADO.net. A causa della complessità del prodotto e del numero elevato delle funzionalità di T-SQL supportate da SQL Data Warehouse, è possibile che non tutte le applicazioni client siano completamente compatibili con SQL Data Warehouse.

Se si esegue un sistema operativo Windows, è consigliabile usare [Visual Studio] o [SQL Server Management Studio].


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>Creare un Azure SQL Data Warehouse

> [!NOTE]
> La creazione di un'istanza di SQL Data Warehouse può avere come risultato un nuovo servizio fatturabile.  Per altre informazioni, vedere [SQL Data Warehouse Prezzi](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>


### <a name="create-a-sql-data-warehouse"></a>Creare un SQL Data Warehouse
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** > **Database** > **SQL Data Warehouse**.

    ![NuovoPannello](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelezioneDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Compilare i dettagli della distribuzione

    **Nome database**: scegliere un nome qualsiasi. Se sono disponibili più istanze di SQL Data Warehouse, è consigliabile che i nomi includano dettagli quali l'area, l'ambiente e così via, ad esempio *mydw-westus-1-test*.

    **Sottoscrizione**: sottoscrizione di Azure.

    **Gruppo di risorse**: creare un nuovo gruppo o usare un gruppo esistente, se si prevede di usare Azure SQL Data Warehouse con altri servizi.
    > [!NOTE]
    > I servizi con lo stesso gruppo di risorse devono avere lo stesso ciclo di vita. I gruppi di risorse sono utili per l'amministrazione delle risorse, ad esempio per la definizione dell'ambito del controllo di accesso e per la distribuzione basata su modelli. Per altre informazioni sui gruppi di risorse di Azure e sulle procedure consigliate, vedere [qui](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)
    >

    **Origine**: database vuoto.

    **Server**: selezionare il server creato in [Prerequisiti].

    **Regole di confronto**: lasciare le regole di confronto predefinite SQL_Latin1_General_CP1_CI_AS.

    **Selezione delle prestazioni**: è consigliabile mantenere il valore standard di 400 DWU.

4. Scegliere **Aggiungi al dashboard**
    ![](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png).

5. Attendere il completamento della distribuzione di Azure SQL Data Warehouse. Il processo richiede normalmente qualche minuto. Il portale segnalerà il completamento della distribuzione dell'istanza. 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>Connettersi ad Azure SQL Data Warehouse tramite SQL Server (server logico)

Questa esercitazione usa SQL Server Management Studio per la connessione a SQL Data Warehouse. È possibile usare altri strumenti per la connessione a SQL Data Warehouse tramite i connettori supportati: ADO.NET, JDBC, ODBC e PHP. Occorre notare che le funzionalità potrebbero essere limitate per gli strumenti non supportati da Microsoft.


### <a name="get-connection-information"></a>Ottenere informazioni di connessione

Per connettersi a SQL Data Warehouse, è necessario usare l'istanza di SQL Server (server logico) creata in [Prerequisiti].

1. Selezionare SQL Data Warehouse dal dashboard o cercarlo nelle risorse.

    ![Dashboard di SQL Data Warehouse](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Trovare il nome completo del server logico.

    ![Selezionare il nome del server](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Aprire SSMS e usare Esplora oggetti per connettersi a questo server usando le credenziali create in [Prerequisiti].

    ![Connettersi con SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Se non si verificano errori, dovrebbe essere stabilita la connessione all'istanza di SQL Server (server logico). È possibile usare le credenziali del server per l'autenticazione a qualsiasi database nel server come proprietario del database. È tuttavia consigliabile creare account di accesso e utenti separati per ogni database. La creazione degli utenti viene illustrata in [Creare un utente per SQL Data Warehouse](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse). 

## <a name="create-a-user-for-sql-data-warehouse"></a>Creare un utente per SQL Data Warehouse

### <a name="why-create-a-separate-user"></a>Vantaggi della creazione di un utente separato

La connessione all'istanza di SQL Server (server logico), con le credenziali del server specificate nel passaggio precedente, viene usata per creare un nuovo utente per SQL Data Warehouse. La creazione di un utente/account di accesso separato per SQL Data Warehouse offre due vantaggi principali.

1.  È opportuno che gli utenti dell'organizzazione usino un account diverso per l'autenticazione. In questo modo è possibile limitare le autorizzazioni concesse alle applicazioni e ridurre i rischi di attività dannose.

2. Per impostazione predefinita, l'account di accesso di amministratore del server attualmente usato per la connessione usa una classe di risorse più ridotta. Attraverso le classi di risorse è possibile controllare l'allocazione di memoria e cicli di CPU assegnati a una query. Agli utenti nel ruolo **smallrc** viene assegnata una quantità di memoria più piccola e ciò permette di sfruttare una concorrenza maggiore. Al contrario, agli utenti con il ruolo **xlargerc** vengono assegnate grandi quantità di memoria e quindi l'esecuzione contemporanea è possibile per un minor numero di query. Per caricare i dati in modo da ottimizzare la compressione, è consigliabile che l'utente che carica i dati appartenga a una classe di risorse più grande. Per altre informazioni sulle classi di risorse, vedere [qui](./sql-data-warehouse-develop-concurrency.md#resource-classes):

### <a name="creating-a-user-of-a-larger-resource-class"></a>Creazione di un utente di una classe di risorse più grande

1. Eseguire una query nel database **master** del server

    ![Nuova query nel master](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Nuova query nel master&1;](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Creare un account di accesso e un utente del server

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. Eseguendo una query nel database di SQL Data Warehouse, creare un nuovo utente del database in base all'account di accesso del server 
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. Concedere all'utente il controllo del database
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Se il nome del database include trattini, assicurarsi di racchiuderlo tra parentesi. 
    >

5. Aggiungere l'utente del database al ruolo **xlargerc** della classe di risorse
    ```sql
    EXEC sp_addrolemember 'xlargerc', 'LoadingUser';
    ```

6. Accedere al database con le nuove credenziali

    ![Accedere con il nuovo account](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>Caricamento dei dati

### <a name="defining-external-data"></a>Definizione dei dati esterni
1. Creare una chiave master e definire un'origine dati esterna

    ```sql
    CREATE MASTER KEY;

    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. Definire i formati di file esterni

    Il comando ```CREATE EXTERNAL FILE FORMAT``` viene usato per specificare il formato dei dati esterni da cui si esegue il caricamento. Per i dati pubblici relativi ai taxi di New York sono stati usati due formati per l'archiviazione dei dati nell'archivio BLOB di Azure.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  Creare uno schema per il formato di file esterni

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. Creare le tabelle esterne. Queste tabelle fanno riferimento a dati archiviati in HDFS o nell'archivio BLOB di Azure. 

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
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
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    CREATE EXTERNAL TABLE [ext].[Geography]
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
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Time]
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
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Trip]
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
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>Create Table as Select (CTAS)

5. Caricare i dati dalle tabelle esterne all'istanza di SQL Data Warehouse. 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > Vengono caricati alcuni GB di dati, che vengono compressi in indici Columnstore del cluster a prestazioni elevate. Eseguire quindi la query DMV e attendere il completamento dell'operazione da parte di Azure SQL Data Warehouse.
    >

6. Creare una nuova query e attendere la visualizzazione dei dati nella vista a gestione dinamica (DMV, Dynamic Management View).

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
    WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
    r.command,
    s.request_id,
    r.status
    ORDER BY
    nbr_files desc, gb_processed desc;
    ```

7. Visualizzare tutte le query di sistema.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. I dati vengono caricati in Azure SQL Data Warehouse.

    ![Visualizzare i dati caricati](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>Eseguire query sui dati 

### <a name="scan-query-with-scaling"></a>Analizzare le query con il ridimensionamento

Viene illustrato il modo in cui il ridimensionamento influisce sulla velocità delle query.

Prima di iniziare, ridurre le prestazioni a 100 DWU, in modo da ottenere informazioni sulle prestazioni di un singolo nodo di calcolo.

1. Passare al portale e selezionare l'istanza di SQL Data Warehouse.

2. Selezionare Ridimensiona nel pannello SQL Data Warehouse. 

    ![Ridimensionare SQL Data Warehouse dal portale](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Ridurre le prestazioni a 100 DWU e scegliere Salva.

    ![Ridimensionare e salvare](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Attendere il completamento dell'operazione di ridimensionamento.

    > [!NOTE]
    > Si noti che le operazioni di ridimensionamento **terminano** le query attualmente in esecuzione e impediscono l'esecuzione di nuove query.
    >
    
5. Eseguire un'analisi sui dati relativi alla corsa, selezionando il primo milione di voci per tutte le colonne. Se si preferisce velocizzare le operazioni, è possibile selezionare un numero minore di righe.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

Annotare il tempo necessario per eseguire l'operazione.

6. Aumentare le prestazioni dell'istanza a 400 DWU. Occorre ricordare che ogni incremento di 100 DWU aggiunge un altro nodo di calcolo ad Azure SQL Data Warehouse.

7. Eseguire di nuovo la query. La differenza dovrebbe essere significativa. 

> [!NOTE]
> Azure SQL Data Warehouse è una piattaforma MPP (Massively Parallel Processing). Le query e le operazioni che possono parallelizzare il lavoro in diversi nodi possono sfruttare al massimo le capacità di Azure SQL Data Warehouse.
>

### <a name="join-query-with-statistics"></a>Query Join con statistiche

1. Eseguire una query che consente il join della tabella Date alla tabella Trip

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    Come previsto, l'esecuzione della query richiede molto più tempo quando si selezionano in modo casuale i dati tra i nodi, in particolare in uno scenario di join come questo.

2. Di seguito viene illustrato come varia la query quando si creano statistiche nella colonna su cui si esegue il join mediante questo comando:

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL Data Warehouse non gestisce automaticamente le statistiche. Le statistiche sono importanti per le prestazioni delle query ed è consigliabile creare e aggiornare le statistiche.
    > 
    > **Il massimo vantaggio è offerto dalle statistiche su colonne usate nei join, colonne usate nella clausola WHERE e colonne presenti in GROUP BY.**
    >

3. Eseguire di nuovo la query da Prerequisiti ed esaminare eventuali differenze tra le prestazioni. Anche se le differenze tra le prestazioni delle query non saranno significative come nel caso di aumento delle prestazioni, si dovrebbe notare una maggiore velocità. 

## <a name="next-steps"></a>Passaggi successivi

È ora possibile eseguire query ed esplorare. Vedere le procedure consigliate o i suggerimenti.

Al termine dell'esplorazione, ricordare di sospendere l'istanza. In produzione è possibile ottenere risparmi notevoli sospendendo e ridimensionando l'istanza in base alle esigenze aziendali.

![Sospendi](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>Vedere anche

[Gestione della concorrenza e del carico di lavoro]

[Procedure consigliate per Azure SQL Data Warehouse]

[Monitoraggio delle query]

Post di blog [Top 10 Best Practices for Building a Large Scale Relational Data Warehouse] (Prime 10 procedure consigliate per la creazione di un data warehouse relazionale di dimensioni elevate)

Post di blog [Migrating Data to Azure SQL Data Warehouse] (Migrazione di dati in Azure SQL Data Warehouse)


[Gestione della concorrenza e del carico di lavoro]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Procedure consigliate per Azure SQL Data Warehouse]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Monitoraggio delle query]: sql-data-warehouse-manage-monitor.md
Post di blog [Top 10 Best Practices for Building a Large Scale Relational Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/ (Prime 10 procedure consigliate per la creazione di un data warehouse relazionale di dimensioni elevate)
Post di blog [Migrating Data to Azure SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/ (Migrazione di dati in Azure SQL Data Warehouse)



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Prerequisiti]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO3-->


