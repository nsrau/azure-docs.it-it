---
title: Copiare dati da o nell'Istanza gestita di database SQL di Azure usando Azure Data Factory | Microsoft Docs
description: Informazioni su come spostare i dati da e verso l'Istanza gestita di database SQL di Azure mediante Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: jingwang
ms.openlocfilehash: df8d337e7950400a86dcab14de4484f4811f43e2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025080"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-using-azure-data-factory"></a>Copiare dati da o nell'Istanza gestita di database SQL di Azure usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e verso un'Istanza gestita di database SQL di Azure. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare i dati da o verso un'Istanza gestita di database SQL di Azure in qualsiasi archivio dati sink supportato oppure copiare i dati da un archivio dati di origine supportato in un'istanza gestita. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare il connettore dell'Istanza gestita di database SQL di Azure supporta:

- La copia dei dati usando l'autenticazione **SQL** o **Windows**.
- Come origine, il recupero di dati tramite query SQL o stored procedure.
- Come sink, l'aggiunta di dati alla tabella di destinazione o la chiamata a una stored procedure con logica personalizzata durante la copia.

## <a name="prerequisites"></a>Prerequisiti

Per copiare i dati da un'Istanza gestita di database SQL di Azure che si trova nella rete virtuale, è necessario configurare un runtime di integrazione self-hosted nella stessa rete virtuale che possa accedere al database. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di data factory specifiche in un connettore dell'Istanza gestita di database SQL di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato dell'Istanza gestita di database SQL di Azure sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SqlServer** | Yes |
| connectionString |Specificare le informazioni di connectionString necessarie per connettersi all'istanza gestita usando l'autenticazione di SQL o di Windows. Vedere l'esempio seguente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| userName |Specificare il nome utente se si usa l'autenticazione Windows. Esempio: **nomedominio\\nomeutente**. |No  |
| password |Specificare la password per l'account utente specificato per userName. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |No  |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Eseguire il provisioning del runtime di integrazione self-hosted nella stessa rete virtuale dell'Istanza gestita. |Yes |

>[!TIP]
>Se viene restituito l'errore con codice "UserErrorFailedToConnectToSqlServer" e un messaggio quale "Il limite di sessioni per il database è XXX ed è stato raggiunto.", aggiungere `Pooling=false` alla stringa di connessione e riprovare.

**Esempio 1: Uso dell'autenticazione SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: Uso dell'autenticazione di Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati dell'Istanza gestita di database SQL di Azure.

Per copiare dati da/verso l'Istanza gestita di database SQL di Azure, impostare la proprietà type del set di dati su **SqlServerTable**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **SqlServerTable** | Yes |
| tableName |Nome della tabella o vista nell'istanza di database a cui fa riferimento il servizio collegato. | No per l'origine, Sì per il sink |

**Esempio**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink dell'Istanza gestita di database SQL di Azure.

### <a name="azure-sql-database-managed-instance-as-source"></a>Istanza gestita di database SQL di Azure come origine

Per copiare i dati dall'Istanza gestita di database SQL di Azure, impostare il tipo di origine nell'attività di copia su **SqlSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **SqlSource** | Yes |
| SqlReaderQuery |Usare la query SQL personalizzata per leggere i dati. Esempio: `select * from MyTable`. |No  |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No  |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono: coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No  |

**Punti da evidenziare**

- Se per SqlSource è specificata la proprietà **sqlReaderQuery**, l'attività di copia esegue questa query nell'origine dell'Istanza gestita per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).
- Se non si specifica la proprietà "sqlReaderQuery" né "sqlReaderStoredProcedureName", le colonne definite nella sezione "struttura" del set di dati JSON vengono usate per creare una query, `select column1, column2 from mytable`, da eseguire nell'Istanza gestita. Se nella definizione del set di dati non è inclusa la "struttura", vengono selezionate tutte le colonne della tabella.

**Esempio: Uso di una query SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
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

**Esempio: Uso di una stored procedure**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
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

### <a name="azure-sql-database-managed-instance-as-sink"></a>Istanza gestita di database SQL di Azure come sink

