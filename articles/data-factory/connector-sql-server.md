---
title: Copiare dati da e verso SQL ServerCopy data to and from SQL Server
description: Informazioni su come spostare i dati da e verso il database di SQL Server in locale o in una macchina virtuale di Azure usando Azure Data Factory.Learn about how to move data to and from SQL Server database that is on-premises or in an Azure VM by using Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 063ac32c98d4eb64b676247c0a16f98fa7d1702d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416694"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Copiare dati da e verso SQL Server usando Azure Data FactoryCopy data to and from SQL Server by using Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in uso:Select the version of Azure Data Factory that you're using:"]
> * [Versione 1](v1/data-factory-sqlserver-connector.md)
> * [Versione corrente](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in un database di SQL Server.This article outlines how to use the copy activity in Azure Data Factory to copy data from and to a SQL Server database. Si basa sull'articolo [sulla panoramica dell'attività](copy-activity-overview.md) di copia che presenta una panoramica generale dell'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore DI SQL Server è supportato per le attività seguenti:This SQL Server connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

È possibile copiare i dati da un database di SQL ServerSQL Server in qualsiasi archivio dati sink supportato. In alternativa, è possibile copiare i dati da qualsiasi archivio dati di origine supportato in un database di SQL ServerSQL Server . Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)

In particolare, il connettore SQL Server supporta:

- Versioni di SQL Server 2016, 2014, 2012, 2008 R2, 2008 e 2005.
- La copia dei dati tramite l'autenticazione di SQL o di Windows.
- Come origine, il recupero dei dati tramite una query SQL o una stored procedure.
- Come sink, l'accodamento di dati a una tabella di destinazione o la chiamata a una stored procedure con logica personalizzata durante la copia.

[IL database locale di SQL Server Express](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) non è supportato.

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) non è supportato da questo connettore. Per risolvere il problema, è possibile utilizzare un [connettore ODBC generico](connector-odbc.md) e un driver ODBC di SQL Server. Seguire [queste indicazioni](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) con il download del driver ODBC e le configurazioni delle stringhe di connessione.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà utilizzate per definire le entità di Data Factory specifiche del connettore di database di SQL Server.The following sections provide details about properties that are used to define Data Factory entities specific to the SQL Server database connector.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato SQL ServerSQL Server sono supportate le proprietà seguenti:The following properties are supported for the SQL Server linked service:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **SqlServer**. | Sì |
| connectionString |Specificare le informazioni connectionString necessarie per connettersi al database di SQL Server utilizzando l'autenticazione SQL o l'autenticazione di Windows.Specify **connectionString** information that's needed to connect to the SQL Server database by using either SQL authentication or Windows authentication. Vedere gli esempi seguenti.<br/>È anche possibile inserire una password in Archiviazione delle chiavi di Azure.You also can put a password in Azure Key Vault. Se si tratta di autenticazione SQL, estrarre la `password` configurazione dalla stringa di connessione. Per altre informazioni, vedere l'esempio JSON che segue la tabella e [Archiviare le credenziali in Archiviazione delle chiavi](store-credentials-in-key-vault.md)di Azure.For more information, see the JSON example following the table and Store credentials in Azure Key Vault . |Sì |
| userName |Specificare un nome utente se si utilizza l'autenticazione di Windows.Specify a user name if you use Windows authentication. Ad esempio, **nomedominio\\nomeutente**. |No |
| password |Specificare una password per l'account utente specificato per il nome utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory.Mark this field as **SecureString** to store it securely in Azure Data Factory. In alternativa, è possibile [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |No |
| connectVia | Questo [runtime di integrazione](concepts-integration-runtime.md) viene usato per connettersi all'archivio dati. Per ulteriori informazioni, vedere la sezione [Prerequisiti.](#prerequisites) Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

>[!TIP]
>Se si verifica un errore con il codice di errore "UserErrorFailedToConnectToSqlServer" e un messaggio del `Pooling=false` tipo "Il limite di sessione per il database è XXX ed è stato raggiunto", aggiungere alla stringa di connessione e riprovare.

**Esempio 1: Utilizzare l'autenticazione SQLExample 1: Use SQL authentication**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: Usare l'autenticazione SQL con una password nell'insieme di credenziali delle chiavi di AzureExample 2: Use SQL authentication with a password in Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 3: Utilizzare l'autenticazione di WindowsExample 3: Use Windows authentication**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati di SQL Server.This section provides a list of properties supported by the SQL Server dataset.

