---
title: Tolleranza di errore dell'attività di copia in Azure Data Factory
description: Informazioni su come aggiungere la tolleranza di errore all'attività di copia in Azure Data Factory ignorando i dati incompatibili.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 6b172a6e15cbb22c3a0a16cb1e238ddfe45048bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130773"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolleranza di errore dell'attività di copia in Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versione corrente](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando si copiano dati dall'archivio di origine a quello di destinazione, l'attività di copia di Azure Data Factory fornisce un certo livello di tolleranza di errore per impedire le interruzioni causate da errori durante lo spostamento dati. Si consiglia, ad esempio, di copiare milioni di righe dall'archivio di origine a quello di destinazione, in cui sia stata creata una chiave primaria nel database di destinazione, ma senza che siano state definite chiavi primarie nel database di origine. Quando si copiano righe duplicate dall'origine alla destinazione, si verifica un errore di violazione della CP nel database di destinazione. Attualmente, l'attività di copia offre due modi per gestire tali errori: 
- È possibile interrompere l'attività di copia quando viene rilevato un errore. 
- È possibile continuare a copiare la parte restante abilitando la tolleranza di errore per ignorare i dati incompatibili. Ad esempio, in questo caso è possibile ignorare la riga duplicata. È anche possibile registrare i dati ignorati abilitando il log sessione all'interno dell'attività di copia. 

## <a name="copying-binary-files"></a>Copia di file binari 

Durante la copia di file binari, ADF supporta gli scenari di tolleranza di errore seguenti. È possibile scegliere di interrompere l'attività di copia o continuare a copiare il resto negli scenari seguenti:

1. I file che devono essere copiati da ADF verranno contemporaneamente eliminati da altre applicazioni.
2. Alcune cartelle o file particolari non consentono l'accesso ad ADF perché gli ACL di tali file o cartelle richiedono un livello di autorizzazione superiore rispetto alle informazioni di connessione configurate nel file ADF.
3. Uno o più file non vengono verificati come coerenti tra l'archivio di origine e quello di destinazione se si abilita l'impostazione di verifica coerenza dati in ADF.

### <a name="configuration"></a>Configurazione 
Quando si copiano file binari tra gli archivi, è possibile abilitare la tolleranza di errore come segue: 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true 
    }, 
    "validateDataConsistency": true, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2", 
            "type": "LinkedServiceReference" 
            }, 
        "path": "sessionlog/" 
     } 
} 
```
Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | -------- 
skipErrorFile | Gruppo di proprietà per specificare i tipi di errori da ignorare durante lo spostamento dati. | | No
fileMissing | Una delle coppie chiave-valore all'interno del contenitore delle proprietà skipErrorFile per determinare se ignorare i file, che nel frattempo vengono eliminati da altre applicazioni durante la copia di Azure Data Factory. <br/> \- True: consente di copiare la parte restante ignorando i file eliminati da altre applicazioni. <br/> - False: consente di interrompere l'attività di copia dopo che i file sono stati eliminati dall'archivio di origine durante lo spostamento dati. <br/>Notare che, per impostazione predefinita, questa proprietà è impostata su True. | True (impostazione predefinita) <br/>False | No
fileForbidden | Una delle coppie chiave-valore all'interno del contenitore delle proprietà skipErrorFile per determinare se ignorare file specifici, quando gli ACL di tali file o cartelle richiedono un livello di autorizzazione superiore rispetto alla connessione configurata in ADF. <br/> \- True: permette di copiare il resto ignorando i file. <br/> - False: permette di interrompere l'attività di copia una volta rilevato il problema di autorizzazione per cartelle o file. | True <br/>False (impostazione predefinita) | No
dataInconsistency | Una delle coppie chiave-valore all'interno del contenitore delle proprietà skipErrorFile per determinare se ignorare i dati incoerenti tra l'archivio di origine e quello di destinazione. <br/> \- True: permette di copiare il resto ignorando i dati incoerenti. <br/> - False: permette di interrompere l'attività di copia una volta trovati dati incoerenti. <br/>Tenere presente che questa proprietà è valida solo quando si imposta validateDataConsistency su True. | True <br/>False (impostazione predefinita) | No
logStorageSettings  | Un gruppo di proprietà che può essere specificato quando si vuole registrare i nomi degli oggetti ignorati. | &nbsp; | No
linkedServiceName | Servizio collegato di [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) per archiviare i file di log della sessione. | Nomi di un servizio collegato di tipo `AzureBlobStorage` o `AzureBlobFS` che fa riferimento all'istanza da usare per archiviare il file di log. | No
path | Percorso dei file di log. | Specificare il percorso usato per archiviare i file di log. Se non si specifica un percorso, il servizio crea automaticamente un contenitore. | No

> [!NOTE]
> Di seguito sono riportati i prerequisiti per abilitare la tolleranza di errore nell'attività di copia durante la copia di file binari.
> Per ignorare file specifici quando vengono eliminati dall'archivio di origine:
> - Il set di dati di origine e il set di dati sink devono essere di formato binario e non è possibile specificare il tipo di compressione. 
> - I tipi di archivio dati supportati sono archiviazione BLOB di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, archiviazione file di Azure, file System, FTP, SFTP, Amazon S3, Google Cloud storage e HDFS.
> - Solo se quando si specificano più file nel set di dati di origine, che può essere una cartella, un carattere jolly o un elenco di file, l'attività di copia può ignorare i file di errore specifici. Se nel set di dati di origine viene specificato un singolo file da copiare nella destinazione, l'attività di copia avrà esito negativo se si verifica un errore.
>
> Per ignorare file specifici quando l'accesso non è consentito dall'archivio di origine:
> - Il set di dati di origine e il set di dati sink devono essere di formato binario e non è possibile specificare il tipo di compressione. 
> - I tipi di archivio dati supportati sono archiviazione BLOB di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, archiviazione file di Azure, SFTP, Amazon S3 e HDFS.
> - Solo se quando si specificano più file nel set di dati di origine, che può essere una cartella, un carattere jolly o un elenco di file, l'attività di copia può ignorare i file di errore specifici. Se nel set di dati di origine viene specificato un singolo file da copiare nella destinazione, l'attività di copia avrà esito negativo se si verifica un errore.
>
> Per ignorare file specifici quando vengono verificati come incoerenti tra l'archivio di origine e quello di destinazione:
> - [Qui](https://docs.microsoft.com/azure/data-factory/copy-activity-data-consistency)è possibile ottenere altri dettagli dalla documentazione sulla coerenza dei dati.

### <a name="monitoring"></a>Monitoraggio 

#### <a name="output-from-copy-activity"></a>Output dell'attività di copia
È possibile ottenere il numero di file letti, scritti e ignorati tramite l'output di ogni esecuzione dell'attività di copia. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>Log di sessione dell'attività di copia

Se si configura la registrazione dei nomi file ignorati, vedere il file di log al percorso seguente: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

I file di log possono essere solo file CSV. Lo schema del file di log è il seguente:

Colonna | Descrizione 
-------- | -----------  
Timestamp | Timestamp dell'ora in cui ADF ignora il file.
Level | Livello log dell'elemento. Si troverà nel livello "Avviso" per l'elemento che mostra il file ignorato.
OperationName | Comportamento operativo dell'attività di copia ADF in ogni file. Sarà "FileSkip" per specificare il file da ignorare.
OperationItem | Nomi file da ignorare.
Message | Altre informazioni per illustrare il motivo per cui il file viene ignorato.

Di seguito è riportato un esempio di file di log: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
Dal log precedente è possibile vedere che il file bigfile.csv è stato ignorato a causa di un'altra applicazione che ha eliminato il file al momento della copia di ADF. Il file 3_nopermission.txt è stato invece ignorato perché ADF non è autorizzato ad accedervi a causa di un problema di autorizzazione.


## <a name="copying-tabular-data"></a>Copia di dati tabulari 

### <a name="supported-scenarios"></a>Scenari supportati
L'attività di copia supporta tre scenari per rilevare, ignorare e registrare i dati tabulari incompatibili:

- **Incompatibilità tra il tipo di dati di origine e il tipo nativo sink**. 

    Ad esempio: si vogliono copiare dati da un file CSV nell'archiviazione BLOB a un database SQL con una definizione di schema che contiene tre colonne di tipo INT. Le righe del file CSV contenenti dati numerici, ad esempio 123, 456, 789, vengono copiate nell'archivio sink. Tuttavia, le righe che contengono valori non numerici, ad esempio 123, 456, abc, vengono rilevate come incompatibili e vengono ignorate.

- **Mancata corrispondenza nel numero di colonne tra l'origine e il sink**.

    Ad esempio: si vogliono copiare dati da un file CSV nell'archivio BLOB a un database SQL con una definizione di schema che contiene sei colonne. Le righe del file CSV che contengono sei colonne vengono copiate nell'archivio sink. Le righe del file CSV che contengono più di sei colonne vengono rilevate come incompatibili e vengono ignorate.

- **Violazione della chiave primaria per la scrittura in SQL Server/database SQL di Azure/Azure Cosmos DB**.

    Ad esempio: si vogliono copiare dati da un'istanza di SQL Server a un database SQL. Il database SQL del sink contiene la definizione di una chiave primaria, che invece manca nell'istanza di SQL Server di origine. Non è possibile copiare nel sink le righe duplicate presenti nell'origine. L'attività di copia copierà nel sink solo la prima riga dei dati di origine. Le righe di origine successive che contengono il valore della chiave primaria duplicato vengono rilevate come incompatibili e vengono ignorate.

>[!NOTE]
>- Per caricare dati in SQL Data Warehouse mediante PolyBase, configurare le impostazioni native della tolleranza di errore di PolyBase specificando i criteri di rifiuto tramite "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" nell'attività di copia. È comunque possibile abilitare il reindirizzamento delle righe incompatibili di PolyBase a BLOB o ADLS come di consueto, come illustrato di seguito.
>- Questa funzionalità non è applicabile quando è configurata l'attività di copia per richiamare lo strumento [Unload di Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Questa funzionalità non è applicabile quando l'attività di copia è configurata per richiamare [una stored procedure da un sink SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

### <a name="configuration"></a>Configurazione
L'esempio seguente offre la definizione JSON per specificare di ignorare le righe incompatibili nell'attività di copia:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logStorageSettings": { 
    "linkedServiceName": { 
        "referenceName": "ADLSGen2", 
        "type": "LinkedServiceReference" 
        }, 
    "path": "sessionlog/" 
    } 
}, 
```

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Specifica se ignorare o meno le righe incompatibili durante la copia. | True<br/>False (impostazione predefinita) | No
logStorageSettings | Un gruppo di proprietà che può essere specificato quando si vuole registrare le righe incompatibili. | &nbsp; | No
linkedServiceName | Servizio collegato di [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) con cui archiviare il log che contiene le righe ignorate. | Nomi di un servizio collegato di tipo `AzureBlobStorage` o `AzureBlobFS` che fa riferimento all'istanza da usare per archiviare il file di log. | No
path | Percorso del file di log che contiene le righe ignorate. | Specificare il percorso da usare per registrare i dati incompatibili. Se non si specifica un percorso, il servizio crea automaticamente un contenitore. | No

