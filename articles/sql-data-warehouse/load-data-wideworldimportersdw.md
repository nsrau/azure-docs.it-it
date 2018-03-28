---
title: 'Esercitazione: Caricare dati in Azure SQL Data Warehouse | Microsoft Docs'
description: Questa esercitazione usa il portale di Azure e SQL Server Management Studio per caricare il data warehouse WideWorldImportersDW dall'archivio BLOB di Azure ad Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: ''
author: ckarst
manager: jhubbard
editor: ''
tags: ''
ms.assetid: ''
ms.service: sql-data-warehouse
ms.custom: mvc,develop data warehouses
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/06/2018
ms.author: cakarst
ms.reviewer: barbkess
ms.openlocfilehash: 7e7d9a299e141ef8fd564e7f97077471264420ea
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-load-data-to-azure-sql-data-warehouse"></a>Esercitazione: Caricare dati in Azure SQL Data Warehouse

Questa esercitazione carica il data warehouse WideWorldImportersDW dall’archivio BLOB di Azure ad Azure SQL Data Warehouse. Questa esercitazione usa il [portale di Azure](https://portal.azure.com) e [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) per: 

> [!div class="checklist"]
> * Creare data warehouse nel portale di Azure
> * Impostare una regola del firewall a livello di server nel portale di Azure
> * Connettersi al data warehouse con SSMS
> * Creare un utente designato per il caricamento dei dati
> * Creare tabelle esterne che usano BLOB di Azure come origine dati
> * Usare l'istruzione T-SQL CTAS per caricare i dati nel data warehouse
> * Visualizzare lo stato di avanzamento dei dati durante il caricamento
> * Generare dati relativi a un anno nelle tabelle delle dimensioni date e dei dati di vendita
> * Creare statistiche sui nuovi dati caricati

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare questa esercitazione, scaricare e installare la versione più recente di [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS).


## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-data-warehouse"></a>Creare un SQL Data Warehouse vuoto

Un SQL Data Warehouse viene creato con un set definito di [risorse di calcolo](performance-tiers.md). Il database viene creato in un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) e in un [server logico di Azure SQL](../sql-database/sql-database-features.md). 

Per creare un SQL Data Warehouse vuoto, eseguire la procedura seguente. 

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Database** nella pagina **Nuovo** e **SQL Data Warehouse** nell'area **In primo piano** della pagina **Nuovo**.

    ![creare un data warehouse](media/load-data-wideworldimportersdw/create-empty-data-warehouse.png)

3. Compilare il modulo di SQL Data Warehouse con le informazioni seguenti:   

   | Impostazione | Valore consigliato | DESCRIZIONE | 
   | ------- | --------------- | ----------- | 
   | **Database name** (Nome database) | SampleDW | Per i nomi di database validi, vedere [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Identificatori di database). | 
   | **Sottoscrizione** | Sottoscrizione in uso  | Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni). |
   | **Gruppo di risorse** | SampleRG | Per i nomi di gruppi di risorse validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). |
   | **Select source** (Seleziona origine) | Database vuoto | Specificare che venga creato un database vuoto. Si noti che un data warehouse è un tipo di database.|

    ![creare un data warehouse](media/load-data-wideworldimportersdw/create-data-warehouse.png)

4. Fare clic su **Server** per creare e configurare un nuovo server per il nuovo database. Compilare il **modulo del nuovo server** con le informazioni seguenti: 

    | Impostazione | Valore consigliato | DESCRIZIONE | 
    | ------- | --------------- | ----------- |
    | **Server name** (Nome server) | Qualsiasi nome globalmente univoco | Per i nomi di server validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). | 
    | **Accesso amministratore server** | Qualsiasi nome valido | Per i nomi di accesso validi, vedere [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificatori di database).|
    | **Password** | Qualsiasi password valida | La password deve contenere almeno otto caratteri delle tre categorie seguenti: maiuscole, minuscole, numeri e caratteri non alfanumerici. |
    | **Posizione** | Qualsiasi località valida | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |

    ![creare un server di database](media/load-data-wideworldimportersdw/create-database-server.png)

5. Fare clic su **Seleziona**.

6. Fare clic su **Livello di prestazioni** per specificare se il data warehouse è ottimizzato per l'elasticità o il calcolo e il numero di unità del data warehouse. 

7. Per questa esercitazione selezionare il livello di servizio **Ottimizzato per l'elasticità**. Il dispositivo di scorrimento, per impostazione predefinita, è impostato su **DW400**.  Provare a spostarlo verso l'alto o il basso per vedere come funziona. 

    ![configurare le prestazioni](media/load-data-wideworldimportersdw/configure-performance.png)

8. Fare clic su **Apply**.
9. Nella pagina SQL Data Warehouse selezionare le **regole di confronto** per il database vuoto. Per questa esercitazione usare il valore predefinito. Per altre informazioni sulle regole di confronto, vedere [Collations](/sql/t-sql/statements/collations.md) (Regole di confronto)

11. Dopo aver completato il modulo del database SQL, fare clic su **Crea** per effettuare il provisioning del database. Il provisioning richiede alcuni minuti. 

    ![fare clic su Crea](media/load-data-wideworldimportersdw/click-create.png)

12. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.
    
     ![notifica](media/load-data-wideworldimportersdw/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Creare una regola del firewall a livello di server

Il servizio SQL Data Warehouse crea un firewall a livello di server che impedisce alle applicazioni e agli strumenti esterni di connettersi al server o ai database sul server. Per abilitare la connettività, è possibile aggiungere regole del firewall per aprire il firewall a indirizzi IP specifici.  Seguire questa procedura per creare una [regola del firewall a livello di server](../sql-database/sql-database-firewall-configure.md) per l'indirizzo IP del client. 

> [!NOTE]
> SQL Data Warehouse comunica attraverso la porta 1433. Se si sta provando a connettersi da una rete aziendale, il traffico in uscita sulla porta 1433 potrebbe non essere consentito dal firewall della rete. In questo caso, non è possibile connettersi al server del database SQL di Azure, a meno che il reparto IT non apra la porta 1433.
>

1. Al termine della distribuzione, scegliere **Database SQL** dal menu a sinistra e fare clic su **SampleDW** nella pagina **Database SQL**. Verrà visualizzata la pagina di panoramica del database, che mostra il nome completo del server, ad esempio **sample-svr.database.windows.net**, e offre altre opzioni per la configurazione. 

2. Copiare il nome completo del server per connettersi al server e ai relativi database nelle guide introduttive successive. Per aprire le impostazioni del server, fare clic sul nome del server.

    ![trovare il nome del server](media/load-data-wideworldimportersdw/find-server-name.png) 

3. Per aprire le impostazioni del server, fare clic sul nome del server.

    ![impostazioni del server](media/load-data-wideworldimportersdw/server-settings.png) 

5. Fare clic su **Mostra impostazioni firewall**. Si apre la pagina **Impostazioni del firewall** per il server del database SQL. 

    ![Regola del firewall del server](media/load-data-wideworldimportersdw/server-firewall-rule.png) 

4.  Per aggiungere l'indirizzo IP corrente a una nuova regola del firewall, fare clic su **Aggiungi IP client** sulla barra degli strumenti. Una regola del firewall può aprire la porta 1433 per un indirizzo IP singolo o un intervallo di indirizzi IP.

5. Fare clic su **Save**. Viene creata una regola del firewall a livello di server per l'indirizzo IP corrente, che apre la porta 1433 nel server logico.

6. Fare clic su **OK** e quindi chiudere la pagina **Impostazioni del firewall**.

È ora possibile connettersi al server SQL e ai relativi data warehouse usando questo indirizzo IP. La connessione funziona da SQL Server Management Studio o un altro strumento di propria scelta. Quando ci si connette, usare l'account serveradmin creato in precedenza.  

> [!IMPORTANT]
> Per impostazione predefinita, l'accesso attraverso il firewall del database SQL è abilitato per tutti i servizi di Azure. Fare clic su **DISATTIVATO** in questa pagina e quindi fare clic su **Salva** per disabilitare il firewall per tutti i servizi di Azure.

## <a name="get-the-fully-qualified-server-name"></a>Ottenere il nome completo del server

Ottenere il nome completo del server SQL nel portale di Azure. In seguitò si userà il nome completo per la connessione al server.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nel riquadro **Informazioni di base** della pagina del portale di Azure per il database individuare e quindi copiare il **Nome server**. In questo esempio il nome completo è mynewserver-20171113.database.windows.net. 

    ![informazioni di connessione](media/load-data-wideworldimportersdw/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Connettersi al server come amministratore del server

In questa sezione si usa [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) per stabilire una connessione al server SQL di Azure.

1. Aprire SQL Server Management Studio.

2. Nella finestra di dialogo **Connetti al server** immettere le informazioni seguenti:

    | Impostazione      | Valore consigliato | DESCRIZIONE | 
    | ------------ | --------------- | ----------- | 
    | Tipo di server | Motore di database | Questo valore è obbligatorio |
    | Nome server | Nome completo del server | Ad esempio, **sample-svr.database.windows.net** è un nome di server completo. |
    | Authentication | Autenticazione di SQL Server | L'autenticazione SQL è il solo tipo di autenticazione configurato in questa esercitazione. |
    | Login | Account amministratore del server | Si tratta dell'account specificato quando è stato creato il server. |
    | Password | Password per l'account amministratore del server | Si tratta della password specificata quando è stato creato il server. |

    ![connetti al server](media/load-data-wideworldimportersdw/connect-to-server.png)

4. Fare clic su **Connetti**. La finestra Esplora oggetti viene visualizzata in SSMS. 

5. In Esplora oggetti espandere **Database**. Espandere quindi **Database di sistema** e **master** per visualizzare gli oggetti nel database master.  Espandere **mySampleDatabase** per visualizzare gli oggetti nel nuovo database.

    ![oggetti di database](media/load-data-wideworldimportersdw/connected.png) 

## <a name="create-a-user-for-loading-data"></a>Creare un utente per il caricamento dei dati

L'account amministratore del server ha la funzione di eseguire operazioni di gestione e non è appropriato per l'esecuzione di query sui dati degli utenti. Il caricamento di dati è un'operazione a elevato utilizzo di memoria. I [valori massimi di memoria](performance-tiers.md#memory-maximums) sono definiti in base al [livello di prestazioni](performance-tiers.md) e alla [classe di risorse](resource-classes-for-workload-management.md). 

È consigliabile creare un account di accesso e un utente dedicato per il caricamento dei dati. Quindi aggiungere l'utente con il compito di caricare i dati a una [classe di risorse](resource-classes-for-workload-management.md) che consente un'allocazione di memoria massima appropriata.

Poiché l'accesso è stato eseguito come amministratore del server, è possibile creare account di accesso e utenti. Usare questa procedura per creare un account di accesso e un utente denominato **LoaderRC60**. Quindi assegnare l'utente alla classe di risorse **staticrc60**. 

1.  In SSMS fare clic con il pulsante destro del mouse su **master** per visualizzare un menu a discesa e scegliere **Nuova query**. Verrà visualizzata una nuova finestra di query.

    ![Nuova query nel master](media/load-data-wideworldimportersdw/create-loader-login.png)

2. Nella finestra della query immettere questi comandi T-SQL per creare un account di accesso e un utente denominato LoaderRC60, sostituendo la propria password al valore "a123STRONGpassword!". 

    ```sql
    CREATE LOGIN LoaderRC60 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC60 FOR LOGIN LoaderRC60;
    ```

3. Fare clic su **Execute**.

4. Fare clic con il pulsante destro del mouse su **SampleDW** e scegliere **Nuova query**. Viene visualizzata una nuova finestra della query.  

    ![Nuova query nel data warehouse di esempio](media/load-data-wideworldimportersdw/create-loading-user.png)
 
5. Immettere i comandi T-SQL seguenti per creare un utente del database denominato LoaderRC60 per l'account di accesso LoaderRC60. La seconda riga concede al nuovo utente autorizzazioni di CONTROLLO sul nuovo data warehouse.  Queste autorizzazioni sono simili a rendere l'utente proprietario del database. La terza riga aggiunge il nuovo utente come membro della [classe di risorse](resource-classes-for-workload-management.md) staticrc60.

    ```sql
    CREATE USER LoaderRC60 FOR LOGIN LoaderRC60;
    GRANT CONTROL ON DATABASE::[SampleDW] to LoaderRC60;
    EXEC sp_addrolemember 'staticrc60', 'LoaderRC60';
    ```

6. Fare clic su **Execute**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Connettersi al server come utente addetto al caricamento

Il primo passo per caricare dati è eseguire l'accesso come LoaderRC60.  

1. In Esplora oggetti fare clic su **Connetti** nel menu a discesa e selezionare **Motore di database**. Viene visualizzata la finestra di dialogo **Connetti al server** .

    ![Connettersi con il nuovo account](media/load-data-wideworldimportersdw/connect-as-loading-user.png)

2. Immettere il nome completo del server e **LoaderRC60** come account di accesso.  Immettere la password per LoaderRC60.

3. Fare clic su **Connetti**.

4. Quando la connessione è pronta, si vedranno due connessioni server in Esplora oggetti. Una connessione ServerAdmin e una connessione LoaderRC60.

    ![La connessione riesce](media/load-data-wideworldimportersdw/connected-as-new-login.png)

## <a name="create-external-tables-and-objects"></a>Creare tabelle e oggetti esterni

Ora è possibile iniziare il processo di caricamento dei dati nel nuovo data warehouse. Per riferimento futuro, per sapere come inserire i dati nell'archivio BLOB di Azure o come caricarli direttamente dall'origine in SQL Data Warehouse, vedere la [panoramica del caricamento](sql-data-warehouse-overview-load.md).

Eseguire gli script SQL seguenti per specificare le informazioni sui dati che si vuole caricare. Queste informazioni includono la posizione dei dati, il formato del contenuto dei dati e la definizione della tabella per i dati. I dati si trovano in un BLOB di Azure pubblico.

1. Nella sezione precedente è stato eseguito l'accesso al data warehouse come LoaderRC60. In SSMS fare clic con il pulsante destro del mouse su **SampleDW** sotto la connessione LoaderRC60 e selezionare **Nuova query**.  Verrà visualizzata una nuova finestra di query, 

    ![Finestra della nuova query di caricamento](media/load-data-wideworldimportersdw/new-loading-query.png)

2. Confrontare la finestra della query all'immagine precedente.  Verificare che la nuova finestra della query sia in esecuzione come LoaderRC60 ed esegua query sul database SampleDW. Usare questa finestra della query per eseguire tutte le operazioni di caricamento.

3. Creare una chiave master per il database SampleDW. È necessario creare una chiave master solo una volta per ogni database. 

    ```sql
    CREATE MASTER KEY;
    ```

4. Eseguire la seguente istruzione [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql.md) per definire la posizione del BLOB di Azure. Questo è il percorso dei dati esterni relativi ai taxi.  Per eseguire un comando che è stato accodato nella finestra della query, evidenziare i comandi che si vuole eseguire e fare clic su **Esegui**.

    ```sql
    CREATE EXTERNAL DATA SOURCE WWIStorage
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://wideworldimporters@sqldwholdata.blob.core.windows.net'
    );
    ```

5. Eseguire l'istruzione T-SQL [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql.md) riportata di seguito per specificare le caratteristiche di formattazione e le opzioni per il file di dati esterno. Questa istruzione specifica che i dati esterni sono memorizzati come testo e i valori sono separati dal carattere barra verticale ("|").  

    ```sql
    CREATE EXTERNAL FILE FORMAT TextFileFormat 
    WITH 
    (   
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS
        (   
            FIELD_TERMINATOR = '|',
            USE_TYPE_DEFAULT = FALSE 
        )
    );
    ```

6.  Eseguire l'istruzione [CREATE SCHEMA](/sql/t-sql/statements/create-schema-transact-sql.md) riportata di seguito per creare uno schema per il formato del file esterno. Lo schema ext fornisce un modo per organizzare le tabelle esterne da creare. Lo schema wwi organizza le tabelle standard che conterranno i dati. 

    ```sql
    CREATE SCHEMA ext;
    GO
    CREATE SCHEMA wwi;
    ```

7. Creare le tabelle esterne. La definizioni di tabella vengono archiviate in SQL Data Warehouse, ma le tabelle fanno riferimento a dati archiviati nell'archivio BLOB di Azure. Eseguire i comandi T-SQL seguenti per creare alcune tabelle esterne che fanno riferimento al BLOB di Azure definito in precedenza nell'origine dati esterna.

    ```sql
    CREATE EXTERNAL TABLE [ext].[dimension_City](
        [City Key] [int] NOT NULL,
        [WWI City ID] [int] NOT NULL,
        [City] [nvarchar](50) NOT NULL,
        [State Province] [nvarchar](50) NOT NULL,
        [Country] [nvarchar](60) NOT NULL,
        [Continent] [nvarchar](30) NOT NULL,
        [Sales Territory] [nvarchar](50) NOT NULL,
        [Region] [nvarchar](30) NOT NULL,
        [Subregion] [nvarchar](30) NOT NULL,
        [Location] [nvarchar](76) NULL,
        [Latest Recorded Population] [bigint] NOT NULL,
        [Valid From] [datetime2](7) NOT NULL,
        [Valid To] [datetime2](7) NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH (LOCATION='/v1/dimension_City/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );  
    CREATE EXTERNAL TABLE [ext].[dimension_Customer] (
        [Customer Key] [int] NOT NULL,
        [WWI Customer ID] [int] NOT NULL,
        [Customer] [nvarchar](100) NOT NULL,
        [Bill To Customer] [nvarchar](100) NOT NULL,
        [Category] [nvarchar](50) NOT NULL,
        [Buying Group] [nvarchar](50) NOT NULL,
        [Primary Contact] [nvarchar](50) NOT NULL,
        [Postal Code] [nvarchar](10) NOT NULL,
        [Valid From] [datetime2](7) NOT NULL,
        [Valid To] [datetime2](7) NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH (LOCATION='/v1/dimension_Customer/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );  
    CREATE EXTERNAL TABLE [ext].[dimension_Employee] (
        [Employee Key] [int] NOT NULL,
        [WWI Employee ID] [int] NOT NULL,
        [Employee] [nvarchar](50) NOT NULL,
        [Preferred Name] [nvarchar](50) NOT NULL,
        [Is Salesperson] [bit] NOT NULL,
        [Photo] [varbinary](300) NULL,
        [Valid From] [datetime2](7) NOT NULL,
        [Valid To] [datetime2](7) NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH ( LOCATION='/v1/dimension_Employee/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[dimension_PaymentMethod] (
        [Payment Method Key] [int] NOT NULL,
        [WWI Payment Method ID] [int] NOT NULL,
        [Payment Method] [nvarchar](50) NOT NULL,
        [Valid From] [datetime2](7) NOT NULL,
        [Valid To] [datetime2](7) NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH ( LOCATION ='/v1/dimension_PaymentMethod/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[dimension_StockItem](
        [Stock Item Key] [int] NOT NULL,
        [WWI Stock Item ID] [int] NOT NULL,
        [Stock Item] [nvarchar](100) NOT NULL,
        [Color] [nvarchar](20) NOT NULL,
        [Selling Package] [nvarchar](50) NOT NULL,
        [Buying Package] [nvarchar](50) NOT NULL,
        [Brand] [nvarchar](50) NOT NULL,
        [Size] [nvarchar](20) NOT NULL,
        [Lead Time Days] [int] NOT NULL,
        [Quantity Per Outer] [int] NOT NULL,
        [Is Chiller Stock] [bit] NOT NULL,
        [Barcode] [nvarchar](50) NULL,
        [Tax Rate] [decimal](18, 3) NOT NULL,
        [Unit Price] [decimal](18, 2) NOT NULL,
        [Recommended Retail Price] [decimal](18, 2) NULL,
        [Typical Weight Per Unit] [decimal](18, 3) NOT NULL,
        [Photo] [varbinary](300) NULL,
        [Valid From] [datetime2](7) NOT NULL,
        [Valid To] [datetime2](7) NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH ( LOCATION ='/v1/dimension_StockItem/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[dimension_Supplier](
        [Supplier Key] [int] NOT NULL,
        [WWI Supplier ID] [int] NOT NULL,
        [Supplier] [nvarchar](100) NOT NULL,
        [Category] [nvarchar](50) NOT NULL,
        [Primary Contact] [nvarchar](50) NOT NULL,
        [Supplier Reference] [nvarchar](20) NULL,
        [Payment Days] [int] NOT NULL,
        [Postal Code] [nvarchar](10) NOT NULL,
        [Valid From] [datetime2](7) NOT NULL,
        [Valid To] [datetime2](7) NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH ( LOCATION ='/v1/dimension_Supplier/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[dimension_TransactionType](
        [Transaction Type Key] [int] NOT NULL,
        [WWI Transaction Type ID] [int] NOT NULL,
        [Transaction Type] [nvarchar](50) NOT NULL,
        [Valid From] [datetime2](7) NOT NULL,
        [Valid To] [datetime2](7) NOT NULL,
        [Lineage Key] [int] NOT NULL
    )    
    WITH ( LOCATION ='/v1/dimension_TransactionType/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[fact_Movement] (
        [Movement Key] [bigint] NOT NULL,
        [Date Key] [date] NOT NULL,
        [Stock Item Key] [int] NOT NULL,
        [Customer Key] [int] NULL,
        [Supplier Key] [int] NULL,
        [Transaction Type Key] [int] NOT NULL,
        [WWI Stock Item Transaction ID] [int] NOT NULL,
        [WWI Invoice ID] [int] NULL,
        [WWI Purchase Order ID] [int] NULL,
        [Quantity] [int] NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH ( LOCATION ='/v1/fact_Movement/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[fact_Order] (
        [Order Key] [bigint] NOT NULL,
        [City Key] [int] NOT NULL,
        [Customer Key] [int] NOT NULL,
        [Stock Item Key] [int] NOT NULL,
        [Order Date Key] [date] NOT NULL,
        [Picked Date Key] [date] NULL,
        [Salesperson Key] [int] NOT NULL,
        [Picker Key] [int] NULL,
        [WWI Order ID] [int] NOT NULL,
        [WWI Backorder ID] [int] NULL,
        [Description] [nvarchar](100) NOT NULL,
        [Package] [nvarchar](50) NOT NULL,
        [Quantity] [int] NOT NULL,
        [Unit Price] [decimal](18, 2) NOT NULL,
        [Tax Rate] [decimal](18, 3) NOT NULL,
        [Total Excluding Tax] [decimal](18, 2) NOT NULL,
        [Tax Amount] [decimal](18, 2) NOT NULL,
        [Total Including Tax] [decimal](18, 2) NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH ( LOCATION ='/v1/fact_Order/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[fact_Purchase] (
        [Purchase Key] [bigint] NOT NULL,
        [Date Key] [date] NOT NULL,
        [Supplier Key] [int] NOT NULL,
        [Stock Item Key] [int] NOT NULL,
        [WWI Purchase Order ID] [int] NULL,
        [Ordered Outers] [int] NOT NULL,
        [Ordered Quantity] [int] NOT NULL,
        [Received Outers] [int] NOT NULL,
        [Package] [nvarchar](50) NOT NULL,
        [Is Order Finalized] [bit] NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH ( LOCATION ='/v1/fact_Purchase/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[fact_Sale] (
        [Sale Key] [bigint] NOT NULL,
        [City Key] [int] NOT NULL,
        [Customer Key] [int] NOT NULL,
        [Bill To Customer Key] [int] NOT NULL,
        [Stock Item Key] [int] NOT NULL,
        [Invoice Date Key] [date] NOT NULL,
        [Delivery Date Key] [date] NULL,
        [Salesperson Key] [int] NOT NULL,
        [WWI Invoice ID] [int] NOT NULL,
        [Description] [nvarchar](100) NOT NULL,
        [Package] [nvarchar](50) NOT NULL,
        [Quantity] [int] NOT NULL,
        [Unit Price] [decimal](18, 2) NOT NULL,
        [Tax Rate] [decimal](18, 3) NOT NULL,
        [Total Excluding Tax] [decimal](18, 2) NOT NULL,
        [Tax Amount] [decimal](18, 2) NOT NULL,
        [Profit] [decimal](18, 2) NOT NULL,
        [Total Including Tax] [decimal](18, 2) NOT NULL,
        [Total Dry Items] [int] NOT NULL,
        [Total Chiller Items] [int] NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH ( LOCATION ='/v1/fact_Sale/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[fact_StockHolding] (
        [Stock Holding Key] [bigint] NOT NULL,
        [Stock Item Key] [int] NOT NULL,
        [Quantity On Hand] [int] NOT NULL,
        [Bin Location] [nvarchar](20) NOT NULL,
        [Last Stocktake Quantity] [int] NOT NULL,
        [Last Cost Price] [decimal](18, 2) NOT NULL,
        [Reorder Level] [int] NOT NULL,
        [Target Stock Level] [int] NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH ( LOCATION ='/v1/fact_StockHolding/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[fact_Transaction] (
        [Transaction Key] [bigint] NOT NULL,
        [Date Key] [date] NOT NULL,
        [Customer Key] [int] NULL,
        [Bill To Customer Key] [int] NULL,
        [Supplier Key] [int] NULL,
        [Transaction Type Key] [int] NOT NULL,
        [Payment Method Key] [int] NULL,
        [WWI Customer Transaction ID] [int] NULL,
        [WWI Supplier Transaction ID] [int] NULL,
        [WWI Invoice ID] [int] NULL,
        [WWI Purchase Order ID] [int] NULL,
        [Supplier Invoice Number] [nvarchar](20) NULL,
        [Total Excluding Tax] [decimal](18, 2) NOT NULL,
        [Tax Amount] [decimal](18, 2) NOT NULL,
        [Total Including Tax] [decimal](18, 2) NOT NULL,
        [Outstanding Balance] [decimal](18, 2) NOT NULL,
        [Is Finalized] [bit] NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH ( LOCATION ='/v1/fact_Transaction/',   
        DATA_SOURCE = WWIStorage,  
        FILE_FORMAT = TextFileFormat,
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 0
    );
    ```

8. In Esplora oggetti espandere SampleDW per vedere l'elenco delle tabelle esterne appena create.

    ![Visualizzare le tabelle esterne](media/load-data-wideworldimportersdw/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>Caricare i dati nel data warehouse

Questa sezione usa le tabelle esterne appena definite per caricare i dati di esempio dal BLOB di Azure a SQL Data Warehouse.  

> [!NOTE]
> Questa esercitazione carica i dati direttamente nella tabella finale. In un ambiente di produzione si userà in genere CREATE TABLE AS SELECT per eseguire il caricamento in una tabella di staging. Mentre i dati si trovano nella tabella di staging è possibile eseguire le trasformazioni eventualmente necessarie. Per accodare i dati della tabella di staging a una tabella di produzione, è possibile usare l'istruzione INSERT...SELECT. Per altre informazioni, vedere [Inserimento di dati in una tabella di produzione](guidance-for-loading-data.md#inserting-data-into-a-production-table).
> 

Lo script usa l'istruzione T-SQL [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) per caricare i dati dal BLOB del servizio di archiviazione di Azure nelle nuove tabelle del data warehouse. CTAS crea una nuova tabella in base ai risultati di un'istruzione SELECT. La nuova tabella ha le stesse colonne e gli stessi tipi di dati dei risultati dell'istruzione SELECT. Quando l'istruzione SELECT seleziona da una tabella esterna, SQL Data Warehouse importa i dati in una tabella relazionale del data warehouse. 

Questo script non carica i dati nelle tabelle wwi.dimension_Date e wwi.fact_Sales. Queste tabelle sono generate in un passaggio successivo, in modo che possano avere un numero consistente di righe.

1. Eseguire lo script seguente per caricare i dati nelle nuove tabelle nel data warehouse.

    ```sql
    CREATE TABLE [wwi].[dimension_City]
    WITH
    ( 
        DISTRIBUTION = REPLICATE,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[dimension_City]
    OPTION (LABEL = 'CTAS : Load [wwi].[dimension_City]')
    ;

    CREATE TABLE [wwi].[dimension_Customer]
    WITH
    ( 
        DISTRIBUTION = REPLICATE,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[dimension_Customer]
    OPTION (LABEL = 'CTAS : Load [wwi].[dimension_Customer]')
    ;

    CREATE TABLE [wwi].[dimension_Employee]
    WITH
    ( 
        DISTRIBUTION = REPLICATE,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[dimension_Employee]
    OPTION (LABEL = 'CTAS : Load [wwi].[dimension_Employee]')
    ;

    CREATE TABLE [wwi].[dimension_PaymentMethod]
    WITH
    ( 
        DISTRIBUTION = REPLICATE,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[dimension_PaymentMethod]
    OPTION (LABEL = 'CTAS : Load [wwi].[dimension_PaymentMethod]')
    ;

    CREATE TABLE [wwi].[dimension_StockItem]
    WITH
    ( 
        DISTRIBUTION = REPLICATE,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[dimension_StockItem]
    OPTION (LABEL = 'CTAS : Load [wwi].[dimension_StockItem]')
    ;

    CREATE TABLE [wwi].[dimension_Supplier]
    WITH
    ( 
        DISTRIBUTION = REPLICATE,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[dimension_Supplier]
    OPTION (LABEL = 'CTAS : Load [wwi].[dimension_Supplier]')
    ;

    CREATE TABLE [wwi].[dimension_TransactionType]
    WITH
    ( 
        DISTRIBUTION = REPLICATE,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[dimension_TransactionType]
    OPTION (LABEL = 'CTAS : Load [wwi].[dimension_TransactionType]')
    ;

    CREATE TABLE [wwi].[fact_Movement]
    WITH
    ( 
        DISTRIBUTION = HASH([Movement Key]),
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[fact_Movement]
    OPTION (LABEL = 'CTAS : Load [wwi].[fact_Movement]')
    ;

    CREATE TABLE [wwi].[fact_Order]
    WITH
    ( 
        DISTRIBUTION = HASH([Order Key]),
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[fact_Order]
    OPTION (LABEL = 'CTAS : Load [wwi].[fact_Order]')
    ;

    CREATE TABLE [wwi].[fact_Purchase]
    WITH
    ( 
        DISTRIBUTION = HASH([Purchase Key]),
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[fact_Purchase]
    OPTION (LABEL = 'CTAS : Load [wwi].[fact_Purchase]')
    ;

    CREATE TABLE [wwi].[seed_Sale]
    WITH
    ( 
        DISTRIBUTION = HASH([WWI Invoice ID]),
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[fact_Sale]
    OPTION (LABEL = 'CTAS : Load [wwi].[seed_Sale]')
    ;

    CREATE TABLE [wwi].[fact_StockHolding]
    WITH
    ( 
        DISTRIBUTION = HASH([Stock Holding Key]),
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[fact_StockHolding]
    OPTION (LABEL = 'CTAS : Load [wwi].[fact_StockHolding]')
    ;

    CREATE TABLE [wwi].[fact_Transaction]
    WITH
    ( 
        DISTRIBUTION = HASH([Transaction Key]),
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[fact_Transaction]
    OPTION (LABEL = 'CTAS : Load [wwi].[fact_Transaction]')
    ;
    ```

2. Visualizzare i dati man mano che vengono caricati. Vengono caricati alcuni GB di dati, che vengono compressi in indici cluster columnstore a prestazioni elevate. Aprire una nuova finestra di query su SampleDW ed eseguire la query seguente per visualizzare lo stato del caricamento. Dopo aver avviato la query, attendere che SQL Data Warehouse esegua le operazioni necessarie.

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
    WHERE
        r.[label] = 'CTAS : Load [wwi].[dimension_City]' OR
        r.[label] = 'CTAS : Load [wwi].[dimension_Customer]' OR
        r.[label] = 'CTAS : Load [wwi].[dimension_Employee]' OR
        r.[label] = 'CTAS : Load [wwi].[dimension_PaymentMethod]' OR
        r.[label] = 'CTAS : Load [wwi].[dimension_StockItem]' OR
        r.[label] = 'CTAS : Load [wwi].[dimension_Supplier]' OR
        r.[label] = 'CTAS : Load [wwi].[dimension_TransactionType]' OR
        r.[label] = 'CTAS : Load [wwi].[fact_Movement]' OR
        r.[label] = 'CTAS : Load [wwi].[fact_Order]' OR
        r.[label] = 'CTAS : Load [wwi].[fact_Purchase]' OR
        r.[label] = 'CTAS : Load [wwi].[fact_StockHolding]' OR
        r.[label] = 'CTAS : Load [wwi].[fact_Transaction]'
    GROUP BY
        r.command,
        s.request_id,
        r.status
    ORDER BY
        nbr_files desc, 
        gb_processed desc;
    ```

3. Visualizzare tutte le query di sistema.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. I dati vengono caricati nel data warehouse.

    ![Visualizzare le tabelle caricate](media/load-data-wideworldimportersdw/view-loaded-tables.png)

## <a name="create-tables-and-procedures-to-generate-the-date-and-sales-tables"></a>Creare tabelle e procedure per generare le tabelle delle date e delle vendite

In questa sezione vengono create le tabelle wwi.dimension_Date e wwi.fact_Sales. Vengono inoltre create stored procedure in grado di generare milioni di righe nelle tabelle wwi.dimension_Date e wwi.fact_Sales.

1. Creare le tabelle dimension_Date e fact_Sale.  

    ```sql
    CREATE TABLE [wwi].[dimension_Date]
    (
        [Date] [datetime] NOT NULL,
        [Day Number] [int] NOT NULL,
        [Day] [nvarchar](10) NOT NULL,
        [Month] [nvarchar](10) NOT NULL,
        [Short Month] [nvarchar](3) NOT NULL,
        [Calendar Month Number] [int] NOT NULL,
        [Calendar Month Label] [nvarchar](20) NOT NULL,
        [Calendar Year] [int] NOT NULL,
        [Calendar Year Label] [nvarchar](10) NOT NULL,
        [Fiscal Month Number] [int] NOT NULL,
        [Fiscal Month Label] [nvarchar](20) NOT NULL,
        [Fiscal Year] [int] NOT NULL,
        [Fiscal Year Label] [nvarchar](10) NOT NULL,
        [ISO Week Number] [int] NOT NULL
    )
    WITH 
    (
        DISTRIBUTION = REPLICATE,
        CLUSTERED INDEX ([Date])
    );
    CREATE TABLE [wwi].[fact_Sale]
    (
        [Sale Key] [bigint] IDENTITY(1,1) NOT NULL,
        [City Key] [int] NOT NULL,
        [Customer Key] [int] NOT NULL,
        [Bill To Customer Key] [int] NOT NULL,
        [Stock Item Key] [int] NOT NULL,
        [Invoice Date Key] [date] NOT NULL,
        [Delivery Date Key] [date] NULL,
        [Salesperson Key] [int] NOT NULL,
        [WWI Invoice ID] [int] NOT NULL,
        [Description] [nvarchar](100) NOT NULL,
        [Package] [nvarchar](50) NOT NULL,
        [Quantity] [int] NOT NULL,
        [Unit Price] [decimal](18, 2) NOT NULL,
        [Tax Rate] [decimal](18, 3) NOT NULL,
        [Total Excluding Tax] [decimal](18, 2) NOT NULL,
        [Tax Amount] [decimal](18, 2) NOT NULL,
        [Profit] [decimal](18, 2) NOT NULL,
        [Total Including Tax] [decimal](18, 2) NOT NULL,
        [Total Dry Items] [int] NOT NULL,
        [Total Chiller Items] [int] NOT NULL,
        [Lineage Key] [int] NOT NULL
    )
    WITH
    (
        DISTRIBUTION = HASH ( [WWI Invoice ID] ),
        CLUSTERED COLUMNSTORE INDEX
    )
    ```

2. Creare [wwi].[InitialSalesDataPopulation] per aumentare il numero di righe [wwi].[seed_Sale] di un fattore di otto. 

    ```sql
    CREATE PROCEDURE [wwi].[InitialSalesDataPopulation] AS
    BEGIN
        INSERT INTO [wwi].[seed_Sale] (
            [Sale Key], [City Key], [Customer Key], [Bill To Customer Key], [Stock Item Key], [Invoice Date Key], [Delivery Date Key], [Salesperson Key], [WWI Invoice ID], [Description], [Package], [Quantity], [Unit Price], [Tax Rate], [Total Excluding Tax], [Tax Amount], [Profit], [Total Including Tax], [Total Dry Items], [Total Chiller Items], [Lineage Key]
        )
        SELECT
            [Sale Key], [City Key], [Customer Key], [Bill To Customer Key], [Stock Item Key], [Invoice Date Key], [Delivery Date Key], [Salesperson Key], [WWI Invoice ID], [Description], [Package], [Quantity], [Unit Price], [Tax Rate], [Total Excluding Tax], [Tax Amount], [Profit], [Total Including Tax], [Total Dry Items], [Total Chiller Items], [Lineage Key]
        FROM [wwi].[seed_Sale]

        INSERT INTO [wwi].[seed_Sale] (
            [Sale Key], [City Key], [Customer Key], [Bill To Customer Key], [Stock Item Key], [Invoice Date Key], [Delivery Date Key], [Salesperson Key], [WWI Invoice ID], [Description], [Package], [Quantity], [Unit Price], [Tax Rate], [Total Excluding Tax], [Tax Amount], [Profit], [Total Including Tax], [Total Dry Items], [Total Chiller Items], [Lineage Key]
        )
        SELECT
            [Sale Key], [City Key], [Customer Key], [Bill To Customer Key], [Stock Item Key], [Invoice Date Key], [Delivery Date Key], [Salesperson Key], [WWI Invoice ID], [Description], [Package], [Quantity], [Unit Price], [Tax Rate], [Total Excluding Tax], [Tax Amount], [Profit], [Total Including Tax], [Total Dry Items], [Total Chiller Items], [Lineage Key]
        FROM [wwi].[seed_Sale]

        INSERT INTO [wwi].[seed_Sale] (
            [Sale Key], [City Key], [Customer Key], [Bill To Customer Key], [Stock Item Key], [Invoice Date Key], [Delivery Date Key], [Salesperson Key], [WWI Invoice ID], [Description], [Package], [Quantity], [Unit Price], [Tax Rate], [Total Excluding Tax], [Tax Amount], [Profit], [Total Including Tax], [Total Dry Items], [Total Chiller Items], [Lineage Key]
        )
        SELECT
            [Sale Key], [City Key], [Customer Key], [Bill To Customer Key], [Stock Item Key], [Invoice Date Key], [Delivery Date Key], [Salesperson Key], [WWI Invoice ID], [Description], [Package], [Quantity], [Unit Price], [Tax Rate], [Total Excluding Tax], [Tax Amount], [Profit], [Total Including Tax], [Total Dry Items], [Total Chiller Items], [Lineage Key]
        FROM [wwi].[seed_Sale]
    END
    ```

3. Creare questa stored procedure per inserire righe in wwi.dimension_Date.

    ```sql
    CREATE PROCEDURE [wwi].[PopulateDateDimensionForYear] @Year [int] AS
    BEGIN
        IF OBJECT_ID('tempdb..#month', 'U') IS NOT NULL 
            DROP TABLE #month
        CREATE TABLE #month (
            monthnum int,
            numofdays int
        )
        WITH ( DISTRIBUTION = ROUND_ROBIN, heap )
        INSERT INTO #month
            SELECT 1, 31 UNION SELECT 2, CASE WHEN (@YEAR % 4 = 0 AND @YEAR % 100 <> 0) OR @YEAR % 400 = 0 THEN 29 ELSE 28 END UNION SELECT 3,31 UNION SELECT 4,30 UNION SELECT 5,31 UNION SELECT 6,30 UNION SELECT 7,31 UNION SELECT 8,31 UNION SELECT 9,30 UNION SELECT 10,31 UNION SELECT 11,30 UNION SELECT 12,31

        IF OBJECT_ID('tempdb..#days', 'U') IS NOT NULL 
            DROP TABLE #days
        CREATE TABLE #days (days int)
        WITH (DISTRIBUTION = ROUND_ROBIN, HEAP)

        INSERT INTO #days
            SELECT 1 UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5 UNION SELECT 6 UNION SELECT 7 UNION SELECT 8 UNION SELECT 9 UNION SELECT 10 UNION SELECT 11 UNION SELECT 12 UNION SELECT 13 UNION SELECT 14 UNION SELECT 15 UNION SELECT 16 UNION SELECT 17 UNION SELECT 18 UNION SELECT 19 UNION SELECT 20    UNION SELECT 21 UNION SELECT 22 UNION SELECT 23 UNION SELECT 24 UNION SELECT 25 UNION SELECT 26 UNION SELECT 27 UNION SELECT 28 UNION SELECT 29 UNION SELECT 30 UNION SELECT 31

        INSERT [wwi].[dimension_Date] (
            [Date], [Day Number], [Day], [Month], [Short Month], [Calendar Month Number], [Calendar Month Label], [Calendar Year], [Calendar Year Label], [Fiscal Month Number], [Fiscal Month Label], [Fiscal Year], [Fiscal Year Label], [ISO Week Number] 
        )
        SELECT
            CAST(CAST(monthnum AS VARCHAR(2)) + '/' + CAST([days] AS VARCHAR(3)) + '/' + CAST(@year AS CHAR(4)) AS DATE) AS [Date]
            ,DAY(CAST(CAST(monthnum AS VARCHAR(2)) + '/' + CAST([days] AS VARCHAR(3)) + '/' + CAST(@year AS CHAR(4)) AS DATE)) AS [Day Number]
            ,CAST(DATENAME(day, CAST(CAST(monthnum AS VARCHAR(2)) + '/' + CAST([days] AS VARCHAR(3)) + '/' + CAST(@year AS CHAR(4)) AS DATE)) AS NVARCHAR(10)) AS [Day]
            ,CAST(DATENAME(month, CAST(CAST(monthnum AS VARCHAR(2)) + '/' + CAST([days] AS VARCHAR(3)) + '/' + CAST(@year as char(4)) AS DATE)) AS nvarchar(10)) AS [Month]
            ,CAST(SUBSTRING(DATENAME(month, CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)), 1, 3) AS nvarchar(3)) AS [Short Month]
            ,MONTH(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) AS [Calendar Month Number]
            ,CAST(N'CY' + CAST(YEAR(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) AS nvarchar(4)) + N'-' + SUBSTRING(DATENAME(month, CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)), 1, 3) AS nvarchar(10)) AS [Calendar Month Label]
            ,YEAR(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) AS [Calendar Year]
            ,CAST(N'CY' + CAST(YEAR(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) AS nvarchar(4)) AS nvarchar(10)) AS [Calendar Year Label]
            ,CASE WHEN MONTH(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) IN (11, 12)
            THEN MONTH(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) - 10
            ELSE MONTH(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) + 2 END AS [Fiscal Month Number]
            ,CAST(N'FY' + CAST(CASE WHEN MONTH(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) IN (11, 12)
            THEN YEAR(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) + 1
            ELSE YEAR(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) END AS nvarchar(4)) + N'-' + SUBSTRING(DATENAME(month, CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)), 1, 3) AS nvarchar(20)) AS [Fiscal Month Label]
            ,CASE WHEN MONTH(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) IN (11, 12)
            THEN YEAR(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) + 1
            ELSE YEAR(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) END AS [Fiscal Year]
            ,CAST(N'FY' + CAST(CASE WHEN MONTH(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) IN (11, 12)
            THEN YEAR(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) + 1
            ELSE YEAR(CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE))END AS nvarchar(4)) AS nvarchar(10)) AS [Fiscal Year Label]
            , DATEPART(ISO_WEEK, CAST(CAST(monthnum as varchar(2)) + '/' + CAST([days] as varchar(3)) + '/' + CAST(@year as char(4)) AS DATE)) AS [ISO Week Number]
    FROM #month m
        CROSS JOIN #days d
    WHERE d.days <= m.numofdays

    DROP table #month;
    DROP table #days;
    END;
    ```
4. Creare questa procedura per popolare le tabelle wwi.dimension_Date e wwi.fact_Sales. La procedura chiama [wwi].[PopulateDateDimensionForYear] per popolare wwi.dimension_Date.

    ```sql
    CREATE PROCEDURE [wwi].[Configuration_PopulateLargeSaleTable] @EstimatedRowsPerDay [bigint],@Year [int] AS
    BEGIN
        SET NOCOUNT ON;
        SET XACT_ABORT ON;

        EXEC [wwi].[PopulateDateDimensionForYear] @Year;

        DECLARE @OrderCounter bigint = 0;
        DECLARE @NumberOfSalesPerDay bigint = @EstimatedRowsPerDay;
        DECLARE @DateCounter date; 
        DECLARE @StartingSaleKey bigint;
        DECLARE @MaximumSaleKey bigint = (SELECT MAX([Sale Key]) FROM wwi.seed_Sale);
        DECLARE @MaxDate date;
        SET @MaxDate = (SELECT MAX([Invoice Date Key]) FROM wwi.fact_Sale)
        IF ( @MaxDate < CAST(@YEAR AS CHAR(4)) + '1231') AND (@MaxDate > CAST(@YEAR AS CHAR(4)) + '0101')
            SET @DateCounter = @MaxDate
        ELSE
            SET @DateCounter= CAST(@Year as char(4)) + '0101';

        PRINT 'Targeting ' + CAST(@NumberOfSalesPerDay AS varchar(20)) + ' sales per day.';

        DECLARE @OutputCounter varchar(20);
        DECLARE @variance DECIMAL(18,10);
        DECLARE @VariantNumberOfSalesPerDay BIGINT;

        WHILE @DateCounter < CAST(@YEAR AS CHAR(4)) + '1231'
        BEGIN
            SET @OutputCounter = CONVERT(varchar(20), @DateCounter, 112);
            RAISERROR(@OutputCounter, 0, 1);
            SET @variance = (SELECT RAND() * 10)*.01 + .95
            SET @VariantNumberOfSalesPerDay = FLOOR(@NumberOfSalesPerDay * @variance)

            SET @StartingSaleKey = @MaximumSaleKey - @VariantNumberOfSalesPerDay - FLOOR(RAND() * 20000);
            SET @OrderCounter = 0;

            INSERT [wwi].[fact_Sale] (
                [City Key], [Customer Key], [Bill To Customer Key], [Stock Item Key], [Invoice Date Key], [Delivery Date Key], [Salesperson Key], [WWI Invoice ID], [Description], Package, Quantity, [Unit Price], [Tax Rate], [Total Excluding Tax], [Tax Amount], Profit, [Total Including Tax], [Total Dry Items], [Total Chiller Items], [Lineage Key]
            )
            SELECT TOP(@VariantNumberOfSalesPerDay)
                [City Key], [Customer Key], [Bill To Customer Key], [Stock Item Key], @DateCounter, DATEADD(day, 1, @DateCounter), [Salesperson Key], [WWI Invoice ID], [Description], Package, Quantity, [Unit Price], [Tax Rate], [Total Excluding Tax], [Tax Amount], Profit, [Total Including Tax], [Total Dry Items], [Total Chiller Items], [Lineage Key]
            FROM [wwi].[seed_Sale]
            WHERE 
                 --[Sale Key] > @StartingSaleKey and /* IDENTITY DOES NOT WORK THE SAME IN SQLDW AND CAN'T USE THIS METHOD FOR VARIANT */
                [Invoice Date Key] >=cast(@YEAR AS CHAR(4)) + '-01-01'
            ORDER BY [Sale Key];

            SET @DateCounter = DATEADD(day, 1, @DateCounter);
        END;

    END;

