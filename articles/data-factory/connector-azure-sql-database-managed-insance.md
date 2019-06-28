---
title: Copiare dati da e verso l'Istanza gestita di database SQL di Azure con Azure Data Factory | Microsoft Docs
description: Informazioni su come spostare i dati da e verso l'Istanza gestita di database SQL di Azure con Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: 3e1978c761c365125ac94a1ecbef5f9ac7375eba
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338610"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copiare dati da e verso l'Istanza gestita di database SQL di Azure con Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e verso l'Istanza gestita di database SQL di Azure. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati dall'Istanza gestita di database SQL di Azure a qualsiasi archivio dati sink supportato. È anche possibile copiare dati da qualsiasi archivio dati di origine supportato all'istanza gestita. Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare il connettore dell'Istanza gestita di database SQL di Azure supporta:

- Copia dei dati tramite l'autenticazione di SQL.
- Come origine, il recupero dei dati usando una query SQL o una stored procedure.
- Come sink, l'accodamento di dati a una tabella di destinazione o la chiamata a una stored procedure con logica personalizzata durante la copia.

>[!NOTE]
>Istanza gestita del Database SQL Azure [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) non è attualmente supportata da questo connettore. Soluzione alternativa, è possibile usare una [connettore ODBC generico](connector-odbc.md) e un driver ODBC di SQL Server tramite un runtime di integrazione self-hosted. Seguire [questo materiale sussidiario](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) con configurazioni stringa download e connessione ODBC driver.

>[!NOTE]
>Autenticazioni identità gestita e dell'entità servizio non sono attualmente supportate da questo connettore. Soluzione alternativa, scegliere un connettore Database SQL di Azure e specificare manualmente il server dell'istanza gestita.

## <a name="prerequisites"></a>Prerequisiti

