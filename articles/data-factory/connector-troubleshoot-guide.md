---
title: Risolvere i problemi di Azure Data Factory connettori
description: Informazioni su come risolvere i problemi del connettore in Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778227"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Risolvere i problemi di Azure Data Factory connettori

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per i connettori in Azure Data Factory.
  

## <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

### <a name="error-code--azurebloboperationfailed"></a>Codice di errore: AzureBlobOperationFailed

- **Messaggio**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa**: l'operazione di archiviazione BLOB ha raggiunto un problema.

- **Raccomandazione**: controllare l'errore in dettagli. Fare riferimento al documento della Guida BLOB: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Se serve assistenza, contattare il team di archiviazione.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Codice di errore: AzureBlobServiceNotReturnExpectedDataLength

- **Messaggio**: `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Codice di errore: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Messaggio**: `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Codice di errore: AzureStorageOperationFailedConcurrentWrite

- **Messaggio**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Messaggio di errore: dimensione della richiesta troppo grande

- **Sintomi**: i dati vengono copiati in Azure Cosmos DB con le dimensioni predefinite del batch di scrittura e l'errore di hit *"**dimensioni della richiesta è troppo grande**"* .

- **Motivo**: Cosmos DB limita le dimensioni di una singola richiesta a 2 MB. La formula è, Size della richiesta = dimensioni del singolo documento * dimensioni del batch di scrittura. Se le dimensioni del documento sono elevate, il comportamento predefinito comporterà dimensioni di richiesta troppo grandi. È possibile ottimizzare le dimensioni del batch di scrittura.

- **Soluzione**: nel sink dell'attività di copia ridurre il valore di ' dimensioni batch di scrittura ' (il valore predefinito è 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Messaggio di errore: violazione del vincolo di indice univoco

- **Sintomi**: quando si copiano i dati in Cosmos DB, viene raggiunto l'errore seguente:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Causa**: sono possibili due cause:

    - Se si usa l'istruzione **Insert** As Write Behavior, questo errore indica che i dati di origine contengono righe/oggetti con lo stesso ID.

    - Se si usa **Upsert** come comportamento di scrittura e si imposta un'altra chiave univoca per il contenitore, questo errore indica che i dati di origine contengono righe/oggetti con ID diversi ma lo stesso valore per la chiave univoca definita.

- **Soluzione**: 

    - Per cause1, impostare **Upsert** come comportamento di scrittura.
    - Per la seconda ragione, assicurarsi che ogni documento abbia un valore diverso per la chiave univoca definita.

### <a name="error-message-request-rate-is-large"></a>Messaggio di errore: la frequenza delle richieste è elevata

- **Sintomi**: quando si copiano i dati in Cosmos DB, viene raggiunto l'errore seguente:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Motivo**: le unità richiesta usate sono maggiori di quelle disponibili configurate in Cosmos DB. Informazioni su come Cosmos DB calcola ur da [qui](../cosmos-db/request-units.md#request-unit-considerations).

- **Soluzione**: di seguito sono riportate due soluzioni:

    1. **Aumentare il** numero di unità richiesta del contenitore a un valore più grande in Cosmos DB, che consente di migliorare le prestazioni dell'attività di copia, anche se comporta un aumento dei costi Cosmos DB. 

    2. Ridurre il valore di **writeBatchSize** a un valore più piccolo, ad esempio 1000, e impostare **parallelCopies** su un valore più basso, ad esempio 1, in modo che le prestazioni dell'esecuzione della copia risultino inferiori a quelle correnti ma non aumenteranno i costi in Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Colonna mancante nel mapping delle colonne

- **Sintomi**: quando si importa lo schema per Cosmos DB per il mapping delle colonne, alcune colonne risultano mancanti. 

- **Motivo**: ADF deduce lo schema dai primi 10 documenti Cosmos DB. Se alcune colonne/proprietà non dispongono di un valore in tali documenti, non verranno rilevati da ADF, quindi non verranno visualizzati.

- **Soluzione**: è possibile ottimizzare la query come indicato di seguito per applicare la colonna per la visualizzazione nel set di risultati con un valore vuoto: (si supponga che la colonna "Impossibile" non sia presente nei primi 10 documenti). In alternativa, è possibile aggiungere manualmente la colonna per il mapping.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Messaggio di errore: GuidRepresentation per il Reader è CSharpLegacy

