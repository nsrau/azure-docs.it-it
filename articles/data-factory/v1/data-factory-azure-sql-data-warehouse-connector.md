---
title: Copiare dati da e verso Azure sinapsi Analytics
description: Informazioni su come copiare dati da e verso Azure sinapsi Analytics usando Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0d071599b72f6a71bdff815f514311fb87f53d5b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452359"
---
# <a name="copy-data-to-and-from-azure-synapse-analytics-using-azure-data-factory"></a>Copiare dati da e verso Azure sinapsi Analytics usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Versione 2 (corrente)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. Se si usa la versione corrente del servizio Data Factory, vedere [connettore Azure sinapsi Analytics nella versione V2](../connector-azure-sql-data-warehouse.md).

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da e verso Azure sinapsi Analytics. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

> [!TIP]
> Per ottenere prestazioni ottimali, usare PolyBase per caricare i dati in Azure Synapse Analytics. Per i dettagli, vedere la sezione [Usare PolyBase per caricare dati in Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics). Per una procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB di dati in Azure Synapse Analytics in meno di 15 minuti con Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Scenari supportati
È possibile copiare dati **da Azure sinapsi Analytics** negli archivi dati seguenti:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

È possibile copiare i dati dagli archivi dati seguenti **in Azure sinapsi Analytics**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Quando si copiano dati da SQL Server o da un database SQL di Azure ad Azure sinapsi Analytics, se la tabella non esiste nell'archivio di destinazione, Data Factory possibile creare automaticamente la tabella in Azure sinapsi Analytics usando lo schema della tabella nell'archivio dati di origine. Per informazioni dettagliate vedere [Creazione automatica della tabella](#auto-table-creation).

## <a name="supported-authentication-type"></a>Tipo di autenticazione supportato
Il connettore Azure sinapsi Analytics supporta l'autenticazione di base.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con un'attività di copia che sposta i dati da e verso un'analisi delle sinapsi di Azure usando diversi strumenti/API.

Il modo più semplice per creare una pipeline che copia i dati da e verso Azure sinapsi Analytics consiste nell'usare la procedura guidata copia dati. Vedere [esercitazione: caricare i dati in Azure sinapsi Analytics con data factory](../load-azure-sql-data-warehouse.md) per una rapida procedura dettagliata sulla creazione di una pipeline usando la copia guidata dati.

È anche possibile usare gli strumenti seguenti per creare una pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager modello**, **API .NET** e **API REST**. Per istruzioni dettagliate su come creare una pipeline con un'attività di copia, vedere l' [esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare un **Data Factory**. Una data factory può contenere una o più pipeline. 
2. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory. Ad esempio, se si copiano dati da un archivio BLOB di Azure a un'analisi di sinapsi di Azure, si creano due servizi collegati per collegare l'account di archiviazione di Azure e l'analisi delle sinapsi di Azure al data factory. Per le proprietà del servizio collegato specifiche di Azure sinapsi Analytics, vedere la sezione [proprietà del servizio collegato](#linked-service-properties) . 
3. Creare **set** di dati per rappresentare i dati di input e di output per l'operazione di copia. Nell'esempio citato nel passaggio precedente, si crea un set di dati per specificare un contenitore BLOB e la cartella che contiene i dati di input. Si crea quindi un altro set di dati per specificare la tabella in Azure sinapsi Analytics che include i dati copiati dall'archiviazione BLOB. Per le proprietà del set di dati specifiche di Azure sinapsi Analytics, vedere la sezione [Proprietà set di dati](#dataset-properties) .
4. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. Nell'esempio indicato in precedenza si usa BlobSource come origine e SqlDWSink come sink per l'attività di copia. Analogamente, se si esegue la copia da Azure sinapsi Analytics all'archivio BLOB di Azure, si usano SqlDWSource e BlobSink nell'attività di copia. Per le proprietà dell'attività di copia specifiche di Azure sinapsi Analytics, vedere la sezione [proprietà dell'attività di copia](#copy-activity-properties) . Per informazioni dettagliate su come usare un archivio dati come origine o come sink, fare clic sul collegamento nella sezione precedente per l'archivio dati.

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory. Per esempi con definizioni JSON per Data Factory entità usate per copiare dati da e verso Azure sinapsi Analytics, vedere la sezione degli [esempi JSON](#json-examples-for-copying-data-to-and-from-azure-synapse-analytics) in questo articolo.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire Data Factory entità specifiche di Azure sinapsi Analytics:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente fornisce la descrizione degli elementi JSON specifici del servizio collegato di Azure sinapsi Analytics.

| Proprietà | Descrizione | Obbligatoria |
| --- | --- | --- |
| type |La proprietà Type deve essere impostata su: **AzureSqlDW** |Sì |
| connectionString |Specificare le informazioni necessarie per connettersi all'istanza di Azure sinapsi Analytics per la proprietà connectionString. È supportata solo l'autenticazione di base. |Sì |

> [!IMPORTANT]
> Configurare il [firewall del database SQL di Azure](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) e il server di database in modo da [consentire ai servizi di Azure di accedere al server](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Inoltre, se si copiano dati in Azure sinapsi Analytics dall'esterno di Azure, ad esempio da origini dati locali con data factory gateway, configurare un intervallo di indirizzi IP appropriato per il computer che invia dati ad Azure sinapsi Analytics.

## <a name="dataset-properties"></a>Proprietà del set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione typeProperties è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **AzureSqlDWTable** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella o della vista nel database di analisi delle sinapsi di Azure a cui fa riferimento il servizio collegato. |Sì |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

> [!NOTE]
> L'attività di copia accetta solo un input e produce solo un output.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

### <a name="sqldwsource"></a>SqlDWSource
In caso di origine di tipo **SqlDWSource**, nella sezione **typeProperties** sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| sqlReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: selezionare * da MyTable. |No |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. |Nome della stored procedure. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |

Se viene specificato **sqlReaderQuery** per SqlDWSource, l'attività di copia esegue questa query nell'origine di analisi delle sinapsi di Azure per ottenere i dati.

In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).

Se non si specifica sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione Structure del set di dati JSON vengono usate per compilare una query da eseguire su Azure sinapsi Analytics. Esempio: `select column1, column2 from mytable`. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

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
**Definizione stored procedure:**

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

| Proprietà | Descrizione | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Specificare una query da eseguire nell'attività di copia per pulire i dati di una sezione specifica. Per informazioni dettagliate, vedere la sezione relativa alla [ripetibilità](#repeatability-during-copy). |Istruzione di query. |No |
| allowPolyBase |Indica se usare PolyBase, quando applicabile, invece del meccanismo BULKINSERT. <br/><br/> **L'uso di polibase è il modo consigliato per caricare i dati in Azure sinapsi Analytics.** Per i vincoli e i dettagli, vedere [usare la codebase per caricare i dati nella sezione analisi delle sinapsi di Azure](#use-polybase-to-load-data-into-azure-synapse-analytics) . |True <br/>False (impostazione predefinita) |No |
| polyBaseSettings |Gruppo di proprietà che è possibile specificare quando la proprietà **allowPolybase** è impostata su **true**. |&nbsp; |No |
| rejectValue |Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo. <br/><br/>Altre informazioni sulle opzioni di rifiuto della polibase nella sezione **argomenti** dell'argomento [Create external Table (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) . |0 (impostazione predefinita), 1, 2, … |No |
| rejectType |Indica se l'opzione rejectValue viene specificata come valore letterale o come percentuale. |Value (impostazione predefinita), Percentage |No |
| rejectSampleValue |Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate. |1, 2, … |Sì se **rejectType** è **percentage** |
| useTypeDefault |Specifica come gestire valori mancanti nei file di testo delimitato quando PolyBase recupera i dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Arguments (Argomenti) in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql). |True, False (valore predefinito) |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. |Numero intero (numero di righe) |No (valore predefinito: 10000) |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |timespan<br/><br/> Esempio: "00:30:00" (30 minuti). |No |

#### <a name="sqldwsink-example"></a>Esempio SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Usare la polibase per caricare i dati in Azure sinapsi Analytics
L'uso di **[polibase](/sql/relational-databases/polybase/polybase-guide)** è un modo efficiente per caricare grandi quantità di dati in Azure sinapsi Analytics con una velocità effettiva elevata. L'uso di PolyBase consente un miglioramento significativo della velocità effettiva rispetto al meccanismo BULKINSERT predefinito. Vedere [Copiare il numero di riferimento prestazioni](data-factory-copy-activity-performance.md#performance-reference) con il confronto dettagliato. Per una procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB di dati in Azure Synapse Analytics in meno di 15 minuti con Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Se i dati di origine si trova in un **BLOB di Azure o in Azure Data Lake Store** e il formato è compatibile con la polibase, è possibile eseguire la copia direttamente in Azure sinapsi Analytics usando la polibase. Vedere **[Copia diretta tramite PolyBase](#direct-copy-using-polybase)** con i relativi dettagli.
* Se l'archivio e il formato dei dati di origine non sono supportati in origine da PolyBase, è possibile usare la funzione **[copia di staging tramite PolyBase](#staged-copy-using-polybase)**. Viene inoltre generata una migliore velocità effettiva tramite la conversione automatica dei dati nel formato compatibile con PolyBase e l'archiviazione dei dati in Archiviazione BLOB di Azure. Quindi carica i dati in Azure sinapsi Analytics.

Impostare la `allowPolyBase` proprietà su **true** , come illustrato nell'esempio seguente per Azure Data Factory usare la polibase per copiare i dati in Azure sinapsi Analytics. Quando si imposta allowPolyBase su true, è possibile specificare proprietà specifiche di PolyBase usando il gruppo di proprietà `polyBaseSettings`. Per informazioni dettagliate sulle proprietà che è possibile usare con polyBaseSettings, vedere la sezione [SqlDWSink](#sqldwsink) .

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
Azure sinapsi Analytics supporta direttamente il BLOB di Azure e Azure Data Lake Store (usando l'entità servizio) come origine e con requisiti specifici per il formato di file. Se i dati di origine soddisfano i criteri descritti in questa sezione, è possibile copiare direttamente dall'archivio dati di origine ad Azure sinapsi Analytics usando la polibase. In caso contrario è possibile usare la [copia di staging tramite PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Per copiare i dati da Data Lake Store ad Azure sinapsi Analytics in modo efficiente, è possibile ottenere altre informazioni da [Azure Data Factory rende ancora più semplice e pratico individuare informazioni dettagliate dai dati quando si usa data Lake Store con analisi delle sinapsi di Azure](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Se i requisiti non vengono soddisfatti, Azure Data Factory controlla le impostazioni e usa automaticamente il meccanismo BULKINSERT per lo spostamento dei dati.

1. Il **servizio collegato all'origine** è di tipo: **AzureStorage** o **AzureDataLakeStore con autenticazione dell'entità servizio**.
2. Il **set di dati di input** è di tipo **AzureBlob** o **AzureDataLakeStore** e il tipo di formato nelle proprietà `type` è **OrcFormat****ParquetFormat** o **TextFormat**, con le configurazioni seguenti:

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
Quando i dati di origine non soddisfano i criteri introdotti nella sezione precedente, è possibile abilitare la copia dei dati tramite un archivio BLOB di Azure di staging provvisorio (non può essere l'archiviazione Premium). In questo caso, Azure Data Factory esegue automaticamente trasformazioni sui dati per soddisfare i requisiti di formato dei dati di polibase, quindi usa la polibase per caricare i dati in Azure sinapsi Analytics e infine pulisce i dati temporanei dall'archiviazione BLOB. Per informazioni dettagliate sul funzionamento generale della copia dei dati tramite un BLOB di Azure di staging, vedere la sezione [Copia di staging](data-factory-copy-activity-performance.md#staged-copy) .

> [!NOTE]
> Quando si copiano dati da un archivio dati locale in Azure sinapsi Analytics usando la versione di base e la gestione temporanea, se la versione del gateway Gestione dati è inferiore a 2,4, JRE (Java Runtime Environment) è necessario nel computer gateway usato per trasformare i dati di origine nel formato corretto. È consigliabile aggiornare il gateway installando la versione più recente per evitare tale dipendenza.
>

Per usare questa funzionalità, creare un [servizio collegato Archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) che faccia riferimento all'account di archiviazione di Azure contenente l'archivio BLOB provvisorio e quindi specificare le proprietà `enableStaging` e `stagingSettings` per l'attività di copia come illustrato nel codice seguente:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to Azure Synapse Analytics via PolyBase",
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
Le sezioni seguenti forniscono procedure consigliate aggiuntive a quelle descritte in [procedure consigliate per l'analisi delle sinapsi di Azure](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Autorizzazione database obbligatoria
Per usare la polibase, è necessario che l'utente usato per caricare i dati in Azure sinapsi Analytics disponga dell' [autorizzazione "Control"](/sql/relational-databases/security/permissions-database-engine) per il database di destinazione. Un modo per ottenere questo risultato consiste nell'aggiungere tale utente come membro del ruolo "db_owner". Informazioni su come eseguire questa operazione sono disponibili nella [sezione](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization) seguente.

### <a name="row-size-and-data-type-limitation"></a>Limitazione alle dimensioni di righe e al tipo di dati
Le operazioni di caricamento di PolyBase sono limitate al caricamento di righe inferiori a **1 MB** che non possono essere caricate in VARCHR(MAX), NVARCHAR(MAX) o VARBINARY(MAX). Vedere [qui](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Se sono presenti dati di origine con righe di dimensioni superiori a 1 MB, è consigliabile suddividere verticalmente le tabelle di origine in tabelle più piccole, in cui le dimensioni massime delle righe di ogni tabella non superano il limite previsto. Le tabelle più piccole possono quindi essere caricate usando la polibase e unite in Azure sinapsi Analytics.

### <a name="azure-synapse-analytics-resource-class"></a>Classe di risorse di analisi di sinapsi di Azure
Per ottenere la massima velocità effettiva possibile, è consigliabile assegnare una classe di risorse di dimensioni maggiori all'utente usato per caricare i dati in Azure sinapsi Analytics tramite la polibase. Per eseguire questa operazione, seguire la procedura descritta in [Esempio di modifica della classe di risorse di un utente](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md).

### <a name="tablename-in-azure-synapse-analytics"></a>TableName in Azure sinapsi Analytics
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
Il valore NULL è una forma speciale di valore predefinito. Se la colonna ammette valori Null, i dati di input (nel BLOB) per tale colonna possono essere vuoti, ma non possono essere mancanti dal set di dati di input. La polibase inserisce il valore NULL in Azure sinapsi Analytics.

## <a name="auto-table-creation"></a>Creazione automatica della tabella
Se si usa la copia guidata per copiare dati da SQL Server o da un database SQL di Azure ad Azure sinapsi Analytics e la tabella che corrisponde alla tabella di origine non esiste nell'archivio di destinazione, Data Factory possibile creare automaticamente la tabella nel data warehouse usando lo schema della tabella di origine.

Data Factory crea la tabella nell'archivio di destinazione con lo stesso nome della tabella nell'archivio dati di origine. I tipi di dati per le colonne vengono scelti in base al mapping dei tipi seguenti. Se necessario, esegue le conversioni del tipo per risolvere eventuali incompatibilità tra gli archivi di origine e di destinazione. Usa inoltre la distribuzione di tabella Round Robin.

| Tipo di colonna di origine del Database SQL | Tipo di colonna di analisi sinapsi di Azure di destinazione (limitazione dimensioni) |
| --- | --- |
| Int | Int |
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
| Datetime | Datetime |
| DateTime2 | DateTime2 |
| Tempo | Tempo |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Testo | Varchar (fino a 8000) |
| NText | NVarChar (fino a 4000) |
| Immagine | VarBinary (fino a 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (fino a 8000) |
| NVarChar | NVarChar (fino a 4000) |
| Xml | Varchar (fino a 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-synapse-analytics"></a>Mapping dei tipi per Azure sinapsi Analytics
Come accennato nell'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni automatiche da tipi di origine a tipi di sink con l'approccio seguente in 2 passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo di sink nativo

Quando si trasferiscono i dati in & da Azure sinapsi Analytics, vengono usati i mapping seguenti dal tipo SQL al tipo .NET e viceversa.

Il mapping è uguale al [mapping del tipo di dati SQL Server per ADO.NET](/dotnet/framework/data/adonet/sql-server-data-type-mappings).

| Tipo di motore di database di SQL Server | Tipo .NET Framework |
| --- | --- |
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
| sql_variant |Object * |
| text |String, Char[] |
| time |TimeSpan |
|  timestamp |Byte[] |
| TINYINT |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

È anche possibile eseguire il mapping delle colonne del set di dati di origine alle colonne del set di dati sink nella definizione dell'attività di copia. Per altre informazioni, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-azure-synapse-analytics"></a>Esempi JSON per la copia di dati da e verso Azure sinapsi Analytics
Gli esempi seguenti forniscono le definizioni JSON di esempio che è possibile usare per creare una pipeline usando [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustrano come copiare dati da e verso Azure sinapsi Analytics e archiviazione BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Data factory di Azure.

### <a name="example-copy-data-from-azure-synapse-analytics-to-azure-blob"></a>Esempio: copiare i dati da Azure sinapsi Analytics al BLOB di Azure
L'esempio definisce le entità di Data Factory seguenti:

1. Un servizio collegato di tipo [AzureSqlDW](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureSqlDWTable](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [SqlDWSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio copia i dati di serie temporali (oraria, giornaliera e così via) da una tabella del database di analisi delle sinapsi di Azure a un BLOB ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di Azure sinapsi Analytics:**

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
**Set di dati di input di analisi sinapsi di Azure:**

Nell'esempio si presuppone che sia stata creata una tabella "MyTable" in Azure sinapsi Analytics e che contenga una colonna denominata "timestampcolumn" per i dati delle serie temporali.

Impostando "external": "true" si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

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
> Nell'esempio, la proprietà **sqlReaderQuery** è specificata per SqlDWSource. L'attività di copia esegue questa query nell'origine Azure sinapsi Analytics per ottenere i dati.
>
> In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).
>
> Se non si specifica sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione Structure del set di dati JSON vengono usate per compilare una query (SELECT column1, column2 FROM MyTable) per l'esecuzione in Azure sinapsi Analytics. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-synapse-analytics"></a>Esempio: copiare i dati dal BLOB di Azure ad Azure sinapsi Analytics
L'esempio definisce le entità di Data Factory seguenti:

1. Un servizio collegato di tipo [AzureSqlDW](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureSqlDWTable](#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlDWSink](#copy-activity-properties).

L'esempio copia i dati di una serie temporale (oraria, giornaliera e così via) dal BLOB di Azure a una tabella in un database di analisi delle sinapsi di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato di Azure sinapsi Analytics:**

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
**Set di dati di input BLOB di Azure:**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, e giorno dell'ora di inizio e il nome del file usa la parte dell'ora di inizio relativa all'ora. l'impostazione "External": "true" informa il servizio Data Factory che questa tabella è esterna al data factory e non è prodotta da un'attività nel data factory.

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
**Set di dati di output di analisi sinapsi di Azure:**

L'esempio copia i dati in una tabella denominata "MyTable" in Azure sinapsi Analytics. Creare la tabella in Azure sinapsi Analytics con lo stesso numero di colonne che si prevede siano contenute nel file CSV del BLOB. Alla tabella vengono aggiunte nuove righe ogni ora.

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
Per una procedura dettagliata, vedere l'articolo [caricare 1 TB in Azure sinapsi Analytics sotto 15 minuti con Azure Data Factory](data-factory-load-sql-data-warehouse.md) e [caricare dati con Azure Data Factory](../load-azure-sql-data-warehouse.md) articolo nella documentazione di Azure sinapsi Analytics.

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).