Per accedere a istanza gestita di Azure SQL Database [endpoint pubblico](../sql-database/sql-database-managed-instance-public-endpoint-securely.md), è possibile usare un runtime di integrazione di Azure gestita di Azure Data Factory. Assicurarsi di abilitare l'endpoint pubblico e anche consentire il traffico di endpoint pubblica nel gruppo di sicurezza di rete in modo che Azure Data Factory possa connettersi al database. Per altre informazioni, vedere [questo materiale sussidiario](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Per accedere all'endpoint privato istanza gestita di Azure SQL Database, configurare un [runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md) che possa accedere al database. Se si esegue il provisioning di runtime di integrazione self-hosted nella stessa rete virtuale dell'istanza gestita, assicurarsi che il computer di runtime di integrazione sia in una subnet diversa da istanza gestita. Se si effettua il provisioning del runtime di integrazione self-hosted in una rete virtuale diversa rispetto a istanza gestita, è possibile utilizzare un peering reti virtuali o una rete virtuale a rete virtuale. Per altre informazioni, vedere [Connettere un'applicazione a Istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Azure Data Factory specifiche per il connettore di istanza gestita di Azure SQL Database.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato dell'Istanza gestita di database SQL di Azure sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **SqlServer**. | Yes |
| connectionString |Questa proprietà specifica la **connectionString** informazioni che ti serve per connettersi all'istanza gestita usando l'autenticazione di SQL. Per altre informazioni, vedere gli esempi seguenti. <br/>Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Azure Data Factory. È anche possibile inserire una password in Azure Key Vault. Se si tratta di autenticazione di SQL Server, eseguire il pull di `password` configurazione all'esterno della stringa di connessione. Per altre informazioni, vedere l'esempio JSON seguente nella tabella e [Store le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | Questo [runtime di integrazione](concepts-integration-runtime.md) viene usato per connettersi all'archivio dati. Se l'istanza gestita ha un endpoint pubblico e consente a diritti di accesso di Azure Data Factory, è possibile utilizzare un runtime di integrazione self-hosted o un runtime di integrazione di Azure. Se non specificato, viene utilizzato il runtime di integrazione di Azure predefinito. |Yes |

**Esempio 1: Usare l'autenticazione di SQL**

La porta predefinita è 1433. Se si usa l'istanza gestita di Azure SQL Database con un endpoint pubblico, è necessario specificare esplicitamente la porta 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: Usa autenticazione di SQL con una password in Azure Key Vault**

La porta predefinita è 1433. Se si usa l'istanza gestita di Azure SQL Database con un endpoint pubblico, è necessario specificare esplicitamente la porta 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
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

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per definire set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati dell'Istanza gestita di database SQL di Azure.

Per copiare dati da e verso l'istanza gestita di Azure SQL Database, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **SqlServerTable**. | Yes |
| tableName |Questa proprietà definisce il nome della tabella o della vista nell'istanza di database a cui fa riferimento il servizio collegato. | No per l'origine, Sì per il sink |

**Esempio**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per definire le attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink dell'Istanza gestita di database SQL di Azure.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Istanza gestita di database SQL di Azure come origine

Per copiare i dati dall'Istanza gestita di database SQL di Azure, impostare il tipo di origine nell'attività di copia su **SqlSource**. Nella sezione source dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **SqlSource**. | Yes |
| sqlReaderQuery |Questa proprietà usa la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Questa proprietà definisce il nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No |
| storedProcedureParameters |Questi parametri sono relativi alla stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e l'uso di maiuscole e minuscole dei parametri devono corrispondere a quelli dei parametri della stored procedure. |No |

**Tenere presente quanto segue:**

- Se per **SqlSource** è specificata la proprietà **sqlReaderQuery**, l'attività di copia esegue questa query sull'origine dell'istanza gestita per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters**, se la stored procedure accetta parametri.
- Se non si specifica la proprietà **sqlReaderQuery** o **sqlReaderStoredProcedureName**, per creare una query vengono usate le colonne definite nella sezione "structure" del codice JSON del set di dati. La query `select column1, column2 from mytable` viene eseguita sull'istanza gestita. Se la definizione del set di dati non include "structure", vengono selezionate tutte le colonne della tabella.

**Esempio: Usare una query SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

**Esempio: Usare una stored procedure**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Istanza gestita di database SQL di Azure come sink

> [!TIP]
> Altre informazioni su comportamenti di scrittura supportati, configurazioni e le procedure consigliate dagli [procedure consigliate per il caricamento dei dati in istanza gestita di Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Per copiare i dati da un'Istanza gestita di database SQL di Azure, impostare il tipo di sink nell'attività di copia su **SqlSink**. Nella sezione sink dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **SqlSink**. | Yes |
| writeBatchSize |Numero di righe da inserire nella tabella SQL *per ogni batch*.<br/>I valori consentiti sono integer per il numero di righe. Per impostazione predefinita, Azure Data Factory determina in modo dinamico le dimensioni del batch appropriato in base alla dimensione di riga.  |No |
| writeBatchTimeout |Questa proprietà specifica il tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono per l'intervallo di tempo. Ad esempio, "00:30:00" corrisponde a 30 minuti. |No |
| preCopyScript |Questa proprietà specifica una query SQL per l'attività di copia da eseguire prima di scrivere dati in istanza gestita. Viene richiamata solo una volta per ogni esecuzione della copia. È possibile usare questa proprietà per pulire i dati precaricati. |No |
| sqlWriterStoredProcedureName |Questo è il nome della stored procedure che definisce come applicare i dati di origine nella tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch*. Per un'operazione che viene eseguita una sola volta e non ha nulla a che fare con i dati di origine, ad esempio un'eliminazione o un troncamento, usare la proprietà `preCopyScript`. |No |
| storedProcedureParameters |Questi parametri vengono usati per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e l'uso di maiuscole e minuscole dei parametri devono corrispondere a quelli dei parametri della stored procedure. |No |
| sqlWriterTableType |Questa proprietà specifica il nome di un tipo di tabella da usare nella stored procedure. Nel corso dell'attività di copia, i dati spostati vengono resi disponibili in una tabella temporanea di questo tipo. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. |No |

**Esempio 1: Accodare dati**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Esempio 2: Richiamare una stored procedure durante la copia**

Per altre informazioni, vedere [Richiamare una stored procedure da un sink SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Procedure consigliate per il caricamento dei dati in istanza gestita di Azure SQL Database

Quando si copiano dati in istanza gestita di Azure SQL Database, si potrebbe richiedere diverso comportamento:

- [Accodare](#append-data): I dati di origine contengono solo i nuovi record.
- [Upsert](#upsert-data): I dati di origine contengono sia istruzioni inserts e updates.
- [Sovrascrivere](#overwrite-the-entire-table): Si vuole ricaricare ogni volta che la tabella di tutta la dimensione.
- [Scrivere con logica personalizzata](#write-data-with-custom-logic): Ho bisogno di ulteriore elaborazione prima dell'inserimento finale nella tabella di destinazione. 

Vedere le sezioni per la configurazione in Azure Data Factory e le procedure consigliate.

### <a name="append-data"></a>Aggiungere i dati

Aggiunta di dati è il comportamento predefinito di questo connettore di sink di istanza gestita di Azure SQL Database. Azure Data Factory esegue un inserimento bulk per scrivere in modo efficiente alla tabella. È possibile configurare l'origine e di conseguenza di sink nell'attività di copia.

### <a name="upsert-data"></a>Eseguire l'upsert dei dati

**Opzione 1:** Se si ha una grande quantità di dati da copiare, utilizzare l'approccio seguente per eseguire un'operazione di upsert: 

- In primo luogo, utilizzare un [tabella temporanea](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) per il caricamento bulk, tutti i record usando l'attività di copia. Poiché le operazioni su tabelle temporanee non sono connessi, è possibile caricare milioni di record in pochi secondi.
- Eseguire un'attività stored procedure in Azure Data Factory per applicare una [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) o un'istruzione di inserimento/aggiornamento. Usare la tabella temporanea come origine per eseguire tutte le aggiorna o inserisce una singola transazione. In questo modo, viene ridotto il numero di round trip e operazioni del log. Al termine dell'attività stored procedure, la tabella temporanea può essere troncata per la preparazione per il successivo ciclo di upsert.

Ad esempio, in Azure Data Factory, è possibile creare una pipeline con un' **attività di copia** concatenato con un **attività di Stored Procedure**. La prima copia i dati dall'archivio di origine in una tabella temporanea, ad esempio, **##UpsertTempTable**, come il nome della tabella nel set di dati. Quest'ultimo richiama quindi una stored procedure per unire i dati di origine dalla stessa tabella nella tabella di destinazione e pulire la tabella temporanea.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Nel database, definire una stored procedure con logica di unione, come nell'esempio seguente, che viene fatto riferimento dall'attività di stored procedure precedente. Si supponga che la destinazione è il **Marketing** tabella con tre colonne: **ProfileID**, **State** e **Category**. Eseguire l'operazione upsert in base il **ProfileID** colonna.

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

**Opzione 2:** È anche possibile effettuare [richiamare una stored procedure all'interno di un'attività di copia](#invoke-a-stored-procedure-from-a-sql-sink). Questo approccio viene eseguito ogni riga nella tabella di origine invece di uso di bulk insert come l'approccio predefinito nell'attività di copia, non è appropriato per l'operazione upsert su larga scala.

### <a name="overwrite-the-entire-table"></a>Sovrascrivere l'intera tabella

È possibile configurare il **preCopyScript** proprietà in un sink dell'attività copia. In questo caso, per ogni attività di copia che viene eseguito, Azure Data Factory esegue lo script prima di tutto. Viene quindi eseguita la copia per inserire i dati. Ad esempio, per sovrascrivere l'intera tabella con i dati più recenti, specificare uno script per eliminare innanzitutto tutti i record prima bulk caricare nuovi dati dall'origine.

### <a name="write-data-with-custom-logic"></a>Scrivere i dati con logica personalizzata

I passaggi per scrivere i dati con logica personalizzata sono simili a quelli descritti nel [Upsert dati](#upsert-data) sezione. Quando è necessario applicare aggiuntivi di elaborazione prima dell'inserimento finale dei dati di origine nella tabella di destinazione, per la scalabilità di grandi dimensioni, è possibile eseguire una delle seguenti operazioni: 

- Carica in una tabella temporanea e quindi richiamare una stored procedure.
- Richiamare una stored procedure durante la copia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Richiamare una stored procedure da un sink SQL

Quando si copiano dati in istanza gestita di Azure SQL Database, è anche possibile configurare e richiamare una specificato dall'utente stored procedure con parametri aggiuntivi.

> [!TIP]
> Richiamare una stored procedure elabora i dati riga per riga, anziché mediante un'operazione bulk, che non è consigliabile per la copia su larga scala. Altre informazioni, vedere [procedure consigliate per il caricamento dei dati in istanza gestita di Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

È possibile usare una stored procedure quando non si possono usare i meccanismi di copia predefiniti. Un esempio è quando si desidera applicare ulteriori operazioni di elaborazione prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di operazioni di elaborazione aggiuntive sono quando si desidera unire le colonne, cercare valori aggiuntivi e inserire dati in più di una tabella.

Nell'esempio seguente viene illustrato come usare una stored procedure per eseguire un'operazione upsert in una tabella del database SQL Server. Si supponga che i dati di input e il sink **Marketing** ogni tabella dispone di tre colonne: **ProfileID**, **State** e **Category**. Eseguire l'operazione di upsert nella colonna **ProfileID** e applicarla solo a una categoria specifica.

**Set di dati di output:** Il valore "tableName" è lo stesso nome parametro di tipo tabella della stored procedure, come illustrato nello script di stored procedure seguente:

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definire le **sink SQL** sezione nell'attività di copia come indicato di seguito:

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Nel database definire la stored procedure con lo stesso nome di **SqlWriterStoredProcedureName**. che gestisce i dati di input dell'origine specificata e li unisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure è identico **tableName** definiti nel set di dati.

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

Nel database definire il tipo di tabella con lo stesso nome **sqlWriterTableType**. Lo schema del tipo di tabella è identico allo schema restituito dai dati di input.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

La funzionalità di stored procedure sfrutta i [parametri valutati a livello di tabella](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapping dei tipi di dati per l'Istanza gestita di database SQL di Azure

Quando si copiano dati da o verso l'Istanza gestita di database SQL di Azure, vengono usati i mapping seguenti tra i tipi di dati dell'Istanza gestita di database SQL di Azure e i tipi di dati provvisori di Azure Data Factory. Per informazioni su come l'attività di copia esegue il mapping dello schema e del tipo di dati di origine al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipi di dati dell'Istanza gestita di database SQL di Azure | Tipo di dati provvisorio di Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Per i tipi di dati associati al tipo provvisorio Decimal, Azure Data Factory supporta attualmente la precisione fino a 28. Se si hanno dati che richiedono una precisione maggiore di 28, è consigliabile convertirli in una stringa in una query SQL.

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