### <a name="monitor-skipped-rows"></a>Monitorare le righe ignorate
Al termine dell'esecuzione dell'attività di copia, è possibile visualizzare il numero di righe ignorate nell'output dell'attività di copia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

Se si configura la registrazione delle righe incompatibili, vedere il file di log al percorso seguente: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

I file di log sono file CSV. Lo schema del file di log è il seguente:

Colonna | Descrizione 
-------- | -----------  
Timestamp | Timestamp dell'ora in cui ADF ignora le righe incompatibili
Level | Livello log dell'elemento. Il livello sarà "Avviso" se questo elemento mostra le righe ignorate
OperationName | Comportamento operativo dell'attività di copia ADF in ogni riga. Sarà "TabularRowSkip" per specificare che la riga incompatibile specifica è stata ignorata
OperationItem | Righe ignorate dall'archivio dati di origine.
Message | Altre informazioni per illustrare il motivo dell'incompatibilità di questa riga specifica.


Di seguito è riportato un esempio del contenuto del file di log:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

Nel file di log di esempio riportato sopra, è possibile vedere che una riga "data1, data2, data3" è stata ignorata a causa di un problema di conversione tipo dall'archivio di origine a quello di destinazione. Un'altra riga "data4, data5, data6" è stata ignorata a causa di un problema di violazione della CP dall'archivio di origine a quello di destinazione. 


