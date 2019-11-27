---
title: Risolvere i problemi di Azure Data Factory connettori
description: Informazioni su come risolvere i problemi del connettore in Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 218031830a7516dfd539e1c0b9b665807822f38d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533158"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Risolvere i problemi di Azure Data Factory connettori

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per i connettori in Azure Data Factory.

## <a name="azure-data-lake-storage"></a>Archiviazione di Azure Data Lake

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

- **Risoluzione**: 

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

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Messaggio di errore: credenziale SFTP non valida specificata per il tipo di autenticazione ' SshPublicKey '

- **Sintomi**: si usa l'autenticazione `SshPublicKey` e si raggiunge l'errore seguente:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Causa**: esistono tre possibili cause:

    1. Se si usa l'interfaccia utente di creazione di ADF per creare il servizio collegato SFTP, questo errore indica che la chiave privata che si sceglie di usare è in formato errato. È possibile usare un formato PKCS # 8 per la chiave privata SSH, ma si noti che ADF supporta solo il formato di chiave SSH tradizionale. In particolare, la differenza tra il formato PKCS # 8 e il formato di chiave tradizionale è il contenuto della chiave PKCS # 8 inizia con " *-----iniziare la chiave privata crittografata-----* " mentre il formato della chiave tradizionale inizia con " *-----iniziare la chiave privata RSA-----* ".
    2. Se si usa Azure Key Vault per archiviare il contenuto della chiave privata o usare il modo programmatico per creare il servizio collegato SFTP, questo errore indica che il contenuto della chiave privata non è corretto, probabilmente non è con codifica Base64.
    3. Contenuto della chiave privata o delle credenziali non valido.

- **Risoluzione**: 

    - Per causare #1 eseguire i comandi seguenti per convertire la chiave nel formato di chiave tradizionale, quindi utilizzarla nell'interfaccia utente di creazione di ADF.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - Per causare #2, per generare tale stringa, il cliente può utilizzare i due modi seguenti:
    - Usando lo strumento di conversione Base64 di terze parti: incollare l'intero contenuto della chiave privata in strumenti come [codifica Base64 e](https://www.base64encode.org/)decodifica, codificarlo in una stringa di formato Base64, quindi incollare questa stringa in Key Vault o usare questo valore per la creazione di un servizio collegato SFTP a livello di programmazione.
    - Uso C# del codice:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Per la #3 di origine, controllare se il file di chiave o la password sono corretti utilizzando altri strumenti per la convalida se è possibile utilizzarlo per accedere correttamente al server SFTP.
  

## <a name="azure-sql-data-warehouse--azure-sql-database--sql-server"></a>Azure SQL Data Warehouse \ database SQL di Azure \ SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Codice di errore: SqlFailedToConnect

- **Messaggio**: `Cannot connect to SQL database: '%server;', Database: '%database;', User: '%user;'. Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access.`

- **Motivo**: se il messaggio di errore contiene "SqlException", il database SQL genera l'errore che indica che un'operazione specifica non è riuscita.

- **Raccomandazione**: per informazioni dettagliate, cercare in base al codice di errore SQL in questo documento di riferimento: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Per ulteriori informazioni, contattare il supporto SQL di Azure.

- **Motivo**: se il messaggio di errore contiene "client con indirizzo IP"... " non è consentito accedere al server e si sta provando a connettersi al database SQL di Azure, in genere è causato da un problema del firewall del database SQL di Azure.

- **Raccomandazione**: nella configurazione di Azure SQL Server Firewall abilitare l'opzione "Consenti ai servizi e alle risorse di Azure di accedere al server". Documentazione di riferimento: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Codice di errore: SqlOperationFailed

- **Messaggio**: `A database operation failed. Please search error to get more details.`

- **Motivo**: se il messaggio di errore contiene "SqlException", il database SQL genera l'errore che indica che un'operazione specifica non è riuscita.

- **Raccomandazione**: per informazioni dettagliate, cercare in base al codice di errore SQL in questo documento di riferimento: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Per ulteriori informazioni, contattare il supporto SQL di Azure.

- **Causa**: se il messaggio di errore contiene "PdwManagedToNativeInteropException", in genere è causato da una mancata corrispondenza tra le dimensioni delle colonne di origine e sink.

- **Raccomandazione**: verificare le dimensioni delle colonne di origine e sink. Per ulteriori informazioni, contattare il supporto SQL di Azure.

- **Causa**: se il messaggio di errore contiene "InvalidOperationException", in genere è causato da dati di input non validi.

- **Raccomandazione**: per identificare la riga in cui si è verificato il problema, abilitare la funzionalità tolleranza di errore nell'attività di copia, che può reindirizzare le righe problematiche a una risorsa di archiviazione per un'analisi più approfondita. Documentazione di riferimento: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Codice di errore: SqlUnauthorizedAccess

