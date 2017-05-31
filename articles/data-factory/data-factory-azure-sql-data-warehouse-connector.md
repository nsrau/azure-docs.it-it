---
title: Spostare i dati da/in Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni su come spostare i dati da e verso Azure SQL Data Warehouse mediante Data factory di Azure
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 02c5b7c8932a08bac4bc9e89bd7df3b3e5c57f94
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017


---
# <a name="move-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Spostare dati da e verso Azure SQL Data Warehouse mediante Data factory di Azure
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da e verso Azure SQL Data Warehouse. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.  

> [!TIP]
> Per ottenere prestazioni ottimali, usare PolyBase per caricare i dati in Azure SQL Data Warehouse. Vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) per i dettagli. Per la procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB di dati in Azure SQL Data Warehouse in meno di 15 minuti con Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Scenari supportati
È possibile copiare i dati **da Azure SQL Data Warehouse** negli archivi di dati seguenti:

[!INCLUDE [data-factory-supported-sinks](../../includes/data-factory-supported-sinks.md)]

È possibile copiare i dati dagli archivi dati seguenti **ad Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Quando si copiano dati da SQL Server o da Database SQL di Azure in SQL Data Warehouse, se la tabella non esiste nell'archivio di destinazione, Data Factory la crea automaticamente in SQL Data Warehouse usando lo schema della tabella nell'archivio dati di origine. Per informazioni dettagliate vedere [Creazione automatica della tabella](#auto-table-creation).

## <a name="supported-authentication-type"></a>Tipo di autenticazione supportato
È supportato il connettore Azure SQL Data Warehouse per l'autenticazione di base.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso un Azure SQL Data Warehouse usando diversi strumenti/API.

Il modo più semplice di creare una pipeline che copia i dati in/da Azure SQL Data Warehouse consiste nell'usare la procedura Copia di dati guidata. Vedere [Esercitazione: Caricare i dati in SQL Data Warehouse con Data Factory](../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) per una procedura dettagliata rapida per creare una pipeline usando la procedura guidata Copia dati.

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare una **data factory**. Una data factory può contenere una o più pipeline. 
2. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory. Ad esempio, se si copiano i dati da un'archiviazione BLOB di Azure in Azure SQL Data Warehouse si creano due servizi collegati per collegare l'account di archiviazione di Azure e Azure SQL Data Warehouse alla data factory. Per le proprietà del servizio collegato specifiche per Azure SQL Data Warehouse, vedere la sezione sulle [proprietà del servizio collegato](#linked-service-properties). 
3. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. Nell'esempio citato nel passaggio precedente, si crea un set di dati per specificare un contenitore BLOB e la cartella che contiene i dati di input. Si crea anche un altro set di dati per specificare la tabella SQL in Azure SQL Data Warehouse che contiene i dati copiati dall'archiviazione BLOB. Per le proprietà del set di dati specifiche per Azure SQL Data Warehouse, vedere la sezione sulle [proprietà del set di dati](#dataset-properties).
4. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. Nell'esempio indicato in precedenza si usa BlobSource come origine e SqlDWSink come sink per l'attività di copia. Analogamente, se si esegue la copia da Azure SQL Data Warehouse nell'archiviazione BLOB di Azure, si usa SqlDWSource e BlobSink nell'attività di copia. Per le proprietà dell'attività di copia specifiche per Azure SQL Data Warehouse, vedere la sezione sulle [proprietà dell'attività di copia](#copy-activity-properties). Per informazioni dettagliate su come usare un archivio dati come origine o come sink, fare clic sul collegamento nella sezione precedente per l'archivio dati.

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per esempi con definizioni JSON per entità di data factory utilizzate per copiare i dati da e verso un Azure SQL Data Warehouse, vedere la sezione degli [esempi JSON](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) in questo articolo.

Le sezioni seguenti riportano le informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di data factory specifiche di un Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di Azure SQL Data Warehouse

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà del tipo deve essere impostata su: **AzureSqlDW** |Sì |
| connectionString |Specificare le informazioni necessarie per connettersi all'istanza di Azure SQL Data Warehouse per la proprietà connectionString. È supportata solo l'autenticazione di base. |Sì |

> [!IMPORTANT]
> Configurare il [firewall del database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e il server di database in modo da [consentire ai servizi di Azure di accedere al server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Se si copiano dati in Azure SQL Data Warehouse dall'esterno di Azure e da origini dati locali con gateway di data factory, configurare anche un intervallo di indirizzi IP appropriato per il computer che invia dati ad Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **AzureSqlDWTable** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella o della visualizzazione nell'istanza del database SQL Data Warehouse di Azure a cui fa riferimento il servizio collegato. |Sì |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

> [!NOTE]
> L'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

### <a name="sqldwsource"></a>SqlDWSource
In caso di origine di tipo **SqlDWSource**, nella sezione **typeProperties** sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| SqlReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: selezionare * da MyTable. |No |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. |Nome della stored procedure. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |

Se la proprietà **sqlReaderQuery** è specificata per SqlDWSource, l'attività di copia esegue questa query nell'origine SQL Data Warehouse di Azure per ottenere i dati.

In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).

Se non si specifica sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura del set di dati JSON vengono usate per compilare una query da eseguire su Azure SQL Data Warehouse. Esempio: `select column1, column2 from mytable`. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

#### <a name="sqldwsource-example"></a>Esempio SqlDWSource

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**Definizione della stored procedure:**

```SQL
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

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Specificare una query da eseguire nell'attività di copia per pulire i dati di una sezione specifica. Per informazioni dettagliate, vedere la sezione relativa alla [ripetibilità](#repeatability-during-copy). |Istruzione di query. |No |
| allowPolyBase |Indica se usare PolyBase, quando applicabile, invece del meccanismo BULKINSERT. <br/><br/> **L'uso di PolyBase è il modo consigliato per caricare dati in SQL Data Warehouse.** Per informazioni su vincoli e dettagli, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) . |True  <br/>False (impostazione predefinita) |No |
| polyBaseSettings |Gruppo di proprietà che è possibile specificare quando la proprietà **allowPolybase** è impostata su **true**. |&nbsp; |No |
| rejectValue |Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo. <br/><br/>Per altre informazioni sulle opzioni di rifiuto di PolyBase, vedere la sezione **Arguments** (Argomenti) in [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) . |0 (impostazione predefinita), 1, 2, … |No |
| rejectType |Indica se l'opzione rejectValue viene specificata come valore letterale o come percentuale. |Value (impostazione predefinita), Percentage |No |
| rejectSampleValue |Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate. |1, 2, … |Sì se **rejectType** è **percentage** |
| useTypeDefault |Specifica come gestire i valori mancanti nei file con testo delimitato quando PolyBase recupera dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Arguments (Argomenti) in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |True, False (valore predefinito) |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. |Numero intero (numero di righe) |No (valore predefinito: 10000) |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). |No |

#### <a name="sqldwsink-example"></a>Esempio SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Usare PolyBase per caricare dati in Azure SQL Data Warehouse
**[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** consente di caricare con efficacia grandi quantità di dati in Azure SQL Data Warehouse con una velocità effettiva elevata. L'uso di PolyBase consente un miglioramento significativo della velocità effettiva rispetto al meccanismo BULKINSERT predefinito. Vedere [Copiare il numero di riferimento prestazioni](data-factory-copy-activity-performance.md#performance-reference) con il confronto dettagliato. Per la procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB di dati in Azure SQL Data Warehouse in meno di 15 minuti con Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Se il formato dei dati di origine è **BLOB di Azure o Azure Data Lake Store** e compatibile con PolyBase, è possibile eseguire la copia direttamente in Azure SQL Data Warehouse usando PolyBase. Vedere **[Copia diretta tramite PolyBase](#direct-copy-using-polybase)** con i relativi dettagli.
* Se l'archivio e il formato dei dati di origine non sono supportati in origine da PolyBase, è possibile usare la funzione **[copia di staging tramite PolyBase](#staged-copy-using-polybase)**. Viene inoltre generata una migliore velocità effettiva tramite la conversione automatica dei dati nel formato compatibile con PolyBase e l'archiviazione dei dati in Archiviazione BLOB di Azure. Vengono quindi caricati i dati in SQL Data Warehouse.

Impostare la proprietà `allowPolyBase` su **true**, come illustrato nell'esempio seguente per Azure Data Factory, per usare PolyBase per copiare i dati in Azure SQL Data Warehouse. Quando si imposta allowPolyBase su true, è possibile specificare proprietà specifiche di PolyBase usando il gruppo di proprietà `polyBaseSettings`. Per informazioni dettagliate sulle proprietà che è possibile usare con polyBaseSettings, vedere la sezione [SqlDWSink](#SqlDWSink) .

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Copia diretta tramite PolyBase
PolyBase di SQL Data Warehouse supporta direttamente Archiviazione BLOB di Azure e Azure Data Lake Store (mediante l'entità servizio) come origine e con requisiti di formato di file specifico. Se i dati di origine soddisfano i criteri descritti in questa sezione, è possibile eseguire la copia direttamente dall'archivio dati di origine ad Azure SQL Data Warehouse con PolyBase. In caso contrario è possibile usare la [copia di staging tramite PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Nell'articolo [Azure Data Factory makes it even easier and convenient to uncover insights from data when using Data Lake Store with SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/) (Con Azure Data Factory è ancora più semplice e pratico individuare informazioni utili sui dati quando si usa Data Lake Store con SQL Data Warehouse) sono indicate altre informazioni utili per copiare efficacemente i dati da Data Lake Store a SQL Data Warehouse.

Se i requisiti non vengono soddisfatti, Azure Data Factory controlla le impostazioni e usa automaticamente il meccanismo BULKINSERT per lo spostamento dei dati.

1. Il **servizio collegato all'origine** è di tipo: **AzureStorage** o **AzureDataLakeStore con autenticazione entità servizio**.  
2. Il **set di dati di input** è di tipo **AzureBlob** o **AzureDataLakeStore** e il tipo di formato nelle proprietà `type` è **OrcFormat** o **TextFormat** con le configurazioni seguenti:

   1. `rowDelimiter` deve essere **\n**.
   2. `nullValue` è impostato su **stringa vuota** ("") o `treatEmptyAsNull` è impostato su **true**.
   3. `encodingName` è impostato su **utf-8**, ovvero il valore **predefinito**.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader` e `skipLineCount` non sono specificati.
   5. `compression` può essere **no compression**, **GZip** o **Deflate**.

    ```JSON
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",     
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",       
           "nullValue": "",           
           "encodingName": "utf-8"    
       },
       "compression": {  
           "type": "GZip",  
           "level": "Optimal"  
       }  
    },
    ```

3. Non è disponibile alcuna impostazione `skipHeaderLineCount` in **BlobSource** o **AzureDataLakeStore** per l'attività di copia nella pipeline.
4. Non è disponibile alcuna impostazione `sliceIdentifierColumnName` in **SqlDWSink** per l'attività di copia nella pipeline. PolyBase garantisce che tutti i dati verranno aggiornati o che nessun dato verrà aggiornato in una singola esecuzione. Per ottenere la **ripetibilità**, è possibile usare `sqlWriterCleanupScript`.
5. Nell'attività di copia associata non viene usato alcun valore `columnMapping`.

### <a name="staged-copy-using-polybase"></a>copia di staging tramite PolyBase
Quando i dati di origine non soddisfano i criteri presentati nella sezione precedente, è possibile abilitare la copia dei dati tramite un'istanza di Archiviazione BLOB di Azure di gestione temporanea provvisoria (non può essere Archiviazione Premium). In questo caso, Azure Data Factory esegue automaticamente trasformazioni sui dati in modo che soddisfino i requisiti di formato dei dati di PolyBase e quindi usa PolyBase per caricare i dati in SQL Data Warehouse e infine pulisce i dati temporanei dall'archiviazione BLOB. Per informazioni dettagliate sul funzionamento generale della copia dei dati tramite un BLOB di Azure di staging, vedere la sezione [Copia di staging](data-factory-copy-activity-performance.md#staged-copy) .

> [!NOTE]
> Quando si copiano i dati da un archivio dati locale in Azure SQL Data Warehouse usando PolyBase e la gestione temporanea, se la versione del gateway di gestione dati è precedente alla versione 2.4, il computer gateway deve disporre di JRE (Java Runtime Environment) per poter convertire i dati di origine nel formato corretto. È consigliabile aggiornare il gateway installando la versione più recente per evitare tale dipendenza.
>

Per usare questa funzionalità, creare un [servizio collegato Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) che faccia riferimento all'account di archiviazione di Azure contenente l'archivio BLOB provvisorio e quindi specificare le proprietà `enableStaging` e `stagingSettings` per l'attività di copia come illustrato nel codice seguente:

```json
"activities":[  
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Procedure consigliate per l'uso di PolyBase
Le sezioni seguenti forniscono procedure consigliate aggiuntive a quelle descritte in [Procedure consigliate per Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Autorizzazione database obbligatoria
Per usare PolyBase, è necessario che l'utente che è solito caricare i dati in SQL Data Warehouse disponga dell'[autorizzazione "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) nel database di destinazione. Un modo per ottenere questo risultato consiste nell'aggiungere tale utente come membro del ruolo "db_owner". Informazioni su come eseguire questa operazione sono disponibili nella [sezione](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization) seguente.

### <a name="row-size-and-data-type-limitation"></a>Limitazione alle dimensioni di righe e al tipo di dati
Le operazioni di caricamento di PolyBase sono limitate al caricamento di righe inferiori a **1 MB** che non possono essere caricate in VARCHR(MAX), NVARCHAR(MAX) o VARBINARY(MAX). Vedere [qui](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Se sono presenti dati di origine con righe di dimensioni superiori a 1 MB, è consigliabile suddividere verticalmente le tabelle di origine in tabelle più piccole, in cui le dimensioni massime delle righe di ogni tabella non superano il limite previsto. Le tabelle più piccole possono essere quindi caricate usando PolyBase e unite in Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe di risorse di SQL Data Warehouse
Per ottenere le migliori prestazioni possibili, considerare di assegnare una classe di risorse più ampia all'utente che carica i dati in SQL Data Warehouse tramite PolyBase. Per eseguire questa operazione, seguire la procedura descritta in [Esempio di modifica della classe di risorse di un utente](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName in Azure SQL Data Warehouse
La tabella seguente fornisce esempi relativi a come specificare la proprietà **tableName** nel set di dati JSON per diverse combinazioni di nomi di schema e di tabella.

| Schema di database | Nome tabella | Proprietà JSON tableName |
| --- | --- | --- |
| dbo |MyTable |MyTable o dbo.MyTable o [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable o [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] o [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Se viene visualizzato l'errore seguente, potrebbe essersi verificato un problema con il valore specificato per la proprietà tableName. Per informazioni sul modo corretto di specificare i valori per la proprietà JSON tableName, vedere la relativa tabella.  

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colonne con valori predefiniti
La funzionalità PolyBase in Data Factory accetta attualmente lo stesso numero di colonne disponibili nella tabella di destinazione. Se si ha una tabella con quattro colonne di cui una definita con un valore predefinito, ad esempio, i dati di input dovranno comunque contenere quattro colonne. Se si specifica un set di dati di input con 3 colonne, si verificherà un errore simile al messaggio seguente:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Il valore NULL è una forma speciale di valore predefinito. Se la colonna ammette valori Null, i dati di input (nel BLOB) per tale colonna possono essere vuoti, ma non possono essere mancanti dal set di dati di input. PolyBase inserisce NULL per tali valori in Azure SQL Data Warehouse.  

## <a name="auto-table-creation"></a>Creazione automatica della tabella
Se si usa la copia guidata per copiare i dati da SQL Server o da Database SQL di Azure in SQL Data Warehouse e la tabella che corrisponde alla tabella di origine non esiste nell'archivio di destinazione, Data Factory la crea automaticamente nel data warehouse usando lo schema della tabella di origine.

Data Factory crea la tabella nell'archivio di destinazione con lo stesso nome della tabella nell'archivio dati di origine. I tipi di dati per le colonne vengono scelti in base al mapping dei tipi seguenti. Se necessario, esegue le conversioni del tipo per risolvere eventuali incompatibilità tra gli archivi di origine e di destinazione. Usa inoltre la distribuzione di tabella Round Robin.

| Tipo di colonna di origine del Database SQL | Tipo di colonna SQL DW di destinazione del Database (limitazione delle dimensioni) |
| --- | --- |
| int | int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| Decimal | Decimal |
| Numeric | Decimal |
| Float | Float |
| Money | Money |
| Real | Real |
| SmallMoney | SmallMoney |
| Binary | Binary |
| Varbinary | Varbinary (fino a 8000) |
| Data | Data |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Time | Time |
| Datetimeoffset | Datetimeoffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (fino a 8000) |
| NText | NVarChar (fino a 4000) |
| Image | VarBinary (fino a 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (fino a 8000) |
| NVarChar | NVarChar (fino a 4000) |
| Xml | Varchar (fino a 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

### <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapping dei tipi per Azure SQL Data Warehouse
Come accennato nell'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si spostano dati da e verso SQL Azure, SQL Server, Sybase sono usati i mapping seguenti dal tipo SQL di tipo .NET e viceversa.

Il mapping è uguale al [mapping del tipo di dati di SQL Server per ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo di motore di database di SQL Server | Tipo di .NET Framework |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimale |Decimale |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| immagine |Byte[] |
| int |Int32 |
| money |Decimale |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimale |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimale |
| sql_variant |Object * |
| text |String, Char[] |
| time |Intervallo di tempo |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

È anche possibile eseguire il mapping delle colonne del set di dati di origine alle colonne del set di dati sink nella definizione dell'attività di copia. Per altre informazioni, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Esempi JSON per la copia dei dati da e verso SQL Data Warehouse
Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tali esempi mostrano come copiare dati in e da Azure SQL Data Warehouse e in e dall'archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Data factory di Azure.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Esempio: Copiare i dati da Azure SQL Data Warehouse al BLOB di Azure
L'esempio definisce le entità di Data Factory seguenti:

1. Un servizio collegato di tipo [AzureSqlDW](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureSqlDWTable](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [SqlDWSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio copia ogni ora i dati di una serie temporale (con frequenza oraria, giornaliera e così via) da una tabella del database di Azure SQL Data Warehouse a un BLOB. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di Azure SQL Data Warehouse:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Servizio collegato di archiviazione BLOB di Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Set di dati di input di Azure SQL Data Warehouse:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in Azure SQL Data Warehouse e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Attività di copia in una pipeline con SqlDWSource e BlobSink:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **SqlDWSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```
> [!NOTE]
> Nell'esempio, la proprietà **sqlReaderQuery** è specificata per SqlDWSource. L'attività di copia esegue questa query nell'origine dell’SQL Data Warehouse di Azure per ottenere i dati.
>
> In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).
>
> Se non si specifica il parametro sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura del set di dati JSON vengono usate per compilare una query (selezionare column1, column2 da mytable) da eseguire nell’SQL Data Warehouse. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Esempio: Copiare i dati dal BLOB di Azure ad Azure SQL Data Warehouse
L'esempio definisce le entità di Data Factory seguenti:

1. Un servizio collegato di tipo [AzureSqlDW](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureSqlDWTable](#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlDWSink](#copy-activity-properties).

L'esempio copia ogni ora i dati di una serie temporale (con frequenza oraria, giornaliera e così via) da un BLOB di Azure a una tabella del database di Azure SQL Data Warehouse. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di Azure SQL Data Warehouse:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Servizio collegato di archiviazione BLOB di Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Set di dati di input del BLOB di Azure:**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti di anno, mese e giorno della data/ora di inizio e il nome file usa la parte relativa all'ora. L'impostazione di "external" su "true" comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Set di dati di output di Azure SQL Data Warehouse:**

Nell’esempio vengono copiati dati in una tabella denominata "MyTable" in Azure SQL Data Warehouse. Creare la tabella in Azure SQL Data Warehouse con lo stesso numero di colonne previsto nel file CSV del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Copiare attività in una pipeline con BlobSource e SqlDWSink:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo di **origine** è impostato su **BlobSource** e il tipo di **sink** è impostato su **SqlDWSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
Per una procedura dettagliata, vedere gli articoli [Caricare 1 TB di dati in Azure SQL Data Warehouse in meno di 15 minuti con Azure Data Factory](data-factory-load-sql-data-warehouse.md) e [Caricare i dati con Azure Data Factory](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) nella documentazione di Azure SQL Data Warehouse.

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