Per copiare i dati da un'Istanza gestita di database SQL di Azure, impostare il tipo di sink nell'attività di copia su **SqlSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su: **SqlSink** | Yes |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize.<br/>I valori consentiti sono integer, ovvero il numero di righe. |No (valore predefinito: 10.000) |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono: intervallo di tempo. Esempio: "00:30:00" (30 minuti). |No  |
| preCopyScript |Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nell'istanza gestita. Può essere richiamata solo una volta per esecuzione della copia. È possibile usare questa proprietà per pulire i dati precaricati. |No  |
| sqlWriterStoredProcedureName |Nome della stored procedure che definisce come applicare i dati di origine nella tabella di destinazione, ad esempio per eseguire upsert o trasformazioni usando logica di business personalizzata. <br/><br/>Si noti che questa stored procedure verrà **richiamata per batch**. Se si vuole eseguire una sola volta un'operazione che non ha nulla a che fare con i dati di origine, ad esempio un'eliminazione o un troncamento, usare la proprietà `preCopyScript`. |No  |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono: coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No  |
| sqlWriterTableType |Specificare il nome di un tipo di tabella da usare nella stored procedure. L'attività di copia rende i dati spostati disponibili in una tabella temporanea con questo tipo di tabella. Il codice della stored procedure può quindi unire i dati copiati con i dati esistenti. |No  |

> [!TIP]
> Quando si copiano dati in un'Istanza gestita di database SQL di Azure, per impostazione predefinita l'attività di copia accoda i dati alla tabella di sink. Per eseguire un'operazione UPSERT o una logica di business aggiuntiva, usare la stored procedure in SqlSink. Altre informazioni sono contenute in [Richiamo delle stored procedure per SQL Sink](#invoking-stored-procedure-for-sql-sink).

**Esempio 1: Accodamento di dati**

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

**Esempio 2: Richiamo di una stored procedure durante la copia per l'operazione upsert**

Altre informazioni sono contenute in [Richiamo delle stored procedure per SQL Sink](#invoking-stored-procedure-for-sql-sink).

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

## <a name="identity-columns-in-the-target-database"></a>Colonne Identity nel database di destinazione

Questa sezione fornisce un esempio per la copia di dati da una tabella di origine senza una colonna identity in una tabella di destinazione con una colonna identity.

**Tabella di origine**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Tabella di destinazione**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Si noti che la tabella di destinazione contiene una colonna identity.

**Definizione JSON del set di dati di origine**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Definizione JSON del set di dati di destinazione**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Si noti che la tabella di origine e la tabella di destinazione hanno schemi diversi (la destinazione include una colonna aggiuntiva identity). In questo scenario è necessario specificare la proprietà **structure** nella definizione del set di dati di destinazione che non include la colonna identity.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Chiamare una stored procedure da un sink SQL

Quando si copiano dati in un'Istanza gestita di database SQL di Azure, è possibile configurare e richiamare una stored procedure specificata per un utente con parametri aggiuntivi.

È possibile usare una stored procedure quando non è possibile usufruire dei meccanismi di copia predefiniti. In genere viene usata quando un'elaborazione upsert, ovvero di inserimento e aggiornamento, o un'operazione supplementare, ovvero di unione colonne, ricerca di valori aggiuntivi, inserimento in più tabelle e così via, deve essere eseguita prima dell'inserimento finale dei dati di origine nella tabella di destinazione.

L'esempio seguente illustra come usare una stored procedure per eseguire un'operazione upsert in una tabella dell'Istanza gestita. Si presuppone che i dati di input e la tabella "Marketing" del sink abbiano tre colonne: ProfileID, State e Category. Eseguire quindi l'operazione upsert nella colonna "ProfileID" e applicarla solo a una categoria specifica.

**Set di dati di output**

```json
{
    "name": "SQLServerDataset",
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

Definire la sezione "SqlSink" nell'attività di copia come indicato di seguito.

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

Nel database definire la stored procedure con lo stesso nome di SqlWriterStoredProcedureName, che gestisce i dati di input dell'origine specificata e li inserisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure deve essere identico al valore "tableName" definito nel set di dati.

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
      VALUES (source.ProfileID, source.State, source.Category)
END
```

Nel database definire il tipo di tabella con lo stesso nome di sqlWriterTableType. Si noti che lo schema del tipo di tabella deve essere identico allo schema restituito dai dati di input.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

La funzionalità di stored procedure sfrutta i [parametri valutati a livello di tabella](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Se si scrive nel tipo di dati Money/Smallmoney richiamando una stored procedure, i valori possono essere arrotondati. Specificare il tipo di dati corrispondente in TVP come Decimal anziché Money/Smallmoney per attenuare. 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapping dei tipi di dati per l'Istanza gestita di database SQL di Azure

Quando si copiano i dati da o verso l'Istanza gestita di database SQL di Azure, vengono usati i mapping seguenti tra i tipi di dati dell'istanza gestita e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipi di dati dell'Istanza gestita di database SQL di Azure | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |boolean |
| char |String, Char[] |
| date |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
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
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |Intervallo di tempo |
|  timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
