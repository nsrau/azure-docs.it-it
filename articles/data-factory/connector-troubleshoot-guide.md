---
title: Risolvere i problemi dei connettori di Azure Data Factory
description: Informazioni su come risolvere i problemi relativi ai connettori in Azure Data Factory.Learn how to troubleshoot connector issues in Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778227"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Risolvere i problemi dei connettori di Azure Data Factory

Questo articolo illustra i metodi di risoluzione dei problemi comuni per i connettori in Azure Data Factory.This article explores common troubleshooting methods for connectors in Azure Data Factory.
  

## <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

### <a name="error-code--azurebloboperationfailed"></a>Codice di errore: AzureBlobOperationFailedError code: AzureBlobOperationFailed

- **Messaggio**:`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa:** problema dell'operazione di archiviazione BLOB.

- **Raccomandazione**: Controllare l'errore nei dettagli. Fare riferimento al https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codesdocumento della Guida BLOB: . Se necessario, contattare il team di archiviazione.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Codice di errore: AzureBlobServiceNotReturnExpectedDataLength

- **Messaggio**:`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Codice di errore: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Messaggio**:`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Codice di errore: AzureStorageOperationFailedConcurrentWrite

- **Messaggio**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Messaggio di errore: La dimensione della richiesta è troppo grande

- **Sintomi:** si copiano i dati nel database Cosmos di Azure con la dimensione predefinita del batch di scrittura e viene visualizzato l'errore *" La dimensione della richiesta è troppo**grande**" .*

- **Causa**: Cosmos DB limita le dimensioni di una singola richiesta a 2 MB. La formula è Dimensione richiesta , Dimensione documento singolo , Dimensioni batch di scrittura. Se le dimensioni del documento sono grandi, il comportamento predefinito comporterà dimensioni troppo grandi per le richieste. È possibile ottimizzare la dimensione del batch di scrittura.

- **Risoluzione**: Nel sink di attività di copia, ridurre il valore 'Write batch size' (il valore predefinito è 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Messaggio di errore: Violazione del vincolo di indice univoco

- **Sintomi**: Quando si copiano dati in Cosmos DB, si verifica il seguente errore:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Causa**: Ci sono due possibili cause:

    - Se si utilizza **Inserisci** come comportamento di scrittura, questo errore indica che i dati di origine hanno righe/oggetti con lo stesso ID.

    - Se si utilizza **Upsert** come comportamento di scrittura e si imposta un'altra chiave univoca per il contenitore, questo errore indica che i dati di origine hanno righe/oggetti con ID diversi ma lo stesso valore per la chiave univoca definita.

- **Soluzione**: 

    - Per la causa1, impostare **Upsert** come comportamento di scrittura.
    - Per la causa 2, assicurarsi che ogni documento abbia un valore diverso per la chiave univoca definita.

### <a name="error-message-request-rate-is-large"></a>Messaggio di errore: Frequenza richieste è elevato

- **Sintomi**: Quando si copiano dati in Cosmos DB, si verifica il seguente errore:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Causa**: Le unità di richiesta utilizzate sono maggiori dell'UR disponibile configurata in Cosmos DB. Scopri come Cosmos DB calcola RU da [qui](../cosmos-db/request-units.md#request-unit-considerations).

- **Risoluzione**: Di seguito sono riportate due soluzioni:

    1. **Aumentare il contenitore RU** a un valore maggiore in Cosmos DB, che migliorerà le prestazioni dell'attività di copia, anche se incorrere in più costi in Cosmos DB. 

    2. Ridurre **writeBatchSize** a un valore inferiore (ad esempio 1000) e impostare **parallelCopies** su un valore inferiore, ad esempio 1, che renderà le prestazioni di esecuzione della copia peggiori di quelle correnti, ma non subirà più costi in Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Colonna mancante nel mapping delle colonneColumn missing in column mapping

- **Sintomi:** quando si importa lo schema per Cosmos DB per il mapping delle colonne, alcune colonne sono mancanti. 

- **Causa**: ADF deduce lo schema dai primi 10 documenti Cosmos DB. Se alcune colonne/proprietà non hanno valore in tali documenti, non verranno rilevate da ADF, pertanto non verranno visualizzate.

- **Soluzione**: È possibile ottimizzare la query come indicato di seguito per applicare la colonna da visualizzare nel set di risultati con valore vuoto: (presupponendo che la colonna "impossibile" non sia presente nei primi 10 documenti). In alternativa, è possibile aggiungere manualmente la colonna per il mapping.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Messaggio di errore: Il GuidRepresentation per il lettore è CSharpLegacy

- **Sintomi**: Quando si copiano dati da Cosmos DB MongoAPI/MongoDB con campo UUID, viene visualizzato il seguente errore:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Causa**: Esistono due modi per rappresentare UUID in BSON - UuidStardard e UuidLegacy. Per impostazione predefinita, UuidLegacy viene utilizzato per leggere i dati. Si verifica un errore se i dati UUID in MongoDB sono UuidStandard.You will hit error if your UUID data in MongoDB is UuidStandard.

- **Risoluzione**: Nella stringa di connessione MongoDB, aggiungere l'opzione "**uuidRepresentation ,standard**". Per ulteriori informazioni, vedere [Stringa di connessione MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Codice di errore: AdlsGen2OperationFailed

- **Messaggio**:`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causa**: ADLS Gen2 genera l'errore che indica che l'operazione non è riuscita.