## Generate millions of rows
Use the stored procedures you created to generate millions of rows in the wwi.fact_Sales table, and corresponding data in the wwi.dimension_Date table. 


1. Run this procedure to seed the [wwi].[seed_Sale] with more rows.

    ```sql    
    EXEC [wwi].[InitialSalesDataPopulation]
    ```

2. Eseguire questa procedura per popolare wwi.fact_Sales con 100.000 righe al giorno per ogni giorno dell'anno 2000.

    ```sql
    EXEC [wwi].[Configuration_PopulateLargeSaleTable] 100000, 2000
    ```
3. La progressiva generazione di dati per l'intero anno eseguita nel passaggio precedente può richiedere tempo.  Per vedere a quale giorno è arrivato il processo, aprire una nuova query ed eseguire questo comando SQL:

    ```sql
    SELECT MAX([Invoice Date Key]) FROM wwi.fact_Sale;
    ```

4. Eseguire il comando seguente per vedere lo spazio occupato.

    ```sql
    EXEC sp_spaceused N'wwi.fact_Sale';
    ```

## <a name="populate-the-replicated-table-cache"></a>Popolare la cache della tabella replicata
SQL Data Warehouse replica una tabella tramite la memorizzazione dei dati nella cache di ogni nodo di calcolo. La cache viene popolata quando viene eseguita una query sulla tabella. La prima query in una tabella replicata può quindi richiedere più tempo per popolare la cache. Dopo il popolamento della cache, le query sulle tabelle replicate verranno eseguite più rapidamente.