- **Messaggio**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa**: le credenziali non sono corrette oppure l'account di accesso non può accedere al database SQL.

- **Consiglio**: verificare che l'account di accesso disponga di autorizzazioni sufficienti per accedere al database SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Codice di errore: SqlOpenConnectionTimeout

- **Messaggio**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Cause**: potrebbe essere un errore temporaneo del database SQL.

- **Raccomandazione**: riprovare ad aggiornare la stringa di connessione del servizio collegato con un valore di timeout di connessione maggiore.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Codice di errore: SqlAutoCreateTableTypeMapFailed

- **Messaggio**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(colunm name:'%colunmName;') in auto-create table.`

- **Causa**: la tabella di creazione automatica non può soddisfare i requisiti di origine.

- **Raccomandazione**: aggiornare il tipo di colonna in "mapping" o creare manualmente la tabella di sink nel server di destinazione.


### <a name="error-code--sqldatatypenotsupported"></a>Codice di errore: SqlDataTypeNotSupported

- **Messaggio**: `A database operation failed. Please check the SQL errors.`

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

- **Raccomandazione**: verificare la colonna nella query ' Structure ' nel set di dati è Mappings ' nell'attività.


### <a name="error-code--sqlbatchwritetimeout"></a>Codice di errore: SqlBatchWriteTimeout

- **Messaggio**: `Timeout in SQL write opertaion.`

- **Cause**: potrebbe essere un errore temporaneo del database SQL.

- **Suggerimento**: se si tratta di un problema, contattare il supporto SQL di Azure.


### <a name="error-code--sqlbatchwriterollbackfailed"></a>Codice di errore: SqlBatchWriteRollbackFailed

- **Messaggio**: `Timeout in SQL write operation and rollback also fail.`

- **Cause**: potrebbe essere un errore temporaneo del database SQL.

- **Raccomandazione**: riprovare ad aggiornare la stringa di connessione del servizio collegato con un valore di timeout di connessione maggiore.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Codice di errore: SqlBulkCopyInvalidColumnLength

- **Messaggio**: `SQL Bulk Copy failed due to received an invalid column length from the bcp client.`

- **Causa**: la copia bulk SQL non è riuscita perché ha ricevuto una lunghezza di colonna non valida dal client bcp.

- **Raccomandazione**: per identificare la riga in cui si è verificato il problema, abilitare la funzionalità tolleranza di errore nell'attività di copia, che può reindirizzare le righe problematiche a una risorsa di archiviazione per un'analisi più approfondita. Documentazione di riferimento: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Codice di errore: SqlConnectionIsClosed

- **Messaggio**: `The connection is closed by SQL database.`

- **Motivo**: la connessione SQL è stata chiusa dal database SQL quando l'esecuzione simultanea e il server interrompono la connessione.

- **Raccomandazione**: il server remoto chiude la connessione SQL. Riprovare. Se si tratta di un problema, contattare il supporto SQL di Azure.

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
            

## <a name="azure-blob-storage"></a>Archivio BLOB di Azure

### <a name="error-code--azurebloboperationfailed"></a>Codice di errore: AzureBlobOperationFailed

- **Messaggio**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa**: l'operazione di archiviazione BLOB ha raggiunto un problema.

- **Consiglio**: controllare l'errore in dettagli. Consultare il documento della Guida BLOB: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Se serve assistenza, contattare il team di archiviazione.



## <a name="azure-data-lake-gen2"></a>Azure Data Lake Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Codice di errore: AdlsGen2OperationFailed

- **Messaggio**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Motivo**: ADLS Gen2 genera l'errore che indica che l'operazione non è riuscita.

- **Raccomandazione**: controllare il messaggio di errore dettagliato generato da ADLS Gen2. Se il problema è causato da un errore temporaneo, riprovare. Per ulteriori informazioni, contattare il supporto di archiviazione di Azure e fornire l'ID richiesta nel messaggio di errore.

- **Motivo**: quando il messaggio di errore contiene ' Forbidden ', l'entità servizio o l'identità gestita utilizzata potrebbe non disporre di autorizzazioni sufficienti per accedere al ADLS Gen2.

- **Raccomandazione**: fare riferimento al documento della guida: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Motivo**: quando il messaggio di errore contiene ' InternalServerError ', l'errore viene restituito da ADLS Gen2.

- **Raccomandazione**: il problema potrebbe essere causato da un errore temporaneo. riprovare. Se il problema persiste, contattare il supporto di archiviazione di Azure e fornire l'ID richiesta nel messaggio di errore.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video su Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)
            