- **Consiglio:** controllare il messaggio di errore dettagliato generato da ADLS Gen2. Se è causato da un errore temporaneo, riprovare. Per ulteriori informazioni, contattare il supporto di Archiviazione di Azure e fornire l'ID richiesta nel messaggio di errore.

- **Causa**: quando il messaggio di errore contiene 'Accesso negato', l'entità servizio o l'identità gestita in uso potrebbe non disporre di autorizzazioni sufficienti per accedere ad ADLS Gen2.

- **Raccomandazione**: Fare riferimento https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authenticational documento di aiuto: .

- **Causa:** quando il messaggio di errore contiene 'InternalServerError', l'errore viene restituito da ADLS Gen2.

- **Raccomandazione:** può essere causato da un errore temporaneo, riprovare. Se il problema persiste, contattare il supporto di Archiviazione di Azure e fornire l'ID della richiesta nel messaggio di errore.


### <a name="error-code--adlsgen2invalidurl"></a>Codice di errore: AdlsGen2InvalidUrl

- **Messaggio**:`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Codice di errore: AdlsGen2InvalidFolderPath

- **Messaggio**:`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Codice di errore: AdlsGen2OperationFailedConcurrentWrite

- **Messaggio**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Codice di errore: AdlsGen2TimeoutError

- **Messaggio**:`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Messaggio di errore: Il server remoto ha restituito un errore: (403) Accesso negato

- **Sintomi**: L'attività di copia non riesce con il seguente errore: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Causa:** una possibile causa è che l'entità servizio o l'identità gestita utilizzata non dispone dell'autorizzazione per accedere a una determinata cartella/file.

- **Risoluzione**: Concedere le autorizzazioni corrispondenti per tutte le cartelle e sottocartelle che è necessario copiare. Fare riferimento a [questo documento](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Messaggio di errore: Impossibile ottenere il token di accesso utilizzando l'entità servizio. Errore ADAL: service_unavailable

- **Sintomi**: L'attività di copia non riesce con il seguente errore:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Causa:** quando il server token di servizio (STS) di proprietà di Azure Active Directory non è disponibile, ovvero troppo occupato per gestire le richieste, restituisce un errore HTTP 503. 

- **Soluzione**: Eseguire nuovamente l'attività di copia dopo alcuni minuti.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Data Warehouse/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Codice di errore: SqlFailedToConnect

- **Messaggio**:`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Causa**: se il messaggio di errore contiene "SqlException", il database SQL genera l'errore che indica un'operazione specifica non riuscita.