Eseguire queste query SQL per popolare la cache delle tabelle replicate nei nodi di calcolo. 

    ```sql
    SELECT TOP 1 * FROM [wwi].[dimension_City];
    SELECT TOP 1 * FROM [wwi].[dimension_Customer];
    SELECT TOP 1 * FROM [wwi].[dimension_Date];
    SELECT TOP 1 * FROM [wwi].[dimension_Employee];
    SELECT TOP 1 * FROM [wwi].[dimension_PaymentMethod];
    SELECT TOP 1 * FROM [wwi].[dimension_StockItem];
    SELECT TOP 1 * FROM [wwi].[dimension_Supplier];
    SELECT TOP 1 * FROM [wwi].[dimension_TransactionType];
    ```

## <a name="create-statistics-on-newly-loaded-data"></a>Creare statistiche sui dati appena caricati

Per ottenere prestazioni elevate per le query, è importante creare statistiche su ogni colonna di ogni tabella dopo il primo caricamento. È anche importante aggiornare le statistiche dopo modifiche sostanziali dei dati. 

1. Creare questa stored procedure che aggiorna le statistiche per tutte le colonne di tutte le tabelle.

    ```sql
    CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
    (   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
    ,   @sample_pct     tinyint
    )
    AS

    IF @create_type IS NULL
    BEGIN
        SET @create_type = 1;
    END;

    IF @create_type NOT IN (1,2,3)
    BEGIN
        THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
    END;

    IF @sample_pct IS NULL
    BEGIN;
        SET @sample_pct = 20;
    END;

    IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
    BEGIN;
        DROP TABLE #stats_ddl;
    END;
    
    CREATE TABLE #stats_ddl
    WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
            ,   LOCATION        = USER_DB
            )
    AS
    WITH T
    AS
    (
    SELECT      t.[name]                        AS [table_name]
    ,           s.[name]                        AS [table_schema_name]
    ,           c.[name]                        AS [column_name]
    ,           c.[column_id]                   AS [column_id]
    ,           t.[object_id]                   AS [object_id]
    ,           ROW_NUMBER()
                OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
    FROM        sys.[tables] t
    JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
    JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
    LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                        AND l.[column_id]       = c.[column_id]
                                        AND l.[stats_column_id] = 1
    LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
    WHERE       l.[object_id] IS NULL
    AND            e.[object_id] IS NULL -- not an external table
    )
    SELECT  [table_schema_name]
    ,       [table_name]
    ,       [column_name]
    ,       [column_id]
    ,       [object_id]
    ,       [seq_nmbr]
    ,       CASE @create_type
            WHEN 1
            THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
            WHEN 2
            THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
            WHEN 3
            THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
            END AS create_stat_ddl
    FROM T
    ;

    DECLARE @i INT              = 1
    ,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
    ,       @s NVARCHAR(4000)   = N''
    ;

    WHILE @i <= @t
    BEGIN
        SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);
        PRINT @s
        EXEC sp_executesql @s
        SET @i+=1;
    END

    DROP TABLE #stats_ddl;
    ```

