---
title: Risolvere i problemi dei connettori di Azure Data Factory
description: Informazioni su come risolvere i problemi relativi ai connettori in Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 2e54c0b09c3dbe398b0522d0ad9ad2314e29ed26
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638143"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Risolvere i problemi dei connettori di Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi più comuni per la risoluzione dei problemi relativi ai connettori in Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

### <a name="error-code--azurebloboperationfailed"></a>Codice errore:  AzureBlobOperationFailed

- **Messaggio** : `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa** : Un'operazione di archiviazione BLOB ha generato un problema.

- **Raccomandazione** :  Controllare i dettagli dell'errore. Fare riferimento al documento della Guida dei BLOB: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Per assistenza, contattare il team di archiviazione.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Codice errore:  AzureBlobServiceNotReturnExpectedDataLength

- **Messaggio** : `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Codice errore:  AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Messaggio** : `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Codice errore:  AzureStorageOperationFailedConcurrentWrite

- **Messaggio** : `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Messaggio di errore: Le dimensioni della richiesta sono troppo grandi

- **Sintomi** : Si copiano i dati in Azure Cosmos DB con le dimensioni predefinite del batch di scrittura e viene generato l'errore *" **Le dimensioni della richiesta sono troppo grandi** "* .

- **Causa** : Cosmos DB limita le dimensioni di una singola richiesta a 2 MB. La formula è Dimensioni richiesta = Dimensioni singolo documento * Dimensioni batch di scrittura. Se le dimensioni del documento sono grandi, il comportamento predefinito risulterà in una richiesta di dimensioni troppo grandi. È possibile ottimizzare le dimensioni del batch di scrittura.

- **Soluzione** : Nel sink dell'attività di copia, ridurre il valore di "Dimensioni batch di scrittura" (il valore predefinito è 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Messaggio di errore: Violazione di vincolo di indice univoco

- **Sintomi** : Quando si copiano i dati in Cosmos DB, viene generato l'errore seguente:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Causa** : Le cause possono essere due:

    - Se si usa **Insert** come comportamento di scrittura, questo errore indica che i dati di origine contengono righe/oggetti con lo stesso ID.

    - Se si usa **Upsert** come comportamento di scrittura e si imposta un'altra chiave univoca per il contenitore, questo errore indica che i dati di origine contengono righe/oggetti con ID diversi, ma lo stesso valore per la chiave univoca definita.

- **Soluzione** : 

    - Per la prima causa, impostare **Upsert** come comportamento di scrittura.
    - Per la seconda causa, assicurarsi che ogni documento abbia un valore diverso per la chiave univoca definita.

### <a name="error-message-request-rate-is-large"></a>Messaggio di errore: La frequenza delle richieste è troppo elevata

- **Sintomi** : Quando si copiano i dati in Cosmos DB, viene generato l'errore seguente:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Causa** : Il numero di unità richiesta usate è superiore al numero di unità richiesta disponibili configurate in Cosmos DB. Per informazioni su come Cosmos DB calcola le unità richiesta, vedere [qui](../cosmos-db/request-units.md#request-unit-considerations).

- **Soluzione** : Sono disponibili due soluzioni:

    1. **Aumentare il numero di unità richiesta del contenitore** in Cosmos DB, operazione che consente di migliorare le prestazioni dell'attività di copia, anche se comporta un aumento dei costi in Cosmos DB. 

    2. Ridurre **writeBatchSize** a un valore più piccolo, ad esempio 1000, e impostare **parallelCopies** su un valore più basso, ad esempio 1, in modo che le prestazioni dell'esecuzione della copia risultino inferiori a quelle correnti ma non aumentino i costi in Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Colonna mancante nel mapping delle colonne

- **Sintomi** : Quando si importa uno schema per Cosmos DB per il mapping delle colonne, alcune colonne risultano mancanti. 