- **Consiglio**: Si prega di cercare per codice https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorsdi errore SQL in questo documento di riferimento per ulteriori dettagli: . Per ulteriore assistenza, contattare il supporto SQL di Azure.If you need further help, contact Azure SQL support.

- **Causa**: Se il messaggio di errore contiene "Client con indirizzo IP '...' non è consentito accedere al server", e si sta tentando di connettersi al Database SQL di Azure, in genere è causato da un problema del firewall del database SQL di Azure.Is not allowed to access the server", and you are trying to connect to Azure SQL Database, usually it is caused by Azure SQL Database firewall issue.

- **Consiglio:** nella configurazione del firewall di SQL Server di Azure abilitare l'opzione "Consenti ai servizi e alle risorse di Azure di accedere a questo server". Documento di https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configureriferimento: .


### <a name="error-code--sqloperationfailed"></a>Codice di errore: SqlOperationFailed

- **Messaggio**:`A database operation failed. Please search error to get more details.`

- **Causa**: se il messaggio di errore contiene "SqlException", il database SQL genera l'errore che indica un'operazione specifica non riuscita.

- **Consiglio:** se l'errore SQL non è chiaro, provare a modificare il database al livello di compatibilità più recente '150'. Può generare errori SQL dell'ultima versione. Si prega di https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompatfare riferimento al documento di dettaglio: .
        Per la risoluzione dei problemi SQL, cercare per codice https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorsdi errore SQL in questo documento di riferimento per ulteriori dettagli: . Per ulteriore assistenza, contattare il supporto SQL di Azure.If you need further help, contact Azure SQL support.

- **Causa**: se il messaggio di errore contiene "PdwManagedToNativeInteropException", in genere è causato da una mancata corrispondenza tra le dimensioni delle colonne di origine e sink.

- **Consiglio:** controllare le dimensioni delle colonne di origine e sink. Per ulteriore assistenza, contattare il supporto SQL di Azure.If you need further help, contact Azure SQL support.

- **Causa**: se il messaggio di errore contiene "InvalidOperationException", in genere è causato da dati di input non validi.

- **Consiglio:** per identificare quale riga rileva il problema, abilitare la funzionalità di tolleranza di errore sull'attività di copia, che può reindirizzare le righe problematiche all'archiviazione per ulteriori indagini. Documento di https://docs.microsoft.com/azure/data-factory/copy-activity-fault-toleranceriferimento: .


### <a name="error-code--sqlunauthorizedaccess"></a>Codice di errore: SqlUnauthorizedAccess

- **Messaggio**:`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa:** le credenziali non sono corrette o l'account di accesso non può accedere al database SQL.

- **Consiglio:** verificare che l'account di accesso disponga di autorizzazioni sufficienti per accedere al database SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Codice di errore: SqlOpenConnectionTimeout

- **Messaggio**:`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa:** potrebbe trattarsi di un errore temporaneo del database SQL.

- **Consiglio:** riprovare ad aggiornare la stringa di connessione del servizio collegato con un valore di timeout di connessione maggiore.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Codice di errore: SqlAutoCreateTableTypeMapFailed