- **Sintomi**: quando si copiano dati da Cosmos DB MongoAPI/MongoDB con il campo UUID, viene raggiunto l'errore seguente:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Motivo**: esistono due modi per rappresentare UUID in BSON-UuidStardard e UuidLegacy. Per impostazione predefinita, UuidLegacy viene usato per leggere i dati. Si raggiungerà un errore se i dati UUID in MongoDB sono UuidStandard.

- **Soluzione**: nella stringa di connessione di MongoDB aggiungere l'opzione "**uuidRepresentation = standard**". Per altre informazioni, vedere [stringa di connessione MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Codice di errore: AdlsGen2OperationFailed

- **Messaggio**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Motivo**: ADLS Gen2 genera l'errore che indica che l'operazione non è riuscita.

- **Raccomandazione**: controllare il messaggio di errore dettagliato generato da ADLS Gen2. Se il problema è causato da un errore temporaneo, riprovare. Per ulteriori informazioni, contattare il supporto di archiviazione di Azure e fornire l'ID richiesta nel messaggio di errore.

- **Motivo**: quando il messaggio di errore contiene ' Forbidden ', l'entità servizio o l'identità gestita utilizzata potrebbe non disporre di autorizzazioni sufficienti per accedere al ADLS Gen2.

- **Raccomandazione**: fare riferimento al documento della guida: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Motivo**: quando il messaggio di errore contiene ' InternalServerError ', l'errore viene restituito da ADLS Gen2.

- **Raccomandazione**: il problema potrebbe essere causato da un errore temporaneo. riprovare. Se il problema persiste, contattare il supporto di archiviazione di Azure e fornire l'ID richiesta nel messaggio di errore.


### <a name="error-code--adlsgen2invalidurl"></a>Codice di errore: AdlsGen2InvalidUrl

- **Messaggio**: `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Codice di errore: AdlsGen2InvalidFolderPath

- **Messaggio**: `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Codice di errore: AdlsGen2OperationFailedConcurrentWrite

- **Messaggio**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Codice di errore: AdlsGen2TimeoutError