- **Causa** : Azure Data Factory deduce lo schema dai primi 10 documenti di Cosmos DB. Se alcune colonne/proprietà non dispongono di un valore in tali documenti, non verranno rilevate da Azure Data Factory e quindi non verranno visualizzate.

- **Soluzione** : È possibile ottimizzare la query come indicato di seguito per far sì che la colonna venga visualizzata nel set di risultati con un valore vuoto: (si supponga che nei primi 10 documenti manchi la colonna "impossibile"). In alternativa, è possibile aggiungere manualmente la colonna per il mapping.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Messaggio di errore: GuidRepresentation per il lettore è CSharpLegacy

- **Sintomi** : Quando si copiano dati da Cosmos DB MongoAPI/MongoDB con il campo UUID, viene generato l'errore seguente:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Causa** : Esistono due modi per rappresentare UUID in BSON - UuidStardard e UuidLegacy. Per impostazione predefinita, viene usato UuidLegacy per leggere i dati. Verrà generato un errore se i dati UUID in MongoDB sono UuidStandard.

- **Soluzione** : Nella stringa di connessione MongoDB aggiungere l'opzione " **uuidRepresentation = standard** ". Per altre informazioni, vedere [Stringa di connessione MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Codice errore:  AdlsGen2OperationFailed

- **Messaggio** : `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causa** : ADLS Gen2 genera l'errore che indica che l'operazione non è riuscita.

- **Raccomandazione** :  Controllare il messaggio di errore dettagliato generato da ADLS Gen2. Se il problema è causato da un errore temporaneo, riprovare. Per ulteriore assistenza, contattare il supporto di Archiviazione di Azure e fornire l'ID richiesta presente nel messaggio di errore.

- **Causa** : Se il messaggio di errore contiene 'Accesso negato', l'entità servizio o l'identità gestita usata potrebbe non avere autorizzazioni sufficienti per accedere ad ADLS Gen2.

- **Raccomandazione** :  Fare riferimento al documento della Guida: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Causa** : Se il messaggio di errore contiene 'InternalServerError', l'errore viene restituito da ADLS Gen2.

- **Raccomandazione** :  Il problema potrebbe essere causato da un errore temporaneo, riprovare. Se il problema persiste, contattare il supporto di Archiviazione di Azure e fornire l'ID richiesta presente nel messaggio di errore.


### <a name="error-code--adlsgen2invalidurl"></a>Codice errore:  AdlsGen2InvalidUrl

- **Messaggio** : `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Codice errore:  AdlsGen2InvalidFolderPath

- **Messaggio** : `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Codice errore:  AdlsGen2OperationFailedConcurrentWrite

- **Messaggio** : `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>Codice di errore: AdlsGen2TimeoutError

- **Messaggio** : `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Messaggio di errore: connessione sottostante chiusa: Impossibile stabilire una relazione di trust per il canale sicuro SSL/TLS.

- **Sintomi** : l'attività di copia ha esito negativo con l'errore seguente: 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **Motivo** : la convalida del certificato non è riuscita durante l'handshake TLS.

- **Soluzione: soluzione** alternativa: usare la copia temporanea per ignorare la convalida TLS per ADLS Gen1. È necessario riprodurre questo problema e raccogliere la traccia Netmon, quindi coinvolgere il team di rete per verificare la configurazione della rete locale.

    ![Risoluzione dei problemi ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Messaggio di errore: Errore del server remoto: (403) Accesso negato

- **Sintomi** : L'attività di copia ha esito negativo con l'errore seguente: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Causa** : Una delle possibili cause è che l'entità servizio o l'identità gestita usata non è autorizzata ad accedere a una cartella o a un file specifico.

- **Soluzione** : Concedere le autorizzazioni corrispondenti per tutte le cartelle e le sottocartelle che è necessario copiare. Fare riferimento a [questo documento](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Messaggio di errore: Non è stato possibile ottenere il token di accesso tramite l'entità servizio. Errore ADAL: service_unavailable

- **Sintomi** : L'attività di copia ha esito negativo con l'errore seguente:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Causa** : Quando il server dei token di servizio (STS) di proprietà di Azure Active Directory non è disponibile, ad esempio è troppo occupato per gestire le richieste, restituisce un errore HTTP 503. 

- **Soluzione** : Eseguire di nuovo l'attività di copia dopo alcuni minuti.
                  

## <a name="azure-synapse-analytics-formerly-sql-data-warehouseazure-sql-databasesql-server"></a>Azure sinapsi Analytics (in precedenza SQL Data Warehouse)/Azure database SQL/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Codice errore:  SqlFailedToConnect

- **Messaggio** : `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Causa** : Se il messaggio di errore contiene "SqlException", il database SQL genera l'errore che indica che un'operazione specifica non è riuscita.