- **Messaggio**:`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa:** la tabella di creazione automatica non può soddisfare il requisito di origine.

- **Consiglio:** aggiornare il tipo di colonna in 'mapping' o creare manualmente la tabella sink nel server di destinazione.


### <a name="error-code--sqldatatypenotsupported"></a>Codice di errore: SqlDataTypeNotSupported

- **Messaggio**:`A database operation failed. Check the SQL errors.`

- **Causa**: se il problema si verifica nell'origine SQL e l'errore è correlato all'overflow di SqlDateTime, il valore dei dati è oltre l'intervallo del tipo di logica (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Consiglio:** eseguire il cast del tipo su stringa nella query SQL di origine o nel mapping delle colonne dell'attività di copia modificare il tipo di colonna in 'String'.

- **Causa:** se il problema si verifica nel sink SQL e l'errore è correlato all'overflow di SqlDateTime, il valore dei dati supera l'intervallo consentito nella tabella sink.

- **Consiglio:** aggiornare il tipo di colonna corrispondente al tipo 'datetime2' nella tabella sink.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Codice di errore: SqlInvalidDbStoredProcedure

- **Messaggio**:`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa**: la stored procedure specificata non è valida. Potrebbe essere causato dal fatto che la stored procedure non restituisce dati.

- **Consiglio:** convalidare la stored procedure tramite SQL Tools. Assicurarsi che la stored procedure possa restituire dati.


### <a name="error-code--sqlinvaliddbquerystring"></a>Codice di errore: SqlInvalidDbQueryString

- **Messaggio**:`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa:** la query SQL specificata non è valida. Potrebbe essere causato dal fatto che la query non restituisce dati

- **Consiglio:** convalidare la query SQL tramite gli strumenti SQL. Assicurarsi che la query possa restituire dati.


### <a name="error-code--sqlinvalidcolumnname"></a>Codice di errore: SqlInvalidColumnName

- **Messaggio**:`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa**: Impossibile trovare la colonna. Possibile configurazione errata.

- **Consiglio:** verificare la colonna nella query, 'struttura' nel set di dati e 'mapping' nell'attività.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Codice di errore: SqlColumnNameMismatchByCaseSensitive

- **Messaggio**:`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Codice di errore: SqlBatchWriteTimeout

- **Messaggio**:`Timeouts in SQL write operation.`

- **Causa:** potrebbe trattarsi di un errore temporaneo del database SQL.

- **Consiglio**: Riprovare. Se il problema viene ripro, contattare il supporto SQL di Azure.If problem repro, contact Azure SQL support.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Codice di errore: SqlBatchWriteTransactionFailed

- **Messaggio**:`SQL transaction commits failed`

- **Causa:** se i dettagli dell'eccezione indicano costantemente il timeout della transazione, la latenza di rete tra il runtime di integrazione e il database è superiore alla soglia predefinita come 30 secondi.

- **Consiglio:** aggiornare la stringa di connessione del servizio collegato Sql con il valore 'timeout di connessione' uguale a 120 o superiore ed eseguire nuovamente l'attività.

- **Causa**: se i dettagli dell'eccezione indicano in modo discontinuo sqlconnection interrotto, potrebbe trattarsi solo di un errore di rete temporaneo o di un problema sul lato del database SQL

- **Consiglio:** ripetere l'attività ed esaminare le metriche lato database SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Codice di errore: SqlBulkCopyInvalidColumnLength

- **Messaggio**:`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa:** la copia bulk SQL non è riuscita a causa della ricezione di una lunghezza di colonna non valida dal client bcp.

- **Consiglio:** per identificare quale riga rileva il problema, abilitare la funzionalità di tolleranza di errore sull'attività di copia, che può reindirizzare le righe problematiche all'archiviazione per ulteriori indagini. Documento di https://docs.microsoft.com/azure/data-factory/copy-activity-fault-toleranceriferimento: .


### <a name="error-code--sqlconnectionisclosed"></a>Codice di errore: SqlConnectionIsClosed

- **Messaggio**:`The connection is closed by SQL Database.`

- **Causa:** la connessione SQL viene chiusa dal database SQL quando l'esecuzione simultanea elevata e la connessione di terminazione del server.

- **Consiglio:** il server remoto ha chiuso la connessione SQL. Riprovare. Se il problema viene ripro, contattare il supporto SQL di Azure.If problem repro, contact Azure SQL support.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Codice di errore: SqlCreateTableFailedUnsupportedType

