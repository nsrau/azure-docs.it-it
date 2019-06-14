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
ms.openlocfilehash: e68b522d5a0fe7c359d83fc436aa7a1fd2159198
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048581"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copiare dati da e verso l'Istanza gestita di database SQL di Azure con Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e verso l'Istanza gestita di database SQL di Azure. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati dall'Istanza gestita di database SQL di Azure a qualsiasi archivio dati sink supportato. È anche possibile copiare dati da qualsiasi archivio dati di origine supportato all'istanza gestita. Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare il connettore dell'Istanza gestita di database SQL di Azure supporta:

- Copia dei dati tramite l'autenticazione di SQL.
- Come origine, il recupero di dati tramite una query SQL o una stored procedure.
- Come sink, l'accodamento di dati a una tabella di destinazione o la chiamata a una stored procedure con logica personalizzata durante la copia.

La funzionalità [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) di SQL Server non è attualmente supportata. 

## <a name="prerequisites"></a>Prerequisiti

Per accedere a istanza gestita di Azure SQL Database  **[endpoint pubblico](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)** , è possibile usare Azure Data factory gestito Azure runtime di integrazione. Assicurarsi che non solo consentono all'endpoint pubblico, ma anche consentire il traffico di endpoint pubblica nel gruppo di sicurezza di rete per rendere Azure Data factory di connettersi al database, seguendo [questo materiale sussidiario](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Per accedere a istanza gestita di Azure SQL Database **endpoint privato**, configurare un [runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md) che possa accedere al database. Se si esegue il provisioning di runtime di integrazione self-hosted nella stessa rete virtuale dell'istanza gestita, assicurarsi che il computer di runtime di integrazione sia in una subnet diversa da istanza gestita. Se si effettua il provisioning del runtime di integrazione self-hosted in una rete virtuale diversa da quella dell'istanza gestita, è consigliabile usare un peering di rete virtuale o stabilire una connessione tra reti virtuali. Per altre informazioni, vedere [Connettere un'applicazione a Istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche in un connettore di Istanza gestita di database SQL di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato dell'Istanza gestita di database SQL di Azure sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **SqlServer**. | Sì. |
| connectionString |Questa proprietà specifica le informazioni di connectionString che ti serve per connettersi all'istanza gestita usando l'autenticazione di SQL. Per altre informazioni, vedere gli esempi seguenti. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password in Azure Key Vault e, se si tratta dell'autenticazione SQL, eseguire il pull della configurazione `password` dalla stringa di connessione. Vedere gli esempi JSON sotto la tabella e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. |Sì. |
| connectVia | Questo [runtime di integrazione](concepts-integration-runtime.md) viene usato per connettersi all'archivio dati. È possibile usare il Runtime di integrazione Self-Hosted o il Runtime di integrazione di Azure (se l'istanza gestita ha endpoint pubblici e consentire ADF accedere). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |Sì. |

**Esempio 1: Usare l'autenticazione di SQL**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername:port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: Usare l'autenticazione di SQL con la password in Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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
| type | La proprietà type del set di dati deve essere impostata su **SqlServerTable**. | Sì. |
| tableName |Questa proprietà definisce il nome della tabella o della vista nell'istanza di database a cui fa riferimento il servizio collegato. | No per l'origine. Sì per il sink. |

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
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **SqlSource**. | Sì. |
| sqlReaderQuery |Questa proprietà usa la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. |No. |
| sqlReaderStoredProcedureName |Questa proprietà definisce il nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No. |
| storedProcedureParameters |Questi parametri sono relativi alla stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e l'uso di maiuscole e minuscole dei parametri devono corrispondere a quelli dei parametri della stored procedure. |No. |

