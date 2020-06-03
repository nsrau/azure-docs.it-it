---
title: CREATE EXTERNAL STREAM (Transact-SQL) - SQL Edge di Azure (anteprima)
description: Informazioni sull'istruzione CREATE EXTERNAL STREAM in SQL Edge di Azure (anteprima)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e1f672a62ee7687fec9cea96ca03240c893ba95
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233322"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

L'oggetto EXTERNAL STREAM ha un duplice scopo: input e output. Può essere usato come input per eseguire query sui dati di streaming da servizi di inserimento di eventi, come Hub eventi di Azure o hub IoT, oppure può essere usato come output per specificare dove e come archiviare i risultati da una query di streaming.

È anche possibile specificare e creare un oggetto EXTERNAL STREAM come output e input per servizi come Hub eventi o Archiviazione BLOB. Questa possibilità è prevista per scenari di concatenamento in cui una query di streaming salva in modo permanente i risultati nel flusso esterno come output e un'altra query di streaming legge dallo stesso flusso esterno come input. 


## <a name="syntax"></a>Sintassi

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>Argomenti


- [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION**: specifica il nome dei dati effettivi o della posizione nell'origine dati. Nel caso di un hub di Edge o di un oggetto flusso Kafka, la posizione specifica il nome dell'hub di Edge o dell'argomento Kafka da cui eseguire la lettura o la scrittura.
- **INPUT_OPTIONS**: specificare le opzioni come coppie chiave-valore per i servizi, ad esempio hub eventi o hub IoT, usati come input per le query di streaming
    - CONSUMER_GROUP: gli hub eventi e gli hub IoT limitano il numero di lettori in un gruppo di consumer (a 5). Se si lascia vuoto questo campo, verrà usato il gruppo di consumer '$Default'.
      - Si applica a hub eventi e hub IoT
    - TIME_POLICY: descrive se eliminare gli eventi o modificare l'ora dell'evento in presenza di eventi in ritardo o eventi non in ordine che superano il relativo valore di tolleranza.
      - Si applica a hub eventi e hub IoT
    - LATE_EVENT_TOLERANCE: ritardo massimo accettabile. Il ritardo rappresenta la differenza tra il timestamp dell'evento e l'orologio di sistema.
      - Si applica a hub eventi e hub IoT
    - OUT_OF_ORDER_EVENT_TOLERANCE: gli eventi possono arrivare non in ordine dopo aver seguito il percorso dall'input alla query di streaming. Questi eventi possono essere accettati così come sono oppure è possibile scegliere di sospenderli per un periodo di tempo determinato per riordinarli.
      - Si applica a hub eventi e hub IoT
- **OUTPUT_OPTIONS**: specificare le opzioni come coppie chiave-valore per i servizi supportati usati come output per le query di streaming 
  - REJECT_POLICY:  DROP | RETRY Specifica i criteri di gestione degli errori dei dati quando si verificano errori di conversione dei dati. 
    - Si applica a tutti gli output supportati 
  - MINIMUM_ROWS:  
    numero minimo di righe necessarie per ogni batch scritto in un output. Per Parquet, ogni batch creerà un nuovo file. 
    - Si applica a tutti gli output supportati 
  - MAXIMUM_TIME:  
    tempo massimo di attesa per batch. Dopo questo periodo di tempo il batch verrà scritto nell'output anche se il requisito di righe minime non è stato soddisfatto. 
    - Si applica a tutti gli output supportati 
  - PARTITION_KEY_COLUMN:  
    colonna usata per la chiave di partizione. 
    - Si applica a hub eventi e all'argomento del bus di servizio 
  - PROPERTY_COLUMNS:  
    elenco di nomi di colonna di output delimitati da virgole da associare ai messaggi come proprietà personalizzate, se forniti.  
    - Si applica a hub eventi e all'argomento e alla coda del bus di servizio 
  - SYSTEM_PROPERTY_COLUMNS:  
    raccolta in formato JSON di coppie nome-valore dei nomi delle proprietà di sistema e delle colonne di output da popolare nei messaggi del bus di servizio. Ad esempio { "MessageId":   "column1", "PartitionKey": "column2"} 
    - Si applica all'argomento e alla coda del bus di servizio 
  - PARTITION_KEY:  
    Nome della colonna di output contenente la chiave di partizione. La chiave di partizione è un identificatore univoco per la partizione in una determinata tabella che costituisce la prima parte della chiave primaria di un'entità. Si tratta di un valore stringa le cui dimensioni massime sono di 1 KB. 
    - Si applica ad Archiviazione tabelle e a Funzioni di Azure 
  - ROW_KEY:  
    Nome della colonna di output contenente la chiave di riga. La chiave di riga è un identificatore univoco per un’entità all'interno di una determinata partizione. Costituisce la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore stringa le cui dimensioni massime sono di 1 KB. 
    - Si applica ad Archiviazione tabelle e a Funzioni di Azure 
  - BATCH_SIZE:  
    rappresenta il numero di transazioni per l'archiviazione tabelle in cui il massimo può raggiungere 100 record. Per Funzioni di Azure rappresenta le dimensioni del batch in byte inviate alla funzione per ogni chiamata. Il valore predefinito è 256 kB. 
    - Si applica ad Archiviazione tabelle e a Funzioni di Azure 
  - MAXIMUM_BATCH_COUNT:  
    numero massimo di eventi inviati alla funzione per ogni chiamata. Il valore predefinito è 100. Per il database SQL, rappresenta il numero massimo di record inviati con ogni transazione di inserimento bulk. Il valore predefinito è 10.000. 
    - Si applica al database SQL e a Funzioni di Azure 
  - STAGING_AREA: Oggetto EXTERNAL DATA SOURCE per Archiviazione BLOB. Area di staging per l'inserimento dei dati a velocità effettiva elevata in SQL Data Warehouse 
    - Si applica a SQL Data Warehouse


## <a name="examples"></a>Esempi

### <a name="example-1---edgehub"></a>Esempio 1 - EdgeHub

Digitare: Input o output<br>
Parametri
- Input o output
  - Alias 
  - Formato di serializzazione eventi 
  - Codifica 
- Solo input: 
  - Tipo di compressione eventi 

Sintassi:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Esempio 2 - Database SQL di Azure, SQL Edge di Azure, SQL Server

Digitare: Output<br>
Parametri
- Alias di output  
- Database (obbligatorio per il database SQL) 
- Server (obbligatorio per il database SQL) 
- Nome utente (obbligatorio per il database SQL) 
- Password (obbligatoria per il database SQL) 
- Tabella 
- Unire tutte le partizioni di input in un singolo schema di partizione di scrittura o di ereditarietà del passaggio o dell'input della query precedente (obbligatorio per il database SQL) 
- Numero massimo di batch 

Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>Esempio 3 - Kafka

Digitare: Input<br>
Parametri

- Server bootstrap Kafka 
- Nome argomento Kafka 
- Numero di partizioni di origine 

Sintassi:

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>Esempio 4 - Archiviazione BLOB

Digitare: Input o output<br>
Parametri
- Input o output:
  - Alias 
  - Account di archiviazione 
  - Chiave dell'account di archiviazione 
  - Contenitore 
  - Modello di percorso 
  - Formato data 
  - Formato ora 
  - Formato di serializzazione eventi 
  - Codifica 
- Solo input: 
  - Partizioni (input) 
  - Tipo di compressione eventi (input) 
- Solo output: 
  - Numero minimo di righe (output) 
  - Tempo massimo (output) 
  - Modalità di autenticazione (output) 

Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>Esempio 5 - Hub eventi

Digitare: Input o output<br>
Parametri
- Input o output:
  - Alias 
  - Spazio dei nomi del bus di servizio 
  - Nome dell'hub eventi 
  - Nome criteri hub eventi 
  - Chiave criteri hub eventi 
  - Formato di serializzazione eventi 
  - Codifica 
- Solo input: 
  - Gruppo di consumer dell'hub eventi 
  - Tipo di compressione eventi 
- Solo output: 
  - Colonna chiave di partizione 
  - Colonne delle proprietà 

Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>Esempio 6 - Hub IoT

Digitare: Input<br>
Parametri

- Alias di input 
- Hub IoT 
- Endpoint 
- Nome criteri di accesso condiviso 
- Chiave criteri di accesso condiviso 
- Gruppo di consumer 
- Formato di serializzazione eventi 
- Codifica 
- Tipo di compressione eventi 

Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>Esempio 7 - Azure Synapse Analytics (in precedenza SQL Data Warehouse)

Digitare: Output<br>
Parametri
- Alias di output 
- Database 
- Server 
- Username 
- Password 
- Tabella 
- Area di staging (per COPY) 

Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>Esempio 8 - Archiviazione tabelle

Digitare: Output<br>
Parametri
- Alias di output 
- Account di archiviazione 
- Chiave dell'account di archiviazione 
- Nome tabella 
- Chiave di partizione 
- Chiave di riga 
- Dimensioni dei batch 

Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>Esempio 9 - Argomento del bus di servizio (stesse proprietà della coda)

Digitare: Output<br>
Parametri
- Alias di output 
- Spazio dei nomi del bus di servizio 
- Nome argomento 
- Nome criteri argomento 
- Chiave criteri argomento 
- Colonne delle proprietà 
- Colonne delle proprietà di sistema 
- Formato di serializzazione eventi 
- Codifica 

Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>Esempio 10 - Cosmos DB

Digitare: Output<br>
Parametri
- Alias di output 
- Account ID 
- Chiave account 
- Database 
- Nome contenitore 
- Document ID 


Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>Esempio 11 - Power BI

Digitare: Output<br>
Parametri
- Alias di output 
- Nome del set di dati 
- Nome tabella 


Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>Esempio 12 - Funzioni di Azure

Digitare: Output<br>
Parametri
- App per le funzioni 
- Funzione 
- Chiave 
- Dimensioni massime batch 
- Numero massimo di batch 

Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>Vedere anche

- [ALTER EXTERNAL STREAM (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 