- **Messaggio**:`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Messaggio di errore: Conversione non riuscita durante la conversione da una stringa di caratteri a uniqueidentifier

- **Sintomi:** quando si copiano dati da un'origine dati tabulare (ad esempio SQL Server) in Azure SQL Data Warehouse usando la copia in fasi e PolyBase, viene visualizzato l'errore seguente:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Causa:** Azure SQL Data Warehouse PolyBase non può convertire una stringa vuota in GUID.

- **Risoluzione**: In Copy activity sink, in Polybase settings, impostare "**use default**" option su false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Messaggio di errore: Tipo di dati previsto: DECIMAL(x,x), valore che non può essere visualizzato

- **Sintomi:** quando si copiano dati da un'origine dati tabulare (ad esempio SQL Server) in SQL DW utilizzando la copia in fasi e PolyBase, viene visualizzato il seguente errore:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Causa:** Azure SQL Data Warehouse Polybase non può inserire una stringa vuota (valore null) nella colonna decimale.

- **Risoluzione**: In Copy activity sink, in Polybase settings, impostare "**use default**" option su false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Messaggio di errore: Messaggio eccezione Java:HdfsBridge::CreateRecordReader

- **Sintomi:** copiare i dati in Azure SQL Data Warehouse usando PolyBase e premere il seguente errore:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Causa**: La causa possibile è che lo schema (larghezza totale della colonna) sia troppo grande (maggiore di 1 MB). Controllare lo schema della tabella SQL DW di destinazione aggiungendo le dimensioni di tutte le colonne:

    - Int -> 4 byte
    - Bigint -> 8 byte
    - Varchar(n),char(n),binary(n), varbinary(n) -> n byte
    - Nvarchar(n), nchar(n) -> n
    - Data -> 6 byte
    - Datetime/(2), smalldatetime -> 16 byte
    - Datetimeoffset -> 20 byte
    - Decimale -> 19 byte
    - Float -> 8 byte
    - Denaro -> 8 byte
    - Smallmoney -> 4 byte
    - Reale -> 4 byte
    - Smallint -> 2 byte
    - Tempo -> 12 byte
    - Tinyint -> 1 byte

- **Soluzione**: Ridurre la larghezza delle colonne a meno di 1 MB

- Oppure utilizzare l'approccio di inserimento bulk disabilitando Polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Messaggio di errore: La condizione specificata utilizzando le intestazioni condizionali HTTP non è soddisfatta

- **Sintomi:** si usa la query SQL per estrarre i dati da Azure SQL Data Warehouse e viene visualizzato l'errore seguente:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Causa:** Azure SQL Data Warehouse ha riscontrato l'esecuzione di query sulla tabella esterna in Archiviazione di Azure.For: Azure SQL Data Warehouse hit issue querying the external table in Azure Storage.

- **Risoluzione**: eseguire la stessa query in SSMS e verificare se viene visualizzato lo stesso risultato. In caso affermativo, aprire un ticket di supporto per Azure SQL Data Warehouse e fornire il nome del server e del database SQL DW per procedere con la risoluzione dei problemi.
            

## <a name="delimited-text-format"></a>Formato testo delimitato

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Codice di errore: DelimitedTextColumnNameNotAllowNull

- **Messaggio**:`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa:** quando si imposta 'firstRowAsHeader' nell'attività, la prima riga verrà utilizzata come nome di colonna. Questo errore indica che la prima riga contiene un valore vuoto. Ad esempio: 'ColumnA,,ColumnB'.

- **Consiglio**: Controllare la prima riga e correggere il valore se è presente un valore vuoto.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Codice di errore: DelimitedTextMoreColumnsThanDefined