## <a name="copying-tabular-data-legacy"></a>Copia di dati tabulari (legacy):

Di seguito è riportata la modalità legacy per abilitare la tolleranza di errore per la sola copia di dati tabulari. Se si sta creando una nuova pipeline o attività, è consigliabile iniziare invece da [qui](#copying-tabular-data).

### <a name="configuration"></a>Configurazione
L'esempio seguente offre la definizione JSON per specificare di ignorare le righe incompatibili nell'attività di copia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Specifica se ignorare o meno le righe incompatibili durante la copia. | True<br/>False (impostazione predefinita) | No
redirectIncompatibleRowSettings | Un gruppo di proprietà che può essere specificato quando si vuole registrare le righe incompatibili. | &nbsp; | No
linkedServiceName | Servizio collegato di [Archiviazione di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) con cui archiviare il log che contiene le righe ignorate. | Nomi di un servizio collegato di tipo `AzureStorage` o `AzureDataLakeStore` che fa riferimento all'istanza da usare per archiviare il file di log. | No
path | Percorso del file di log che contiene le righe ignorate. | Specificare il percorso da usare per registrare i dati incompatibili. Se non si specifica un percorso, il servizio crea automaticamente un contenitore. | No

### <a name="monitor-skipped-rows"></a>Monitorare le righe ignorate
Al termine dell'esecuzione dell'attività di copia, è possibile visualizzare il numero di righe ignorate nell'output dell'attività di copia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Se si configura la registrazione delle righe incompatibili, vedere il file di log al percorso seguente: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

I file di log possono essere solo i file CSV. Se necessario, i dati originali ignorati verranno registrati con la virgola come delimitatore di colonna. Altre due colonne "ErrorCode" e "ErrorMessage" vengono aggiunte ai dati di origine originali nel file di log, in cui è possibile visualizzare la causa radice dell'incompatibilità. ErrorCode ed ErrorMessage verranno racchiusi tra virgolette doppie. 

Di seguito è riportato un esempio del contenuto del file di log:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Prestazioni dell'attività di copia](copy-activity-performance.md)


