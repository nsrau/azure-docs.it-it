---
title: CREARE un flusso esterno (Transact-SQL)-Azure SQL Edge
description: Informazioni sull'istruzione CREATE EXTERNAL STREAM in Azure SQL Edge
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: e28ce4cd46cb802241e02e4060441747389d3989
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888169"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

L'oggetto flusso esterno ha uno scopo doppio di un flusso di input e di output. Può essere usato come input per eseguire query sui dati di streaming da servizi di inserimento di eventi, ad esempio hub eventi di Azure, Hub Azure (o Hub Edge) o Kafka oppure può essere usato come output per specificare dove e come archiviare i risultati da una query di streaming.

È anche possibile specificare e creare un oggetto EXTERNAL STREAM come output e input per servizi come Hub eventi o Archiviazione BLOB. Ciò facilita il concatenamento di scenari in cui una query di streaming rende permanente i risultati nel flusso esterno come output e un'altra query di streaming che legge dallo stesso flusso esterno come input.

SQL Edge di Azure attualmente supporta solo le origini dati seguenti come output e input di flusso.

| Tipo di origine dati | Input | Output | Descrizione |
|------------------|-------|--------|------------------|
| Hub Azure IoT Edge | S | S | Origine dati per la lettura e la scrittura dei dati di streaming in un hub Azure IoT Edge. Per altre informazioni, vedere [Hub IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).|
| Database SQL | N | S | Connessione all'origine dati per scrivere i dati di streaming nel database SQL. Il database può essere un database locale in Azure SQL Edge o un database remoto in SQL Server o nel database SQL di Azure.|
| Kafka | S | N | Origine dati per la lettura dei dati in streaming da un argomento Kafka. Il supporto Kafka non è disponibile per la versione ARM64 di Azure SQL Edge.|



## <a name="syntax"></a>Sintassi

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>Argomenti

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION**: specifica il nome dei dati effettivi o della posizione nell'origine dati. 
   - Per l'Hub Edge o gli oggetti di flusso Kafka, location specifica il nome dell'Hub Edge o dell'argomento Kafka da cui eseguire la lettura o la scrittura.
   - Per gli oggetti di flusso SQL (SQL Server, database SQL di Azure o Azure SQL Edge) specifica il nome della tabella. Se il flusso viene creato nello stesso database e nello stesso schema della tabella di destinazione, è sufficiente solo il nome della tabella. In caso contrario, è necessario qualificare completamente (<database_name. schema_name. table_name) il nome della tabella.
   - Per il percorso dell'oggetto flusso di archiviazione BLOB di Azure si riferisce al modello di percorso da usare all'interno del contenitore BLOB. Per altre informazioni su questa funzionalità, vedere (/articles/Stream-Analytics/Stream-Analytics-define-Outputs.MD # BLOB-Storage-and-Azure-Data-Lake-Gen2)

- **INPUT_OPTIONS**: specificare le opzioni come coppie chiave-valore per servizi come Kafka, Hub IOT Edge che sono input per le query di streaming
    - PARTIZIONI: numero di partizioni definite per un argomento. Il numero massimo di partizioni che è possibile usare è limitato a 32.
      - Si applica ai flussi di input Kafka
    - CONSUMER_GROUP: gli hub eventi e gli hub IoT limitano il numero di lettori in un gruppo di consumer (a 5). Se si lascia vuoto questo campo, verrà usato il gruppo di consumer '$Default'.
      - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge.  
    - TIME_POLICY: descrive se eliminare gli eventi o modificare l'ora dell'evento in presenza di eventi in ritardo o eventi non in ordine che superano il relativo valore di tolleranza.
      - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge.
    - LATE_EVENT_TOLERANCE: ritardo massimo accettabile. Il ritardo rappresenta la differenza tra il timestamp dell'evento e l'orologio di sistema.
      - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge.
    - OUT_OF_ORDER_EVENT_TOLERANCE: gli eventi possono arrivare non in ordine dopo aver seguito il percorso dall'input alla query di streaming. Questi eventi possono essere accettati così come sono oppure è possibile scegliere di sospenderli per un periodo di tempo determinato per riordinarli.
      - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge.
        
- **OUTPUT_OPTIONS**: specificare le opzioni come coppie chiave-valore per i servizi supportati usati come output per le query di streaming 
  - REJECT_POLICY:  DROP | RETRY Specifica i criteri di gestione degli errori dei dati quando si verificano errori di conversione dei dati. 
    - Si applica a tutti gli output supportati 
  - MINIMUM_ROWS:  
    numero minimo di righe necessarie per ogni batch scritto in un output. Per Parquet, ogni batch creerà un nuovo file. 
    - Si applica a tutti gli output supportati 
  - MAXIMUM_TIME:  
    Tempo di attesa massimo in minuti per batch. Dopo questo periodo di tempo il batch verrà scritto nell'output anche se il requisito di righe minime non è stato soddisfatto. 
    - Si applica a tutti gli output supportati 
  - PARTITION_KEY_COLUMN:  
    colonna usata per la chiave di partizione. 
    - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge.
  - PROPERTY_COLUMNS:  
    elenco di nomi di colonna di output delimitati da virgole da associare ai messaggi come proprietà personalizzate, se forniti.  
    - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge. 
  - SYSTEM_PROPERTY_COLUMNS:  
    raccolta in formato JSON di coppie nome-valore dei nomi delle proprietà di sistema e delle colonne di output da popolare nei messaggi del bus di servizio. Ad esempio { "MessageId":   "column1", "PartitionKey": "column2"} 
    - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge. 
  - PARTITION_KEY:  
    Nome della colonna di output contenente la chiave di partizione. La chiave di partizione è un identificatore univoco per la partizione in una determinata tabella che costituisce la prima parte della chiave primaria di un'entità. Si tratta di un valore stringa le cui dimensioni massime sono di 1 KB. 
    - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge.
  - ROW_KEY:  
    Nome della colonna di output contenente la chiave di riga. La chiave di riga è un identificatore univoco per un’entità all'interno di una determinata partizione. Costituisce la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore stringa le cui dimensioni massime sono di 1 KB. 
    - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge.
  - BATCH_SIZE:  
    rappresenta il numero di transazioni per l'archiviazione tabelle in cui il massimo può raggiungere 100 record. Per Funzioni di Azure rappresenta le dimensioni del batch in byte inviate alla funzione per ogni chiamata. Il valore predefinito è 256 kB. 
    - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge. 
  - MAXIMUM_BATCH_COUNT:  
    numero massimo di eventi inviati alla funzione per ogni chiamata. Il valore predefinito è 100. Per il database SQL, rappresenta il numero massimo di record inviati con ogni transazione di inserimento bulk. Il valore predefinito è 10.000. 
    - Si applica a tutti gli output basati su SQL 
  - STAGING_AREA: oggetto origine dati esterna per l'archiviazione BLOB nell'area di gestione temporanea per l'inserimento di dati a velocità effettiva elevata in Azure sinapsi Analytics 
    - Riservato per un utilizzo futuro. Non si applica a Azure SQL Edge.


## <a name="examples"></a>Esempi

### <a name="example-1---edgehub"></a>Esempio 1 - EdgeHub

Digitare: Input o output<br>

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
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Esempio 2 - Database SQL di Azure, SQL Edge di Azure, SQL Server

Digitare: Output<br>

Sintassi:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>Esempio 3 - Kafka

Digitare: Input<br>

Sintassi:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>Vedere anche

- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 