Tenere presente quanto segue:

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
> Altre informazioni sui comportamenti di scrittura supportati, configurazioni e consigliata da [procedure consigliate per il caricamento dei dati in istanza gestita di Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Per copiare i dati da un'Istanza gestita di database SQL di Azure, impostare il tipo di sink nell'attività di copia su **SqlSink**. Nella sezione sink dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **SqlSink**. | Sì. |
| writeBatchSize |Numero di righe nella tabella SQL inserimenti **per ogni batch**.<br/>I valori consentiti sono integer per il numero di righe. Per impostazione predefinita, Data Factory di determinare in modo dinamico le dimensioni del batch appropriato in base alla dimensione di riga.  |No |
| writeBatchTimeout |Questa proprietà specifica il tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono relativi all'intervallo di tempo. Ad esempio, "00:30:00" corrisponde a 30 minuti. |No. |
| preCopyScript |Questa proprietà specifica una query SQL per l'attività di copia da eseguire prima di scrivere i dati nell'istanza gestita. Viene richiamata solo una volta per ogni esecuzione della copia. È possibile usare questa proprietà per pulire i dati precaricati. |No. |
| sqlWriterStoredProcedureName |Questo è il nome della stored procedure che definisce come applicare i dati di origine nella tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch*. Per un'operazione che viene eseguita una sola volta e non ha nulla a che fare con i dati di origine, ad esempio un'eliminazione o un troncamento, usare la proprietà `preCopyScript`. |No. |
| storedProcedureParameters |Questi parametri vengono usati per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e l'uso di maiuscole e minuscole dei parametri devono corrispondere a quelli dei parametri della stored procedure. |No. |
| sqlWriterTableType |Questa proprietà specifica il nome di un tipo di tabella da usare nella stored procedure. Nel corso dell'attività di copia, i dati spostati vengono resi disponibili in una tabella temporanea di questo tipo. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. |No. |

**Esempio 1: aggiungere dati**

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

**Esempio 2: richiamare una stored procedure durante la copia**

Per altre informazioni, vedere [Richiamare una stored procedure da un sink SQL](#invoking-stored-procedure-for-sql-sink).

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

Quando si copiano dati in istanza gestita di Azure SQL Database, potrebbe essere necessario il comportamento di scrittura diverse:

- **[Accodare](#append-data)** : i dati di origine contengono solo nuovi record;
- **[Upsert](#upsert-data)** : i dati di origine contengono sia istruzioni inserts e updates;
- **[Sovrascrivere](#overwrite-entire-table)** : Desidera ricaricare tabella intera dimensione ogni volta che;
- **[Scrivere con logica personalizzata](#write-data-with-custom-logic)** : Ho bisogno di ulteriore elaborazione prima dell'inserimento finale nella tabella di destinazione.

Vedere la sezioni rispettivamente su come configurare in Azure Data factory e le procedure consigliate.

### <a name="append-data"></a>Aggiungere i dati

Si tratta del comportamento predefinito di questo connettore di sink di istanza gestita di Azure SQL Database e si di ADF **bulk insert** per scrivere in modo efficiente alla tabella. È possibile semplicemente configurare l'origine e di conseguenza di sink nell'attività di copia.

### <a name="upsert-data"></a>Eseguire l'upsert dei dati

**Opzione ho** (consigliato soprattutto quando ci sono dati di grandi dimensioni da copiare): il **approccio ad alte prestazioni la maggior parte delle** eseguire upsert è il seguente: 

- In primo luogo, utilizzare un [tabella temporanea](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) per eseguire il caricamento bulk tutti i record usando l'attività di copia. Come operazioni su tabelle temporanee non sono connessi, è possibile caricare milioni di record in pochi secondi.
- Eseguire un'attività Stored Procedure in Azure Data factory per applicare una [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (o inserimento/aggiornamento) istruzione e utilizzare temporanea di tabella come origine per eseguire tutte le aggiorna o inserisce come una singola transazione, riducendo la quantità di round trip e operazioni di log. Al termine dell'attività Stored Procedure, tabelle temporanee possono essere troncate per la preparazione per il successivo ciclo di upsert. 

Ad esempio, in Azure Data Factory, è possibile creare una pipeline con un' **attività di copia** concatenato con un **attività Stored Procedure** esito è positivo. La prima copia i dati dall'archivio di origine in una tabella temporanea, ad esempio " **##UpsertTempTable**" come nome della tabella nel set di dati, quindi quest'ultimo richiama una Stored Procedure per unire i dati di origine dalla stessa tabella in tabella di destinazione e la pulizia tabella temporanea.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Nel database, definire una Stored Procedure con logica di unione, simile alla seguente, che viene fatto riferimento dall'attività di Stored Procedure precedente. Supponendo che di destinazione **Marketing** tabella con tre colonne: **ProfileID**, **stato**, e **categoria**, e di eseguire l'operazione upsert in base il **ProfileID** colonna.

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

**Opzione II:** in alternativa, è possibile effettuare [chiamare una stored procedure all'interno di attività di copia](#invoking-stored-procedure-for-sql-sink), durante la nota di questo approccio viene eseguito per ogni riga nella tabella di origine invece di uso di bulk insert come l'approccio predefinito Nell'attività di copia, pertanto non può essere inclusa per l'operazione upsert su larga scala.

### <a name="overwrite-entire-table"></a>Sovrascrivere l'intera tabella

È possibile configurare **preCopyScript** sink proprietà nell'attività di copia, nel qual caso per ogni esecuzione attività di copia, Azure Data Factory esegue lo script prima di tutto, quindi eseguire la copia per inserire i dati. Ad esempio, per sovrascrivere l'intera tabella con i dati più recenti, è possibile specificare uno script per eliminare tutti i record prima del caricamento bulk dei nuovi dati dall'origine.

### <a name="write-data-with-custom-logic"></a>Scrivere i dati con logica personalizzata

Analogamente a come descritto in [Upsert dati](#upsert-data) sezione quando è necessario applicare ulteriori operazioni di elaborazione prima dell'inserimento finale dei dati di origine nella tabella di destinazione, è possibile un) su larga scala, caricare in una tabella temporanea e richiamare una stored procedure, o b) richiamare una stored procedure durante la copia.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Richiamare una stored procedure da un sink SQL

Quando si copiano dati in istanza gestita di Azure SQL Database, è anche possibile configurare e richiamare una specificato dall'utente stored procedure con parametri aggiuntivi.

> [!TIP]
> Richiamo delle stored procedure elabora i dati row-by-row anziché operazione bulk, che non è consigliata per la copia su larga scala. Altre informazioni, vedere [procedure consigliate per il caricamento dei dati in istanza gestita di Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

È possibile usare una stored procedure quando meccanismi di copia predefiniti non svolgono la funzione, ad esempio, applicare ulteriori operazioni di elaborazione prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di elaborazione extra sono l'unione di colonne, la ricerca di altri valori e l'inserimento in più di una tabella.

Nell'esempio seguente viene illustrato come usare una stored procedure per eseguire un'operazione upsert in una tabella del database SQL Server. Si presuppone che i dati di input e la tabella **Marketing** del sink abbiano tre colonne: **ProfileID**, **State** e **Category**. Eseguire l'operazione di upsert nella colonna **ProfileID** e applicarla solo a una categoria specifica.

**Set di dati di output:** "tableName" deve essere lo stesso nome parametro di tipo tabella della stored procedure (vedere di seguito script di stored procedure).

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

Definire le **sink SQL** sezione nell'attività di copia come indicato di seguito.

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

Nel database definire la stored procedure con lo stesso nome di **SqlWriterStoredProcedureName**, che gestisce i dati di input dell'origine specificata e li unisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure deve essere identico al valore **tableName** definito nel set di dati.

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

Nel database definire il tipo di tabella con lo stesso nome di sqlWriterTableType. Lo schema del tipo di tabella è identico allo schema restituito dai dati di input.

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