- **Raccomandazione** : per informazioni dettagliate, cercare il codice di errore SQL in questo documento di riferimento https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Per assistenza, contattare il supporto di Azure SQL.

- **Causa** : Se il messaggio di errore contiene "L'accesso al server del client con indirizzo IP '...' non è consentito" e si sta provando a connettersi al database SQL di Azure, in genere l'errore è causato da un problema del firewall del database SQL di Azure.

- **Raccomandazione** : in configurazione logica del firewall di SQL Server abilitare l'opzione "Consenti ai servizi e alle risorse di Azure di accedere al server". Documentazione di riferimento: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Codice errore:  SqlOperationFailed

- **Messaggio** : `A database operation failed. Please search error to get more details.`

- **Causa** : Se il messaggio di errore contiene "SqlException", il database SQL genera l'errore che indica che un'operazione specifica non è riuscita.

- **Raccomandazione** :  Se l'errore SQL non è chiaro, provare a modificare il database con il livello di compatibilità più recente '150'. Può generare errori SQL dell'ultima versione. Vedere la [documentazione dettagliata](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Per la risoluzione dei problemi SQL, eseguire una ricerca in base al codice di errore SQL in questo documento di riferimento per altri dettagli: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Per assistenza, contattare il supporto di Azure SQL.

- **Causa** : Se il messaggio di errore contiene "PdwManagedToNativeInteropException", in genere l'errore è causato da una mancata corrispondenza tra le dimensioni delle colonne di origine e sink.

- **Raccomandazione** :  Controllare le dimensioni delle colonne di origine e sink. Per assistenza, contattare il supporto di Azure SQL.

- **Causa** : Se il messaggio di errore contiene "InvalidOperationException", in genere l'errore è causato da dati di input non validi.

- **Raccomandazione** :  Per identificare la riga in cui si è verificato il problema, abilitare la funzionalità di tolleranza di errore nell'attività di copia, in modo da poter reindirizzare le righe problematiche alla risorsa di archiviazione per un'analisi più approfondita. Documentazione di riferimento: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.



### <a name="error-code--sqlunauthorizedaccess"></a>Codice errore:  SqlUnauthorizedAccess

- **Messaggio** : `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa** : Le credenziali non sono corrette oppure l'account di accesso non può accedere al database SQL.

- **Raccomandazione** :  Verificare che l'account di accesso abbia autorizzazioni sufficienti per accedere al database SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Codice errore:  SqlOpenConnectionTimeout

- **Messaggio** : `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa** : Potrebbe trattarsi di un errore temporaneo del database SQL.

- **Raccomandazione** : riprovare ad aggiornare la stringa di connessione del servizio collegato con un valore di timeout di connessione maggiore.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Codice errore:  SqlAutoCreateTableTypeMapFailed

- **Messaggio** : `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa** : La tabella di creazione automatica non soddisfa i requisiti di origine.

- **Raccomandazione** :  Aggiornare il tipo di colonna nei 'mapping' o creare manualmente la tabella di sink nel server di destinazione.


### <a name="error-code--sqldatatypenotsupported"></a>Codice errore:  SqlDataTypeNotSupported

- **Messaggio** : `A database operation failed. Check the SQL errors.`

- **Causa** : Se il problema si verifica nell'origine SQL e l'errore è correlato all'overflow di SqlDateTime, il valore dei dati è oltre l'intervallo del tipo di logica (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Raccomandazione** :  Eseguire il cast del tipo alla stringa nella query SQL di origine o nel mapping delle colonne dell'attività di copia modificare il tipo di colonna in 'String'.

- **Causa** : Se il problema si verifica nel sink SQL e l'errore è correlato all'overflow di SqlDateTime, il valore dei dati è oltre l'intervallo consentito nella tabella di sink.

- **Raccomandazione** :  Aggiornare il tipo di colonna corrispondente con il tipo 'datetime2' nella tabella di sink.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Codice errore:  SqlInvalidDbStoredProcedure

- **Messaggio** : `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa** : La stored procedure specificata non è valida. Il problema potrebbe essere causato dal fatto che la stored procedure non restituisce alcun dato.

- **Raccomandazione** :  Convalidare la stored procedure con gli strumenti SQL. Assicurarsi che la stored procedure possa restituire i dati.


### <a name="error-code--sqlinvaliddbquerystring"></a>Codice errore:  SqlInvalidDbQueryString

- **Messaggio** : `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa** : La query SQL specificata non è valida. Il problema potrebbe essere causato dal fatto che la query non restituisce alcun dato

- **Raccomandazione** :  Convalidare la query SQL con gli strumenti SQL. Assicurarsi che la query possa restituire i dati.


### <a name="error-code--sqlinvalidcolumnname"></a>Codice errore:  SqlInvalidColumnName

- **Messaggio** : `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa** : Non è possibile trovare la colonna. È possibile che la configurazione non sia corretta.

- **Raccomandazione** :  Verificare la colonna nella query, la 'struttura' nel set di dati e i 'mapping' nell'attività.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Codice errore:  SqlColumnNameMismatchByCaseSensitive

- **Messaggio** : `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Codice errore:  SqlBatchWriteTimeout

- **Messaggio** : `Timeouts in SQL write operation.`

- **Causa** : Potrebbe trattarsi di un errore temporaneo del database SQL.

- **Raccomandazione** : riprovare. Se il problema si ripresenta, contattare il supporto di Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Codice errore:  SqlBatchWriteTransactionFailed

- **Messaggio** : `SQL transaction commits failed`

- **Causa** : Se i dettagli dell'eccezione indicano costantemente il timeout della transazione, la latenza di rete tra il runtime di integrazione e il database è superiore alla soglia predefinita di 30 secondi.

- **Raccomandazione** :  Aggiornare la stringa di connessione del servizio collegato SQL con un valore di 'timeout connessione' maggiore o uguale a 120 ed eseguire di nuovo l'attività.

- **Causa** : Se i dettagli dell'eccezione indicano in modo intermittente un'interruzione della connessione SQL, potrebbe trattarsi di un errore di rete temporaneo o di un problema relativo al database SQL

- **Raccomandazione** : riprovare l'attività ed esaminare le metriche lato database SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Codice errore:  SqlBulkCopyInvalidColumnLength

- **Messaggio** : `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa** : La copia bulk SQL non è riuscita perché il client bcp ha inviato una lunghezza di colonna non valida.

- **Raccomandazione** :  Per identificare la riga in cui si è verificato il problema, abilitare la funzionalità di tolleranza di errore nell'attività di copia, in modo da poter reindirizzare le righe problematiche alla risorsa di archiviazione per un'analisi più approfondita. Documentazione di riferimento: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Codice errore:  SqlConnectionIsClosed

- **Messaggio** : `The connection is closed by SQL Database.`

- **Causa** : La connessione SQL viene chiusa dal database SQL quando l'esecuzione simultanea e il server terminano la connessione.

- **Raccomandazione** :  La connessione SQL è stata chiusa dal server remoto. Riprovare. Se il problema si ripresenta, contattare il supporto di Azure SQL.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Codice errore:  SqlCreateTableFailedUnsupportedType

- **Messaggio** : `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Messaggio di errore: Conversione non riuscita durante la conversione di una stringa di caratteri nel tipo di dati uniqueidentifier

- **Sintomi** : quando si copiano dati da un'origine dati tabulare, ad esempio SQL Server, in Azure sinapsi Analytics usando la copia di staging e la polibase, viene raggiunto l'errore seguente:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Motivo** : la polibase di Azure sinapsi Analytics non è in grado di convertire una stringa vuota in GUID.

- **Soluzione** : Nelle impostazioni Polybase del sink dell'attività di copia impostare l'opzione " **Tipo di uso predefinito** " su false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Messaggio di errore: Tipo di dati previsto: DECIMAL(x, x), valore danneggiato

- **Sintomi** : quando si copiano dati da un'origine dati tabulare, ad esempio SQL Server, in Azure sinapsi Analytics usando la copia di staging e la polibase, viene raggiunto l'errore seguente:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Motivo** : la polibase di Azure sinapsi Analytics non può inserire una stringa vuota (valore null) in una colonna decimale.

- **Soluzione** : Nelle impostazioni Polybase del sink dell'attività di copia impostare l'opzione " **Tipo di uso predefinito** " su false.

### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Messaggio di errore: messaggio eccezione Java: HdfsBridge:: CreateRecordReader

- **Sintomi** : i dati vengono copiati in Azure sinapsi Analytics usando la polibase e viene raggiunto l'errore seguente:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Causa** : La possibile causa è che lo schema, ovvero la larghezza totale della colonne, sia troppo grande, ovvero maggiore di 1 MB. Verificare lo schema della tabella di Azure sinapsi Analytics di destinazione aggiungendo la dimensione di tutte le colonne:

    - Int -> 4 byte
    - Bigint -> 8 byte
    - Varchar(n), char(n), binary(n), varbinary(n) -> n byte
    - Nvarchar(n), nchar(n) -> n*2 byte
    - Date -> 6 byte
    - Datetime/(2), smalldatetime -> 16 byte
    - Datetimeoffset -> 20 byte
    - Decimal -> 19 byte
    - Float -> 8 byte
    - Money -> 8 byte
    - Smallmoney -> 4 byte
    - Real -> 4 byte
    - Smallint -> 2 byte
    - Time -> 12 byte
    - Tinyint -> 1 byte

- **Soluzione** : Ridurre la larghezza delle colonne in modo che sia inferiore a 1 MB

- In alternativa, usare l'approccio di inserimento bulk disabilitando PolyBase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Messaggio di errore: La condizione specificata con le intestazioni condizionali HTTP non è stata soddisfatta

- **Sintomi** : si usa una query SQL per eseguire il pull dei dati da Azure sinapsi Analytics e si raggiunge l'errore seguente:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Causa** : l'analisi sinapsi di Azure ha raggiunto un problema durante l'esecuzione di query sulla tabella esterna in archiviazione di Azure.

- **Soluzione** : Eseguire la stessa query in SSMS e verificare se viene visualizzato lo stesso risultato. In caso affermativo, aprire un ticket di supporto per Azure sinapsi Analytics e fornire il nome del server e del database di Azure sinapsi Analytics per ulteriore risoluzione dei problemi.
            

## <a name="delimited-text-format"></a>Formato di testo delimitato

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Codice errore:  DelimitedTextColumnNameNotAllowNull

- **Messaggio** : `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa** : Quando si imposta 'firstRowAsHeader' nell'attività, la prima riga verrà usata come nome di colonna. Questo errore indica che la prima riga contiene un valore vuoto. Ad esempio: "Columna,, ColumnB".

- **Raccomandazione** :  Controllare la prima riga e correggere il valore se è presente un valore vuoto.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Codice errore:  DelimitedTextMoreColumnsThanDefined

- **Messaggio** : `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Causa** : Il numero di colonne della riga problematica è maggiore di quello della prima riga. La causa potrebbe essere dovuta a un problema di dati o a impostazioni non corrette del delimitatore di colonna o del carattere virgolette.

- **Raccomandazione** : recuperare il numero di righe in un messaggio di errore, controllare la colonna della riga e correggere i dati.

- **Causa** : Se il numero di colonne previsto è "1" nel messaggio di errore, è possibile che siano state specificate impostazioni di compressione o di formato errate, che hanno causato l'analisi non corretta dei file da parte di Azure Data Factory.

- **Raccomandazione** :  Controllare le impostazioni di formato per assicurarsi che corrispondano ai file di origine.

- **Causa** : Se l'origine è una cartella, è possibile che i file nella cartella specificata abbiano uno schema diverso.

- **Raccomandazione** :  Assicurarsi che i file nella cartella specificata abbiano lo stesso schema.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Codice errore:  DelimitedTextIncorrectRowDelimiter

- **Messaggio** : `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Codice errore:  DelimitedTextTooLargeColumnCount

- **Messaggio** : `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Codice errore:  DelimitedTextInvalidSettings

- **Messaggio** : `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Codice errore:  DynamicsCreateServiceClientError

- **Messaggio** : `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Causa** : Si tratta di un problema temporaneo relativo al server Dynamics.

- **Raccomandazione** :  Eseguire di nuovo la pipeline. Se il problema persiste, provare a ridurre il parallelismo. Se l'errore persiste ancora, contattare il supporto tecnico di Dynamics.



## <a name="json-format"></a>Formato JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Codice errore:  JsonInvalidArrayPathDefinition

- **Messaggio** : `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Codice errore:  JsonEmptyJObjectData

- **Messaggio** : `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Codice errore:  JsonNullValueInPathDefinition

- **Messaggio** : `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Codice errore:  JsonUnsupportedHierarchicalComplexValue

- **Messaggio** : `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Codice errore:  JsonConflictPartitionDiscoverySchema

- **Messaggio** : `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Codice errore:  JsonInvalidDataFormat

- **Messaggio** : `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Codice errore:  JsonInvalidDataMixedArrayAndObject

- **Messaggio** : `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Formato Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Codice errore:  ParquetJavaInvocationException

- **Messaggio** : `An error occurred when invoking java, message: %javaException;.`

- **Causa** : Se il messaggio di errore contiene 'java.lang.OutOfMemory', 'Spazio dell'heap di Java' e 'doubleCapacity', in genere si tratta di un problema di gestione della memoria nella versione precedente del runtime di integrazione.

- **Raccomandazione** : se si usa Integration Runtime self-hosted e la versione è precedente a 3.20.7159.1, è consigliabile eseguire l'aggiornamento alla versione più recente.

- **Causa** : Se il messaggio di errore contiene 'java.lang.OutOfMemory', il runtime di integrazione non dispone di risorse sufficienti per elaborare i file.

- **Raccomandazione** :  Limitare le esecuzioni simultanee nel runtime di integrazione. Per il runtime di integrazione self-hosted aumentare la memoria fino a ottenere un computer potente con memoria uguale o superiore a 8 GB.

- **Causa** : Se il messaggio di errore contiene 'NullPointerReference', è possibile che sia un errore temporaneo.

- **Raccomandazione** : riprovare. Se il problema persiste, contattare il supporto tecnico.


### <a name="error-code--parquetinvalidfile"></a>Codice errore:  ParquetInvalidFile

- **Messaggio** : `File is not a valid parquet file.`

- **Causa** : Problema del file parquet.

- **Raccomandazione** :  Verificare che l'input sia un file parquet valido.


### <a name="error-code--parquetnotsupportedtype"></a>Codice errore:  ParquetNotSupportedType

- **Messaggio** : `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa** : Il formato parquet non è supportato in Azure Data Factory.

- **Raccomandazione** :  Controllare i dati di origine. Fare riferimento al documento: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Codice errore:  ParquetMissedDecimalPrecisionScale

- **Messaggio** : `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa** : Si è provato ad analizzare la precisione e la scala di un numero, ma non sono state fornite tali informazioni.

- **Raccomandazione** :  'Source' non restituisce la precisione e la scala corrette. Controllare la precisione e la scala della colonna del problema.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Codice errore:  ParquetInvalidDecimalPrecisionScale

- **Messaggio** : `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa** : Lo schema non è valido.

- **Raccomandazione** :  Controllare la precisione e la scala della colonna del problema.


### <a name="error-code--parquetcolumnnotfound"></a>Codice errore:  ParquetColumnNotFound

- **Messaggio** : `Column %column; does not exist in Parquet file.`

- **Causa** : Lo schema di origine non corrisponde allo schema del sink.

- **Raccomandazione** :  Controllare i 'mapping' in 'attività'. Assicurarsi che sia possibile eseguire il mapping della colonna di origine alla colonna sink corretta.


### <a name="error-code--parquetinvaliddataformat"></a>Codice errore:  ParquetInvalidDataFormat

- **Messaggio** : `Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa** : Non è possibile convertire i dati nel tipo specificato in mapping.source

- **Raccomandazione** :  Controllare i dati di origine o specificare il tipo di dati corretto per la colonna nel mapping delle colonne dell'attività di copia. Fare riferimento al documento: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Codice errore:  ParquetDataCountNotMatchColumnCount

- **Messaggio** : `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa** : Il numero di colonne di origine e il numero di colonne sink non corrispondono

- **Raccomandazione** :  Controllare che il numero di colonne di origine sia uguale al numero di colonne sink nel 'mapping'.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Codice errore:  ParquetDataTypeNotMatchColumnType

- **Messaggio** : Il tipo di dati %srcType; non corrisponde al tipo di colonna specificato %dstType; nella colonna '%columnIndex;'.

- **Causa** : I dati dell'origine non possono essere convertiti in tipi definiti nel sink

- **Raccomandazione** : specificare un tipo corretto in mapping. sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Codice errore:  ParquetBridgeInvalidData

- **Messaggio** : `%message;`

- **Causa** : Valore dei dati oltre la limitazione

- **Raccomandazione** : riprovare. Se il problema persiste, contattare Microsoft.


### <a name="error-code--parquetunsupportedinterpretation"></a>Codice errore:  ParquetUnsupportedInterpretation

- **Messaggio** : `The given interpretation '%interpretation;' of parquet format is not supported.`

- **Causa** : Scenario non supportato

- **Raccomandazione** :  'ParquetInterpretFor' non deve essere 'sparkSql'.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Codice errore:  ParquetUnsupportFileLevelCompressionOption

- **Messaggio** : `File level compression is not supported for Parquet.`

- **Causa** : Scenario non supportato

- **Raccomandazione** :  Rimuovere 'CompressionType' nel payload.



## <a name="general-copy-activity-error"></a>Errore generale dell'attività di copia

### <a name="error-code--jrenotfound"></a>Codice errore:  JreNotFound

- **Messaggio** : `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa** : Il runtime di integrazione self-hosted non riesce a trovare Java Runtime. Java Runtime è necessario per la lettura di un'origine particolare.

- **Raccomandazione** :  Controllare l'ambiente del runtime di integrazione. Documentazione di riferimento: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Codice errore:  WildcardPathSinkNotSupported

- **Messaggio** : `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa** : Il set di dati sink non supporta i caratteri jolly.

- **Raccomandazione** :  Controllare il set di dati sink e correggere il percorso senza il carattere jolly.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Codice errore:  MappingInvalidPropertyWithEmptyValue

- **Messaggio** : `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Codice errore:  MappingInvalidPropertyWithNamePathAndOrdinal

- **Messaggio** : `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Codice errore:  MappingDuplicatedOrdinal

- **Messaggio** : `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Codice errore:  MappingInvalidOrdinalForSinkColumn

- **Messaggio** : `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
