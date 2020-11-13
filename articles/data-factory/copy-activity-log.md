---
title: Log sessione nell'attività di copia
description: Informazioni su come abilitare il log di sessione nell'attività di copia in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: e56a840da07a2f6e966867699506f0122a0e7956
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593646"
---
#  <a name="session-log-in-copy-activity"></a>Log sessione nell'attività di copia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

È possibile registrare i nomi di file copiati nell'attività di copia, in modo da garantire che i dati non vengano copiati solo dall'archivio di origine all'archivio di destinazione, ma anche coerenti tra l'archivio di origine e quello di destinazione, esaminando i file copiati nei log delle sessioni dell'attività di copia.  

Quando si Abilita l'impostazione della tolleranza di errore nell'attività di copia per ignorare i dati difettosi, è possibile registrare anche i file ignorati e le righe ignorate.  È possibile ottenere altri dettagli dalla [tolleranza di errore nell'attività di copia](copy-activity-fault-tolerance.md). 

## <a name="configuration"></a>Configurazione
Nell'esempio seguente viene fornita una definizione JSON per abilitare il log di sessione nell'attività di copia: 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
    },
    "validateDataConsistency": true,
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
}
```

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | -------- 
enableCopyActivityLog | Quando viene impostata su true, si avrà la possibilità di registrare i file copiati, i file ignorati o le righe ignorate.  | True<br/>False (impostazione predefinita) | No
logLevel | "Informazioni" registrerà tutti i file copiati, i file ignorati e le righe ignorate. "Avviso" registrerà i file ignorati e solo le righe ignorate.  | Info<br/>Warning (impostazione predefinita) | No
enableReliableLogging | Quando è true, l'attività di copia in modalità affidabile eliminerà i log immediatamente dopo la copia di ogni file nella destinazione.  Quando si copiano grandi quantità di file con la modalità di registrazione affidabile abilitata nell'attività di copia, è necessario aspettarsi che la velocità effettiva di copia venga interessata, perché le operazioni di scrittura doppie sono necessarie per ogni copia del file. Una richiesta è l'archivio di destinazione e un'altra richiesta è nell'archivio di archiviazione dei log.  L'attività di copia in modalità di utilizzo ottimale eliminerà i log con batch di record in un determinato periodo di tempo, in cui la velocità effettiva di copia sarà molto meno interessata. La completezza e la tempestività di registrazione non sono garantite in questa modalità, perché esistono alcune possibilità che l'ultimo batch di eventi di log non sia stato scaricato nel file di log quando l'attività di copia non è riuscita. A questo punto, verranno visualizzati alcuni file copiati nella destinazione non registrati.  | True<br/>False (impostazione predefinita) | No
logLocationSettings | Gruppo di proprietà che è possibile utilizzare per specificare il percorso in cui archiviare i log di sessione. | | No
linkedServiceName | Servizio collegato di [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) per archiviare i file di log della sessione. | Nomi di un servizio collegato di tipo `AzureBlobStorage` o `AzureBlobFS` che fa riferimento all'istanza da usare per archiviare i file di log. | No
path | Percorso dei file di log. | Specificare il percorso desiderato per archiviare i file di log. Se non si specifica un percorso, il servizio crea automaticamente un contenitore. | No


## <a name="monitoring"></a>Monitoraggio

### <a name="output-from-copy-activity"></a>Output dell'attività di copia
Quando l'attività di copia viene eseguita completamente, è possibile visualizzare il percorso dei file di log dall'output di ogni esecuzione dell'attività di copia. È possibile trovare i file di log nel percorso: `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  I file di log sono file CSV. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

### <a name="the-schema-of-the-log-file"></a>Schema del file di log

Di seguito è riportato lo schema di un file di log.

Colonna | Descrizione 
-------- | -----------  
Timestamp | Timestamp durante la lettura, la scrittura o l'omissione dell'oggetto da parte di ADF.
Level | Livello log dell'elemento. Può essere "Warning" o "info".
OperationName | Comportamento operativo dell'attività di copia ADF su ogni oggetto. Può essere ' FileRead ',' filewrite ',' FileSkip ' o ' TabularRowSkip '.
OperationItem | Nomi file o righe ignorate.
Messaggio | Ulteriori informazioni per mostrare se il file è stato letto dall'archivio di origine o è stato scritto nell'archivio di destinazione. Può anche essere il motivo per cui il file o le righe sono state ignorate.

Di seguito è riportato un esempio di un file di log. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
Dal file di log riportato sopra, è possibile vedere che sample1.csv è stato ignorato perché non è stato possibile verificarne la coerenza tra l'archivio di origine e quello di destinazione. È possibile ottenere altre informazioni sui motivi per cui sample1.csv diventa incoerente, cioè perché è stato modificato da altre applicazioni quando l'attività di copia di ADF esegue la copia nello stesso momento. È anche possibile vedere che sample2.csv è stato copiato correttamente dall'archivio di origine a quello di destinazione.

È possibile utilizzare più motori di analisi per analizzare ulteriormente i file di log.  Di seguito sono riportati alcuni esempi per usare query SQL per analizzare il file di log importando il file di log CSV nel database SQL in cui il nome della tabella può essere SessionLogDemo.  

-   Assegnami l'elenco dei file copiati. 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   Visualizza l'elenco dei file copiato in un intervallo di tempo specifico. 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   Fornire un file specifico con l'ora e i metadati copiati. 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   Fornire un elenco di file con i relativi metadati copiati in un intervallo di tempo. 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   Visualizza l'elenco dei file ignorati. 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   Fornire il motivo per cui un determinato file è stato ignorato. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   Consente di visualizzare l'elenco dei file ignorati a causa dello stesso motivo: "il file BLOB non esiste". 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   Specificare il nome del file che richiede il tempo più lungo per la copia.
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Tolleranza di errore dell'attività di copia](copy-activity-fault-tolerance.md)
- [Coerenza dei dati dell'attività di copia](copy-activity-data-consistency.md)