- **Messaggio**:`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Causa:** il conteggio delle colonne della riga problematica è elevato rispetto al conteggio delle colonne della prima riga. Può essere causato da problemi di dati o impostazioni non corrette di delimite di colonna/caratteri di citazione.

- **Raccomandazione**: Si prega di ottenere il conteggio delle righe nel messaggio di errore, controllare la colonna della riga e correggere i dati.

- **Causa**: se il numero di colonne previsto è "1" nel messaggio di errore, è possibile che sia stata specificata una compressione o impostazioni di formato errate, che ha causato ADF di analizzare erroneamente i file.

- **Raccomandazione:** controllare le impostazioni del formato per assicurarsi che corrisponda ai file di origine.

- **Causa**: se l'origine è una cartella, è possibile che i file nella cartella specificata abbiano uno schema diverso.

- **Consiglio:** Assicurarsi che i file nella cartella specificata abbiano uno schema identico.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Codice di errore: DelimitedTextIncorrectRowDelimiter

- **Messaggio**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Codice di errore: DelimitedTextTooLargeColumnCount

- **Messaggio**:`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Codice di errore: DelimitedTextInvalidSettings

- **Messaggio**:`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Codice di errore: DynamicsCreateServiceClientError

- **Messaggio**:`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Causa:** si tratta di un problema temporaneo sul lato server Dynamics.

- **Consiglio:** eseguire nuovamente la pipeline. Se continuare a fallire, provare a ridurre il parallelismo. Se ancora non riescono, si prega di contattare il supporto dinamiche.



## <a name="json-format"></a>Formato JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Codice di errore: JsonInvalidArrayPathDefinition

- **Messaggio**:`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Codice di errore: JsonEmptyJObjectData

- **Messaggio**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Codice di errore: JsonNullValueInPathDefinition

- **Messaggio**:`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Codice di errore: JsonUnsupportedHierarchicalComplexValue

- **Messaggio**:`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Codice di errore: JsonConflictPartitionDiscoverySchema

- **Messaggio**:`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Codice di errore: JsonInvalidDataFormat

- **Messaggio**:`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Codice di errore: JsonInvalidDataMixedArrayAndObject

- **Messaggio**:`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Formato parquet

### <a name="error-code--parquetjavainvocationexception"></a>Codice di errore: ParquetJavaInvocationException

- **Messaggio**:`An error occurred when invoking java, message: %javaException;.`

- **Causa:** quando il messaggio di errore contiene 'java.lang.OutOfMemory', 'Spazio heap Java' e 'doubleCapacity', in genere si tratta di un problema di gestione della memoria nella versione precedente del runtime di integrazione.

- **Consiglio:** se si utilizza Self-hosted Integration Runtime e la versione è precedente alla 3.20.7159.1, suggerire di eseguire l'aggiornamento alla versione più recente.

- **Causa:** quando il messaggio di errore contiene 'java.lang.OutOfMemory', il runtime di integrazione non dispone di risorse sufficienti per elaborare i file.

- **Consiglio:** limitare le esecuzioni simultanee nel runtime di integrazione. Per Self-hosted Integration Runtime, scalabilità verticale a un computer potente con memoria uguale o superiore a 8 GB.

- **Causa:** quando il messaggio di errore contiene 'NullPointerReference', è possibile che si tratti di un errore temporaneo.

- **Consiglio**: Riprovare. Se il problema persiste, contattare il supporto tecnico.


### <a name="error-code--parquetinvalidfile"></a>Codice di errore: ParquetInvalidFile

- **Messaggio**:`File is not a valid parquet file.`

- **Causa**: Problema di file di parquet.

- **Raccomandazione**: Controllare che l'input sia un file di parquet valido.


### <a name="error-code--parquetnotsupportedtype"></a>Codice di errore: ParquetNotSupportedType

- **Messaggio**:`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa:** il formato del parquet non è supportato in Azure Data Factory.

- **Consiglio**: Controllare i dati di origine. Fare riferimento al https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecsdocumento: .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Codice di errore: ParquetMissedDecimalPrecisionScale

- **Messaggio**:`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa**: provare ad analizzare la precisione e la scala del numero, ma non vengono fornite informazioni di questo tipo.

