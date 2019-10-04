---
title: Risolvere i problemi di Azure Data Factory connettori | Microsoft Docs
description: Informazioni su come risolvere i problemi del connettore in Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: bea5191063cf673f6b1395d46a15536e80b0aa30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143505"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Risolvere i problemi di Azure Data Factory connettori

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per i connettori in Azure Data Factory.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Messaggio di errore: Il server remoto ha restituito un errore: (403) non consentito

- **Sintomi**: L'attività di copia ha esito negativo con l'errore seguente: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Causa**: Una delle possibili cause è che l'entità servizio o l'identità gestita usata non è autorizzata ad accedere a una cartella o a un file specifico.

- **Risoluzione**: Concedere le autorizzazioni corrispondenti per tutte le cartelle e le sottocartelle che è necessario copiare. Fare riferimento a [questo documento](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Messaggio di errore: Non è stato possibile ottenere il token di accesso tramite l'entità servizio. Errore ADAL: service_unavailable

- **Sintomi**: l'attività di copia ha esito negativo con l'errore seguente:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Causa**: Quando il server del token del servizio (STS) di proprietà di Azure Active Directory non è disponibile, ad esempio troppo occupato per gestire le richieste, restituisce un errore HTTP 503. 

- **Risoluzione**: Eseguire di nuovo l'attività di copia dopo alcuni minuti.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Messaggio di errore: Conversione non riuscita durante la conversione da una stringa di caratteri a uniqueidentifier

- **Sintomi**: Quando si copiano dati da un'origine dati tabulare, ad esempio SQL Server, in Azure SQL Data Warehouse usando la copia di staging e la polibase, viene raggiunto il seguente errore:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Causa**: Azure SQL Data Warehouse polibase non è in grado di convertire una stringa vuota in GUID.

- **Risoluzione**: In sink attività di copia, in impostazioni di base, impostare l'opzione "**Usa tipo predefinito**" su false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Messaggio di errore: Tipo di dati previsto: DECIMAL (x, x), valore offensivo

- **Sintomi**: Quando si copiano dati da un'origine dati tabulare, ad esempio SQL Server, in SQL DW usando la copia di staging e la polibase, viene raggiunto il seguente errore:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Causa**: Azure SQL Data Warehouse polibase non può inserire una stringa vuota (valore null) in una colonna decimale.

- **Risoluzione**: In sink attività di copia, in impostazioni di base, impostare l'opzione "**Usa tipo predefinito**" su false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Messaggio di errore: Messaggio di eccezione Java: HdfsBridge:: CreateRecordReader

- **Sintomi**: Si copiano i dati in Azure SQL Data Warehouse usando la polibase e si raggiunge l'errore seguente:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Causa**: La possibile cause è che lo schema (larghezza totale della colonna) è troppo grande (maggiore di 1 MB). Controllare lo schema della tabella SQL DW di destinazione aggiungendo la dimensione di tutte le colonne:

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

- **Risoluzione**: Ridurre la larghezza della colonna in modo che sia minore di 1 MB

- In alternativa, usare l'approccio BULK INSERT disabilitando la funzionalità di base

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Messaggio di errore: La condizione specificata con le intestazioni condizionali HTTP non è stata soddisfatta

- **Sintomi**: Usare query SQL per estrarre i dati da Azure SQL Data Warehouse e raggiungere l'errore seguente:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Causa**: Azure SQL Data Warehouse ha riscontrato un problema durante l'esecuzione di query sulla tabella esterna in archiviazione di Azure.

- **Risoluzione**: Eseguire la stessa query in SSMS e verificare se viene visualizzato lo stesso risultato. In caso affermativo, aprire un ticket di supporto per Azure SQL Data Warehouse e fornire il nome del server e del database SQL DW per ulteriore risoluzione dei problemi.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Messaggio di errore: Dimensione della richiesta troppo grande

- **Sintomi**: I dati vengono copiati in Azure Cosmos DB con le dimensioni predefinite del batch di scrittura e l'errore di hit *"**dimensioni della richiesta è troppo grande**"* .

- **Causa**: Cosmos DB limita le dimensioni di una singola richiesta a 2 MB. La formula è, Size della richiesta = dimensioni del singolo documento * dimensioni del batch di scrittura. Se le dimensioni del documento sono elevate, il comportamento predefinito comporterà dimensioni di richiesta troppo grandi. È possibile ottimizzare le dimensioni del batch di scrittura.

- **Risoluzione**: Nel sink dell'attività di copia, ridurre il valore di "dimensioni batch di scrittura" (il valore predefinito è 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Messaggio di errore: Violazione del vincolo di indice univoco

- **Sintomi**: Quando si copiano i dati in Cosmos DB, viene raggiunto l'errore seguente:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Causa**: Le cause possono essere due:

    - Se si usa l'istruzione **Insert** As Write Behavior, questo errore indica che i dati di origine contengono righe/oggetti con lo stesso ID.

    - Se si usa **Upsert** come comportamento di scrittura e si imposta un'altra chiave univoca per il contenitore, questo errore indica che i dati di origine contengono righe/oggetti con ID diversi ma lo stesso valore per la chiave univoca definita.

- **Risoluzione**: 

    - Per cause1, impostare **Upsert** come comportamento di scrittura.
    - Per la seconda ragione, assicurarsi che ogni documento abbia un valore diverso per la chiave univoca definita.

### <a name="error-message-request-rate-is-large"></a>Messaggio di errore: La frequenza delle richieste è troppo elevata

- **Sintomi**: Quando si copiano i dati in Cosmos DB, viene raggiunto l'errore seguente:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Causa**: Il numero di unità richiesta usate è maggiore della UR disponibile configurata in Cosmos DB. Informazioni su come Cosmos DB calcola ur da [qui](../cosmos-db/request-units.md#request-unit-considerations).

- **Risoluzione**: Ecco due soluzioni:

    1. **Aumentare il** numero di unità richiesta del contenitore a un valore più grande in Cosmos DB, che consente di migliorare le prestazioni dell'attività di copia, anche se comporta un aumento dei costi Cosmos DB. 

    2. Ridurre il valore di **writeBatchSize** a un valore più piccolo, ad esempio 1000, e impostare **parallelCopies** su un valore più basso, ad esempio 1, in modo che le prestazioni dell'esecuzione della copia risultino inferiori a quelle correnti ma non aumenteranno i costi in Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Colonna mancante nel mapping delle colonne

- **Sintomi**: Quando si importa lo schema per Cosmos DB per il mapping delle colonne, alcune colonne risultano mancanti. 

- **Causa**: ADF deduce lo schema dai primi 10 documenti Cosmos DB. Se alcune colonne/proprietà non dispongono di un valore in tali documenti, non verranno rilevati da ADF, quindi non verranno visualizzati.

- **Risoluzione**: È possibile ottimizzare la query come indicato di seguito per applicare la colonna per la visualizzazione nel set di risultati con un valore vuoto: (si supponga che la colonna "Impossibile" non sia presente nei primi 10 documenti). In alternativa, è possibile aggiungere manualmente la colonna per il mapping.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Messaggio di errore: Il GuidRepresentation per il Reader è CSharpLegacy

- **Sintomi**: Quando si copiano dati da Cosmos DB MongoAPI/MongoDB con il campo UUID, viene raggiunto l'errore seguente:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Causa**: Esistono due modi per rappresentare UUID in BSON-UuidStardard e UuidLegacy. Per impostazione predefinita, UuidLegacy viene usato per leggere i dati. Si raggiungerà un errore se i dati UUID in MongoDB sono UuidStandard.

- **Risoluzione**: Nella stringa di connessione MongoDB aggiungere l'opzione "**uuidRepresentation = standard**". Per altre informazioni, vedere [stringa di connessione MongoDB](connector-mongodb.md#linked-service-properties).

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Messaggio di errore: Credenziale SFTP non valida specificata per il tipo di autenticazione ' SshPublicKey '

- **Sintomi**: Si usa `SshPublicKey` l'autenticazione di e si raggiunge l'errore seguente:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Causa**: Esistono tre possibili cause:

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

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video su Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)