Per copiare dati da e in un database di SQL Server, sono supportate le proprietà seguenti:To copy data from and to a SQL Server database, the following properties are supported:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del dataset deve essere impostata su **SqlServerTable**. | Sì |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella/vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella/vista con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Per un nuovo `schema` `table`carico di lavoro, utilizzare e . | No per l'origine, Sì per il sink |

**Esempio**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per definire le attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione viene fornito un elenco delle proprietà supportate dall'origine e dal sink di SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server come origine

Per copiare dati da un database SQL Server, impostare il tipo di origine nell'attività di copia su **SqlSource**. Nella sezione source dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata **su SqlSource**. | Sì |
| sqlReaderQuery |Usare la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Questa proprietà definisce il nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No |
| storedProcedureParameters |Questi parametri sono relativi alla stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole/minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |
| isolationLevel | Specifica il comportamento di blocco delle transazioni per l'origine SQL. I valori consentiti sono: **ReadCommitted** (impostazione predefinita), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Fare riferimento a [questo documento](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) per ulteriori dettagli. | No |

**Punti da notare:**

- Se si specifica **sqlReaderQuery** per **SqlSource**, l'attività di copia esegue la query sull'origine SQL Server per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters**, se la stored procedure accetta parametri.
- Se non si specifica **sqlReaderQuery** o **sqlReaderStoredProcedureName**, le colonne definite nella sezione "structure" del set di dati JSON vengono utilizzate per costruire una query. La `select column1, column2 from mytable` query viene eseguita su SQL Server. Se la definizione del set di dati non include "structure", vengono selezionate tutte le colonne della tabella.

**Esempio: utilizzare una query SQLExample: Use SQL query**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Esempio: utilizzare una stored procedureExample: Use a stored procedure**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Definizione della stored procedure**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server come sink