- **Messaggio**: `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Archiviazione Azure Data Lake di prima generazione

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Messaggio di errore: il server remoto ha restituito un errore: (403) non consentito

- **Sintomi**: l'attività di copia ha esito negativo con l'errore seguente: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Motivo**: è possibile che l'entità servizio o l'identità gestita usata non disponga dell'autorizzazione per accedere a una cartella o a un file specifico.

- **Soluzione**: concedere le autorizzazioni corrispondenti per tutte le cartelle e le sottocartelle che è necessario copiare. Fare riferimento a [questo documento](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Messaggio di errore: non è stato possibile ottenere il token di accesso tramite l'entità servizio. Errore ADAL: service_unavailable

- **Sintomi**: l'attività di copia ha esito negativo con l'errore seguente:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Motivo**: quando il server dei token di servizio (STS) di proprietà di Azure Active Directory non è disponibile, ad esempio troppo occupato per gestire le richieste, viene restituito un errore HTTP 503. 

- **Soluzione**: ripetere l'attività di copia dopo alcuni minuti.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Data Warehouse/database SQL di Azure/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Codice di errore: SqlFailedToConnect

- **Messaggio**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Motivo**: se il messaggio di errore contiene "SqlException", il database SQL genera l'errore che indica che un'operazione specifica non è riuscita.

- **Raccomandazione**: per informazioni dettagliate, cercare in base al codice di errore SQL in questo documento di riferimento: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Per ulteriori informazioni, contattare il supporto SQL di Azure.

- **Motivo**: se il messaggio di errore contiene "client con indirizzo IP"... " non è consentito accedere al server e si sta provando a connettersi al database SQL di Azure, in genere è causato da un problema del firewall del database SQL di Azure.

- **Raccomandazione**: nella configurazione di Azure SQL Server Firewall abilitare l'opzione "Consenti ai servizi e alle risorse di Azure di accedere al server". Documentazione di riferimento: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Codice di errore: SqlOperationFailed

- **Messaggio**: `A database operation failed. Please search error to get more details.`

- **Motivo**: se il messaggio di errore contiene "SqlException", il database SQL genera l'errore che indica che un'operazione specifica non è riuscita.

- **Raccomandazione**: se l'errore SQL non è chiaro, provare a modificare il database con il livello di compatibilità più recente ' 150'. Può generare errori SQL più recenti della versione. Per informazioni dettagliate, vedere il documento https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat.
        Per la risoluzione dei problemi relativi a SQL, eseguire una ricerca in base al codice di errore SQL in questo documento di riferimento per altri dettagli: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Per ulteriori informazioni, contattare il supporto SQL di Azure.

- **Causa**: se il messaggio di errore contiene "PdwManagedToNativeInteropException", in genere è causato da una mancata corrispondenza tra le dimensioni delle colonne di origine e sink.

- **Raccomandazione**: controllare le dimensioni delle colonne di origine e sink. Per ulteriori informazioni, contattare il supporto SQL di Azure.

- **Causa**: se il messaggio di errore contiene "InvalidOperationException", in genere è causato da dati di input non validi.

- **Raccomandazione**: per identificare la riga in cui si è verificato il problema, abilitare la funzionalità tolleranza di errore nell'attività di copia, che può reindirizzare le righe problematiche alla risorsa di archiviazione per un'analisi più approfondita. Documentazione di riferimento: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Codice di errore: SqlUnauthorizedAccess

- **Messaggio**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa**: le credenziali non sono corrette oppure l'account di accesso non può accedere al database SQL.

- **Consiglio**: verificare che l'account di accesso disponga di autorizzazioni sufficienti per accedere al database SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Codice di errore: SqlOpenConnectionTimeout

- **Messaggio**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Cause**: potrebbe essere un errore temporaneo del database SQL.

- **Raccomandazione**: riprovare ad aggiornare la stringa di connessione del servizio collegato con un valore di timeout di connessione maggiore.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Codice di errore: SqlAutoCreateTableTypeMapFailed

- **Messaggio**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa**: la tabella di creazione automatica non può soddisfare i requisiti di origine.

- **Raccomandazione**: aggiornare il tipo di colonna in ' mapping ' o creare manualmente la tabella di sink nel server di destinazione.


### <a name="error-code--sqldatatypenotsupported"></a>Codice di errore: SqlDataTypeNotSupported

- **Messaggio**: `A database operation failed. Check the SQL errors.`

- **Causa**: se il problema si verifica nell'origine SQL e l'errore è correlato all'overflow di SqlDateTime, il valore dei dati è sull'intervallo del tipo di logica (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Raccomandazione**: eseguire il cast del tipo in stringa nella query SQL di origine o nel mapping delle colonne dell'attività di copia modificare il tipo di colonna in ' String '.

- **Causa**: se il problema si verifica nel sink SQL e l'errore è correlato all'overflow di SqlDateTime, il valore dei dati è sull'intervallo consentito nella tabella di sink.

- **Raccomandazione**: aggiornare il tipo di colonna corrispondente al tipo ' datetime2' nella tabella di sink.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Codice di errore: SqlInvalidDbStoredProcedure

- **Messaggio**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Motivo**: la stored procedure specificata non è valida. Il problema potrebbe essere causato dal fatto che il stored procedure non restituisce alcun dato.

- **Raccomandazione**: convalidare il stored procedure da strumenti SQL. Verificare che il stored procedure possa restituire i dati.


### <a name="error-code--sqlinvaliddbquerystring"></a>Codice di errore: SqlInvalidDbQueryString

- **Messaggio**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Motivo**: la query SQL specificata non è valida. La causa potrebbe essere la mancata restituzione di dati da parte della query

- **Raccomandazione**: convalidare la query SQL tramite strumenti SQL. Assicurarsi che la query possa restituire i dati.


### <a name="error-code--sqlinvalidcolumnname"></a>Codice di errore: SqlInvalidColumnName

- **Messaggio**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Cause**: Impossibile trovare la colonna. Possibile configurazione errata.

- **Raccomandazione**: verificare la colonna nella query,' Structure ' nel set di dati è mapping ' nell'attività.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Codice di errore: SqlColumnNameMismatchByCaseSensitive

- **Messaggio**: `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Codice di errore: SqlBatchWriteTimeout

- **Messaggio**: `Timeouts in SQL write operation.`

- **Cause**: potrebbe essere un errore temporaneo del database SQL.

- **Raccomandazione**: riprovare. Se si tratta di un problema, contattare il supporto SQL di Azure.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Codice di errore: SqlBatchWriteTransactionFailed

- **Messaggio**: `SQL transaction commits failed`