2. Eseguire questo comando per creare statistiche per tutte le colonne di tutte le tabelle nel data warehouse.

    ```sql
    EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Le risorse di calcolo e i dati caricati nel data warehouse prevedono un addebito. Questi costi vengono addebitati separatamente.  

Seguire questa procedura per pulire le risorse nel modo desiderato.

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic sul data warehouse.

    ![Pulire le risorse](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Se si vogliono mantenere i dati nelle risorse di archiviazione, è possibile sospendere il calcolo quando il data warehouse non è in uso. Sospendendo il calcolo verrà addebitata solo l'archiviazione dati: sarà possibile riprendere il calcolo quando sarà necessario elaborare i dati. Per sospendere il calcolo, fare clic sul pulsante **Pausa**. Quando si sospende il data warehouse, viene visualizzato il pulsante **Avvia**.  Per riprendere il calcolo, fare clic su **Avvia**.

3. Per evitare di ricevere addebiti in futuro, è possibile eliminare il data warehouse. Per rimuovere il data warehouse in modo da non ricevere addebiti per calcoli o archiviazioni, fare clic su **Elimina**.

4. Per rimuovere il server SQL creato, fare clic su **sample-svr.database.windows.net** nell'immagine precedente e quindi fare clic su **Elimina**.  Prestare attenzione con questa operazione perché eliminando il server saranno eliminati tutti i database assegnati al server.

5. Per rimuovere il gruppo di risorse, fare clic su **SampleRG** e quindi su **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi 
In questa esercitazione è stato descritto come creare un data warehouse e creare un utente per il caricamento dei dati. Sono state create tabelle esterne per definire la struttura per i dati archiviati nel BLOB del servizio di archiviazione di Azure e quindi è stata usata l'istruzione PolyBase CREATE TABLE AS SELECT per caricare dati nel data warehouse. 

Sono state eseguite queste operazioni:
> [!div class="checklist"]
> * È stato creato un data warehouse nel portale di Azure
> * Impostare una regola del firewall a livello di server nel portale di Azure
> * Ci si è connessi al data warehouse con SSMS
> * È stato creato un utente designato per il caricamento dei dati
> * Sono state create tabelle esterne per i dati nel BLOB del servizio di archiviazione di Azure
> * È stata usata l'istruzione T-SQL CTAS per caricare i dati nel data warehouse
> * È stato visualizzato lo stato di avanzamento dei dati durante il caricamento
> * Sono state create statistiche sui nuovi dati caricati

Per informazioni su come eseguire la migrazione di un database esistente in SQL Data Warehouse, passare alla panoramica sulla migrazione.

> [!div class="nextstepaction"]
>[Informazioni su come eseguire la migrazione di un database esistente in SQL Data Warehouse](sql-data-warehouse-overview-migrate.md)