> [!TIP]
> Per ulteriori informazioni sui comportamenti di scrittura, le configurazioni e le procedure consigliate supportati, vedere Procedure consigliate per il caricamento dei [dati in SQL Server.](#best-practice-for-loading-data-into-sql-server)

Per copiare dati da SQL Server, impostare il tipo di sink nell'attività di copia su **SqlSink**. Nella sezione sink dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **SqlSink**. | Sì |
| writeBatchSize |Numero di righe da inserire nella tabella SQL *per batch.*<br/>I valori consentiti sono integer per il numero di righe. Per impostazione predefinita, Azure Data Factory determina dinamicamente le dimensioni del batch appropriate in base alle dimensioni della riga. |No |
| writeBatchTimeout |Questa proprietà specifica il tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono per l'intervallo di tempo. Un esempio è "00:30:00" per 30 minuti. Se non viene specificato alcun valore, il timeout predefinito è "02:00:00". |No |
| preCopyScript |Questa proprietà specifica una query SQL per l'attività di copia da eseguire prima di scrivere i dati in SQL Server.This property specifies a SQL query for the copy activity to run before writing data into SQL Server. Viene richiamata solo una volta per ogni esecuzione della copia. È possibile usare questa proprietà per pulire i dati precaricati. |No |
| sqlWriterStoredProcedureName | Il nome della stored procedure che definisce come applicare i dati di origine in una tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch*. Per le operazioni che vengono eseguite una sola volta e non hanno `preCopyScript` nulla a che fare con i dati di origine, ad esempio eliminare o troncare, utilizzare la proprietà . | No |
| storedProcedureTableTypeParameterName |Nome del parametro del tipo di tabella specificato nella stored procedure.  |No |
| sqlWriterTableType |Nome del tipo di tabella da utilizzare nella stored procedure. Nel corso dell'attività di copia, i dati spostati vengono resi disponibili in una tabella temporanea di questo tipo. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. |No |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |
| Tableoption | Specifica se creare automaticamente la tabella sink se non esiste in base allo schema di origine. La creazione automatica della tabella non è supportata quando il sink specifica che la stored procedure o la copia in fasi è configurata nell'attività di copia. I valori `none` consentiti `autoCreate`sono: (predefinito), . |No |

**Esempio 1: Aggiungere datiExample 1: Append data**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Esempio 2: Richiamare una stored procedure durante la copiaExample 2: Invoke a stored procedure during copy**

Per altre informazioni, vedere [Richiamare una stored procedure da un sink SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Procedura consigliata per il caricamento di dati in SQL ServerBest practice for loading data into SQL Server

Quando si copiano dati in SQL ServerSQL Server, potrebbe essere necessario un comportamento di scrittura diverso:When you copy data into SQL ServerSQL Server, you might require different write behavior:

- [Append](#append-data): I dati di origine conpossonore solo nuovi record.
- [Upsert](#upsert-data): I miei dati di origine hanno sia inserti che aggiornamenti.
- [Sovrascrivi](#overwrite-the-entire-table): Voglio ricaricare ogni volta l'intera tabella delle dimensioni.
- [Scrivere con logica personalizzata:](#write-data-with-custom-logic)è necessaria un'ulteriore elaborazione prima dell'inserimento finale nella tabella di destinazione.

Vedere le rispettive sezioni per informazioni su come configurare Azure Data Factory e le procedure consigliate.

### <a name="append-data"></a>Accodare dati

L'aggiunta di dati è il comportamento predefinito di questo connettore sink di SQL Server.Appending data is the default behavior of this SQL Server sink connector. Azure Data Factory esegue un inserimento bulk per scrivere nella tabella in modo efficiente. È possibile configurare l'origine e il sink di conseguenza nell'attività di copia.

### <a name="upsert-data"></a>Eseguire l'upsert dei dati

**Opzione 1:** Quando si dispone di una grande quantità di dati da copiare, utilizzare il seguente approccio per eseguire un upsert: 

- In primo luogo, utilizzare una [tabella temporanea](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) per eseguire il caricamento bulk di tutti i record utilizzando l'attività di copia. Poiché le operazioni su tabelle temporanee non vengono registrate, è possibile caricare milioni di record in pochi secondi.
- Eseguire un'attività di stored procedure in Azure Data Factory per applicare un'istruzione [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) o INSERT/UPDATE. Utilizzare la tabella temporanea come origine per eseguire tutti gli aggiornamenti o gli inserimenti come singola transazione. In questo modo, il numero di round trip e operazioni di log viene ridotto. Al termine dell'attività della stored procedure, la tabella temporanea può essere troncata per essere pronta per il ciclo di ripresa successivo.

Ad esempio, in Azure Data Factory è possibile creare una pipeline con **un'attività di copia** concatenata a un'attività Stored **procedure**. Precedentemente copia i dati dall'archivio di origine in una tabella temporanea del database, ad **esempio, #UpsertTempTable**, come nome della tabella nel set di dati. Quindi quest'ultima richiama una stored procedure per unire i dati di origine dalla tabella temporanea nella tabella di destinazione e pulire la tabella temporanea.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Nel database definire una stored procedure con logica MERGE, come nell'esempio seguente, a cui si fa riferimento dall'attività precedente della stored procedure. Si supponga che la destinazione sia la tabella **Marketing** con tre colonne: **ProfileID**, **State**e **Category**. Eseguire l'upsert in base alla colonna **ProfileID.**

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opzione 2:** È inoltre possibile scegliere di [richiamare una stored procedure all'interno dell'attività](#invoke-a-stored-procedure-from-a-sql-sink)di copia. Questo approccio esegue ogni riga nella tabella di origine anziché usare l'inserimento bulk come approccio predefinito nell'attività di copia, che non è appropriato per l'upsert di grandi dimensioni.

### <a name="overwrite-the-entire-table"></a>Sovrascrivere l'intera tabella

È possibile configurare la proprietà **preCopyScript** in un sink di attività di copia. In questo caso, per ogni attività di copia eseguita, Azure Data Factory esegue prima lo script. Quindi esegue la copia per inserire i dati. Ad esempio, per sovrascrivere l'intera tabella con i dati più recenti, specificare uno script per eliminare tutti i record prima di caricare in blocco i nuovi dati dall'origine.

### <a name="write-data-with-custom-logic"></a>Scrivere dati con logica personalizzataWrite data with custom logic

I passaggi per scrivere dati con logica personalizzata sono simili a quelli descritti nella sezione Dati [Upsert.](#upsert-data) Quando è necessario applicare l'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione, per la scalabilità di grandi dimensioni, è possibile eseguire una delle due operazioni seguenti:When you need to apply extra processing before the final insertion of source data into the destination table, for large scale, you can do one of two things: 

- Caricare in una tabella temporanea e quindi richiamare una stored procedure. 
- Richiamare una stored procedure durante la copia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Richiamare una stored procedure da un sink SQL

Quando si copiano dati in un database di SQL ServerSQL Server , è anche possibile configurare e richiamare una stored procedure specificata dall'utente con parametri aggiuntivi. La funzionalità della stored procedure sfrutta i parametri con valori di [tabella.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> La chiamata di una stored procedure elabora i dati riga per riga anziché utilizzando un'operazione in blocco, che non è consigliabile per la copia su larga scala. Per ulteriori informazioni, vedere [Procedura consigliata per il caricamento dei dati in SQL Server](#best-practice-for-loading-data-into-sql-server).

È possibile usare una stored procedure quando non si possono usare i meccanismi di copia predefiniti. Un esempio è quando si desidera applicare un'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di elaborazione aggiuntivi sono quando si desidera unire le colonne, cercare valori aggiuntivi e inserire dati in più di una tabella.

Nell'esempio seguente viene illustrato come usare una stored procedure per eseguire un'operazione upsert in una tabella del database SQL Server. Si supponga che i dati di input e la tabella **marketing** sink dispongano ognuna di tre colonne: **ProfileID**, **State**e **Category**. Eseguire l'upsert in base alla colonna **ProfileID** e applicarla solo per una categoria specifica denominata "ProductA".

1. Nel database definire il tipo di tabella con lo stesso nome di **sqlWriterTableType**. Lo schema del tipo di tabella è identico allo schema restituito dai dati di input.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Nel database definire la stored procedure con lo stesso nome **di sqlWriterStoredProcedureName**. che gestisce i dati di input dell'origine specificata e li unisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure è lo stesso **tableName** definito nel set di dati.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. In Azure Data Factory definire la sezione **del sink SQL** nell'attività di copia come segue:In Azure Data Factory, define the SQL sink section in the copy activity as follows:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mapping dei tipi di dati per SQL ServerData type mapping for SQL Server

Quando si copiano dati da e in SQL ServerSQL Server, i mapping seguenti vengono usati dai tipi di dati di SQL ServerSQL Server ai tipi di dati provvisori di Azure Data Factory.When you copy data from and to SQL ServerSQL Server, the following mappings are used from SQL Serversql Server data types to Azure Data Factory interim data types. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di SQL Server | Tipo di dati provvisorio di Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| Data |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Oggetto |
| text |String, Char[] |
| time |TimeSpan |
|  timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Per i tipi di dati associati al tipo provvisorio Decimal, Azure Data Factory supporta attualmente la precisione fino a 28. Se si hanno dati che richiedono una precisione maggiore di 28, è consigliabile convertirli in una stringa in una query SQL.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare [Attività GetMetadataTo](control-flow-get-metadata-activity.md) learn details about the properties, check GetMetadata activity 

## <a name="troubleshoot-connection-issues"></a>Risolvere i problemi di connessione

1. Configurare l'istanza di SQL Server per accettare le connessioni remote. Avviare **SQL Server Management Studio**, fare clic con il pulsante destro del mouse sul **server**e scegliere **Proprietà**. Selezionare **Connessioni** dall'elenco e selezionare la casella di controllo **Consenti connessioni remote al server.**

    ![Abilitare le connessioni remote](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Per la procedura dettagliata, vedere [Configurare l'opzione](https://msdn.microsoft.com/library/ms191464.aspx)di configurazione del server di accesso remoto .

2. Avviare **Gestione configurazione SQL Server**. Espandere **Configurazione di rete SQL Server** per l'istanza prevista e selezionare **Protocolli per MSSQLSERVER**. I protocolli vengono visualizzati nel riquadro di destra. Abilitare TCP/IP facendo clic con il pulsante destro del mouse su **TCP/IP** e selezionando **Abilita**.

    ![Abilitare TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Per ulteriori informazioni e modalità alternative di abilitazione del protocollo TCP/IP, vedere [Abilitare o disabilitare un protocollo](https://msdn.microsoft.com/library/ms191294.aspx)di rete del server .

3. Nella stessa finestra, fare doppio clic su **TCP/IP** per avviare la finestra **Proprietà TCP/IP.**
4. Passare alla scheda **Indirizzi IP.** Scorrere verso il basso per visualizzare la sezione **IPAll.** Annotare la **porta TCP**. Il valore predefinito è **1433**.
5. Creare una **regola per Windows Firewall** nel computer per consentire il traffico in ingresso attraverso questa porta. 
6. **Verificare la connessione**: Per connettersi a SQL Server utilizzando un nome completo, utilizzare SQL Server Management Studio da un computer diverso. Un esempio è `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)