- **Motivo**: se i dettagli dell'eccezione indicano costantemente il timeout della transazione, la latenza di rete tra il runtime di integrazione e il database è superiore alla soglia predefinita come 30 secondi.

- **Raccomandazione**: aggiornare la stringa di connessione del servizio collegato SQL con il valore ' timeout connessione ' uguale a 120 o superiore e rieseguire l'attività.

- **Causa**: se i dettagli dell'eccezione indicano in modo intermittente la connessione SqlConnection, potrebbe trattarsi di un errore di rete temporaneo o di un problema lato database SQL

- **Raccomandazione**: ritentare l'attività ed esaminare le metriche laterali del database SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Codice di errore: SqlBulkCopyInvalidColumnLength

- **Messaggio**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa**: la copia bulk di SQL non è riuscita a causa della ricezione di una lunghezza di colonna non valida dal client bcp.

- **Raccomandazione**: per identificare la riga in cui si è verificato il problema, abilitare la funzionalità tolleranza di errore nell'attività di copia, che può reindirizzare le righe problematiche alla risorsa di archiviazione per un'analisi più approfondita. Documentazione di riferimento: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Codice di errore: SqlConnectionIsClosed

- **Messaggio**: `The connection is closed by SQL Database.`

- **Motivo**: la connessione SQL è stata chiusa dal database SQL quando la connessione a esecuzione simultanea e interruzione del server sono elevate.

- **Suggerimento**: il server remoto ha chiuso la connessione SQL. Riprova. Se si tratta di un problema, contattare il supporto SQL di Azure.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Codice di errore: SqlCreateTableFailedUnsupportedType

- **Messaggio**: `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Messaggio di errore: conversione non riuscita durante la conversione da una stringa di caratteri a uniqueidentifier

- **Sintomi**: quando si copiano dati da un'origine dati tabulare, ad esempio SQL Server, in Azure SQL data warehouse usando la copia di staging e la polibase, viene raggiunto l'errore seguente:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Motivo**: Azure SQL data warehouse polibase non è in grado di convertire una stringa vuota in GUID.

- **Soluzione**: nel sink dell'attività di copia, in impostazioni di base, impostare l'opzione "**Usa tipo predefinito**" su false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Messaggio di errore: tipo di dati previsto: DECIMAL (x, x), valore offensivo

- **Sintomi**: quando si copiano dati da un'origine dati tabulare, ad esempio SQL Server, in SQL DW usando la copia di staging e la polibase, viene raggiunto l'errore seguente:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Motivo**: Azure SQL data warehouse polibase non può inserire una stringa vuota (valore null) in una colonna decimale.

- **Soluzione**: nel sink dell'attività di copia, in impostazioni di base, impostare l'opzione "**Usa tipo predefinito**" su false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Messaggio di errore: messaggio eccezione Java: HdfsBridge:: CreateRecordReader

- **Sintomi**: i dati vengono copiati in Azure SQL data warehouse usando la polibase e viene raggiunto l'errore seguente:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Motivo**: la possibile cause è che lo schema (larghezza totale della colonna) è troppo grande (maggiore di 1 MB). Controllare lo schema della tabella SQL DW di destinazione aggiungendo la dimensione di tutte le colonne:

    - Int-> 4 byte
    - Bigint-> 8 byte
    - Varchar (n), char (n), Binary (n), varbinary (n)-> n byte
    - Nvarchar (n), nchar (n)-> n * 2 byte
    - Data-> 6 byte
    - DateTime/(2), smalldatetime-> 16 byte
    - DateTimeOffset-> 20 byte
    - Decimale-> 19 byte
    - Float-> 8 byte
    - Money-> 8 byte
    - Smallmoney-> 4 byte
    - Real-> 4 byte
    - Smallint-> 2 byte
    - Tempo-> 12 byte
    - Tinyint-> 1 byte

- **Soluzione**: ridurre la larghezza della colonna in modo che sia minore di 1 MB

- In alternativa, usare l'approccio BULK INSERT disabilitando la funzionalità di base

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Messaggio di errore: la condizione specificata con le intestazioni condizionali HTTP non è stata soddisfatta

- **Sintomi**: si usa la query SQL per estrarre i dati da Azure SQL data warehouse e si raggiunge l'errore seguente:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Causa**: si è verificata Azure SQL data warehouse problema durante l'esecuzione di query sulla tabella esterna in archiviazione di Azure.

- **Soluzione**: eseguire la stessa query in SSMS e verificare se viene visualizzato lo stesso risultato. In caso affermativo, aprire un ticket di supporto per Azure SQL Data Warehouse e fornire il nome del server e del database SQL DW per procedere con la risoluzione dei problemi.
            

## <a name="delimited-text-format"></a>Formato testo delimitato

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Codice di errore: DelimitedTextColumnNameNotAllowNull

- **Messaggio**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Motivo**: quando si imposta ' firstRowAsHeader ' nell'attività, la prima riga verrà utilizzata come nome di colonna. Questo errore indica che la prima riga contiene un valore vuoto. Ad esempio: "Columna,, ColumnB".

- **Raccomandazione**: controllare la prima riga e correggere il valore se è presente un valore vuoto.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Codice di errore: DelimitedTextMoreColumnsThanDefined

- **Messaggio**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Motivo**: il numero di colonne della riga problematica è elevato rispetto al numero di colonne della prima riga. La causa potrebbe essere un problema di dati o un delimitatore di colonna/delimitatore di colonna non corretto.

- **Raccomandazione**: per ottenere il numero di righe nel messaggio di errore, controllare la colonna della riga e correggere i dati.

- **Causa**: se il numero di colonne previsto è "1" nel messaggio di errore, è possibile che siano state specificate impostazioni di compressione o di formato errate, che hanno causato l'analisi errata dei file da parte di ADF.

- **Consiglio**: controllare le impostazioni di formato per assicurarsi che corrispondano ai file di origine.

- **Causa**: se l'origine è una cartella, è possibile che i file nella cartella specificata abbiano uno schema diverso.

- **Raccomandazione**: assicurarsi che i file nella cartella specificata abbiano lo stesso schema.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Codice di errore: DelimitedTextIncorrectRowDelimiter

- **Messaggio**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Codice di errore: DelimitedTextTooLargeColumnCount

- **Messaggio**: `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Codice di errore: DelimitedTextInvalidSettings