- **Raccomandazione:**'Source' non restituisce Precisione e scala corrette. Controllare la precisione e la scala della colonna del problema.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Codice di errore: ParquetInvalidDecimalPrecisionScale

- **Messaggio**:`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa:** lo schema non è valido.

- **Consiglio**: Controllare la precisione e la scala della colonna del problema.


### <a name="error-code--parquetcolumnnotfound"></a>Codice di errore: ParquetColumnNotFound

- **Messaggio**:`Column %column; does not exist in Parquet file.`

- **Causa:** lo schema di origine non corrisponde allo schema sink.

- **Raccomandazione:** controllare i "mapping" in 'attività'. Assicurarsi che la colonna di origine possa essere mappata alla colonna sink destra.


### <a name="error-code--parquetinvaliddataformat"></a>Codice di errore: ParquetInvalidDataFormat

- **Messaggio**:`Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: I dati non possono essere convertiti nel tipo specificato in mappings.source

- **Consiglio:** controllare i dati di origine o specificare il tipo di dati corretto per questa colonna nel mapping delle colonne dell'attività di copia. Fare riferimento al https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecsdocumento: .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Codice di errore: ParquetDataCountNotMatchColumnCount

- **Messaggio**:`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa:** il conteggio delle colonne di origine e il conteggio delle colonne sink non corrispondono

- **Consiglio:** il conteggio delle colonne di origine a doppio controllo è lo stesso conteggio delle colonne sink in 'mapping'.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Codice di errore: ParquetDataTypeNotMatchColumnType

- **Messaggio**: Il tipo di dati %srcType; non corrisponde al tipo di colonna specificato %dstType; nella colonna '%columnIndex;'.

- **Causa:** i dati dall'origine non possono essere convertiti in tipi definiti nel sink

- **Consiglio:** specificare un tipo corretto in mapping.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Codice di errore: ParquetBridgeInvalidData

- **Messaggio**:`%message;`

- **Causa**: Valore dei dati rispetto alla limitazione

- **Consiglio**: Riprovare. Se il problema persiste, contattaci.


### <a name="error-code--parquetunsupportedinterpretation"></a>Codice di errore: ParquetUnsupportedInterpretation

- **Messaggio**:`The given interpretation '%interpretation;' of parquet format is not supported.`

- **Causa**: Scenario non supportato

- **Raccomandazione:**'ParquetInterpretFor' non deve essere 'sparkSql'.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Codice di errore: ParquetUnsupportFileLevelCompressionOption

- **Messaggio**:`File level compression is not supported for Parquet.`

- **Causa**: Scenario non supportato

- **Consiglio:** rimuovere 'CompressionType' nel payload.



## <a name="general-copy-activity-error"></a>Errore generale dell'attività di copia

### <a name="error-code--jrenotfound"></a>Codice di errore: JreNotFound

- **Messaggio**:`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa**: Il runtime di integrazione self-hosted non è in grado di trovare Java Runtime. Il runtime Java è necessario per la lettura di una determinata origine.

- **Consiglio:** controllare l'ambiente di runtime dell'integrazione, il documento di riferimento:Recommendation: Check your integration runtime environment, the reference doc:https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Codice di errore: WildcardPathSinkNotSupported

- **Messaggio**:`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa:** il set di dati sink non supporta i caratteri jolly.

- **Consiglio:** controllare il set di dati sink e correggere il percorso senza il valore con caratteri jolly.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Codice di errore: MappingInvalidPropertyWithEmptyValue

- **Messaggio**:`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Codice di errore: MappingInvalidPropertyWithNamePathAndOrdinal

- **Messaggio**:`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Codice di errore: MappingDuplicatedOrdinal

- **Messaggio**:`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Codice di errore: MappingInvalidOrdinalForSinkColumn

- **Messaggio**:`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, prova queste risorse:For more troubleshooting help, try these resources:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data FactoryData Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum sull'overflow dello stack per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
            