- **Messaggio**: `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Codice di errore: DynamicsCreateServiceClientError

- **Messaggio**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Causa**: si tratta di un problema temporaneo sul lato Dynamics server.

- **Raccomandazione**: eseguire di nuovo la pipeline. Se non si riesce, provare a ridurre il parallelismo. Se l'errore persiste, contattare il supporto tecnico di Dynamics.



## <a name="json-format"></a>Formato JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Codice di errore: JsonInvalidArrayPathDefinition

- **Messaggio**: `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Codice di errore: JsonEmptyJObjectData

- **Messaggio**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Codice di errore: JsonNullValueInPathDefinition

- **Messaggio**: `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Codice di errore: JsonUnsupportedHierarchicalComplexValue

- **Messaggio**: `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Codice di errore: JsonConflictPartitionDiscoverySchema

- **Messaggio**: `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Codice di errore: JsonInvalidDataFormat

- **Messaggio**: `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Codice di errore: JsonInvalidDataMixedArrayAndObject

- **Messaggio**: `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Formato parquet

### <a name="error-code--parquetjavainvocationexception"></a>Codice di errore: ParquetJavaInvocationException

- **Messaggio**: `An error occurred when invoking java, message: %javaException;.`

- **Causa**: quando il messaggio di errore contiene ' Java. lang. OutOfMemory ',' Java heap space ' è doubleCapacity ', in genere si tratta di un problema di gestione della memoria nella versione precedente di Integration Runtime.

- **Suggerimento**: se si usa Integration Runtime self-hosted e la versione è precedente a 3.20.7159.1, suggerire di eseguire l'aggiornamento alla versione più recente.

- **Motivo**: quando il messaggio di errore contiene ' Java. lang. OutOfMemory ', il runtime di integrazione non dispone di risorse sufficienti per elaborare i file.

- **Raccomandazione**: limitare le esecuzioni simultanee nel runtime di integrazione. Per Integration Runtime indipendenti, passare a un computer potente con memoria uguale o maggiore di 8 GB.

- **Motivo**: se il messaggio di errore contiene ' NullPointerReference ', è possibile che si verifichi un errore temporaneo.

- **Raccomandazione**: riprovare. Se il problema persiste, contattare il supporto tecnico.


### <a name="error-code--parquetinvalidfile"></a>Codice di errore: ParquetInvalidFile

- **Messaggio**: `File is not a valid parquet file.`

- **Causa**: problema di file parquet.

- **Raccomandazione**: controllare che l'input sia un file parquet valido.


### <a name="error-code--parquetnotsupportedtype"></a>Codice di errore: ParquetNotSupportedType

- **Messaggio**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Motivo**: il formato parquet non è supportato in Azure Data Factory.

- **Raccomandazione**: controllare i dati di origine. Fare riferimento al documento: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Codice di errore: ParquetMissedDecimalPrecisionScale

- **Messaggio**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Motivo**: provare ad analizzare la precisione e la scala dei numeri, ma non vengono fornite informazioni di questo tipo.

- **Raccomandazione**:' source ' non restituisce precisione e scala corrette. Controllare la precisione e la scala della colonna del problema.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Codice di errore: ParquetInvalidDecimalPrecisionScale

- **Messaggio**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Motivo**: lo schema non è valido.

- **Consiglio**: controllare la precisione e la scala della colonna del problema.


### <a name="error-code--parquetcolumnnotfound"></a>Codice di errore: ParquetColumnNotFound

- **Messaggio**: `Column %column; does not exist in Parquet file.`

- **Causa**: lo schema di origine non corrisponde allo schema del sink.

- **Raccomandazione**: controllare the'mappings ' in ' Activity '. Verificare che sia possibile eseguire il mapping della colonna di origine alla colonna di sink a destra.


### <a name="error-code--parquetinvaliddataformat"></a>Codice di errore: ParquetInvalidDataFormat

- **Messaggio**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: i dati non possono essere convertiti nel tipo specificato nei mapping. origine

- **Raccomandazione**: controllare i dati di origine o specificare il tipo di dati corretto per la colonna nel mapping delle colonne dell'attività di copia. Fare riferimento al documento: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Codice di errore: ParquetDataCountNotMatchColumnCount

- **Messaggio**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa**: numero di colonne di origine e numero di colonne sink non corrispondenti

- **Raccomandazione**: il numero di colonne di origine del controllo doppio è uguale al numero di colonne del sink in ' mapping '.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Codice di errore: ParquetDataTypeNotMatchColumnType

- **Messaggio**: tipo di dati% srcType; non corrisponde al tipo di colonna specificato% dstType; nella colonna '% columnIndex;'.

- **Causa**: i dati dell'origine non possono essere convertiti in tipi definiti nel sink

- **Raccomandazione**: specificare un tipo corretto in mapping. sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Codice di errore: ParquetBridgeInvalidData

- **Messaggio**: `%message;`

- **Motivo**: valore dei dati oltre la limitazione

- **Raccomandazione**: riprovare. Se il problema persiste, contattaci.


### <a name="error-code--parquetunsupportedinterpretation"></a>Codice di errore: ParquetUnsupportedInterpretation

- **Messaggio**: `The given interpretation '%interpretation;' of parquet format is not supported.`

- **Cause**: scenario non supportato

- **Raccomandazione**:' ParquetInterpretFor ' non deve essere ' sparkSql '.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Codice di errore: ParquetUnsupportFileLevelCompressionOption

- **Messaggio**: `File level compression is not supported for Parquet.`

- **Cause**: scenario non supportato

- **Raccomandazione**: rimuovere ' CompressionType ' nel payload.



## <a name="general-copy-activity-error"></a>Errore di attività di copia generale

### <a name="error-code--jrenotfound"></a>Codice di errore: JreNotFound

- **Messaggio**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Motivo**: il runtime di integrazione self-hosted non riesce a trovare il runtime Java. Il runtime Java è necessario per la lettura di un'origine particolare.

- **Consiglio**: controllare l'ambiente di runtime di integrazione, la documentazione di riferimento: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Codice di errore: WildcardPathSinkNotSupported

- **Messaggio**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Motivo**: il set di dati sink non supporta caratteri jolly.

- **Raccomandazione**: controllare il set di dati sink e correggere il percorso senza il valore jolly.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Codice di errore: MappingInvalidPropertyWithEmptyValue

- **Messaggio**: `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Codice di errore: MappingInvalidPropertyWithNamePathAndOrdinal

- **Messaggio**: `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Codice di errore: MappingDuplicatedOrdinal

- **Messaggio**: `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Codice di errore: MappingInvalidOrdinalForSinkColumn

- **Messaggio**: `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video su Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)
            
