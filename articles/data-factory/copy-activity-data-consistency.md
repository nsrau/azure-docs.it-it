---
title: Verifica della coerenza dei dati nell'attività di copia
description: Informazioni su come abilitare la verifica della coerenza dei dati nell'attività di copia in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: a386c7d44cf5ba7eda895006cda7ce1fa9b798ac
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663709"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>Verifica della coerenza dei dati nell'attività di copia (anteprima)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando si spostano i dati dall'archivio di origine a quello di destinazione, l'attività di copia di Azure Data Factory offre un'opzione che consente di eseguire ulteriori verifiche di coerenza dei dati per garantire che i dati non vengano copiati solo dall'archivio di origine all'archivio di destinazione, ma anche che siano coerenti tra l'archivio di origine e quello di destinazione. Se vengono trovati dati incoerenti durante lo spostamento dei dati, è possibile interrompere l'attività di copia o continuare a copiare il resto abilitando l'impostazione di tolleranza di errore per ignorare i dati incoerenti. È possibile ottenere i nomi degli oggetti ignorati abilitando l'impostazione del log della sessione nell'attività di copia. 

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima con le limitazioni seguenti a cui stiamo lavorando attivamente:
>- La verifica della coerenza dei dati è disponibile solo per i file binari che esegue la copia tra archivi basati su file con comportamento "PreserveHierarchy" nell'attività di copia. Per la copia dei dati tabulari, la verifica della coerenza dei dati non è ancora disponibile nell'attività di copia.
>- Quando si abilita l'impostazione del log di sessione nell'attività di copia per registrare i file incoerenti ignorati, la completezza del file di log non può essere garantita al 100% se l'attività di copia non è riuscita.
>- Il log di sessione contiene solo file incoerenti, in cui i file copiati correttamente non vengono registrati fino a questo momento.

## <a name="supported-data-stores"></a>Archivi dati supportati

### <a name="source-data-stores"></a>Archivi dati di origine

-   [Archivio BLOB di Azure](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Archiviazione file di Azure](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [File system](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>Archivio dati di destinazione

-   [Archivio BLOB di Azure](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Archiviazione file di Azure](connector-azure-file-storage.md)
-   [File system](connector-file-system.md)


## <a name="configuration"></a>Configurazione
Nell'esempio seguente viene fornita una definizione JSON per abilitare la verifica della coerenza dei dati nell'attività di copia: 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2_storage", 
            "type": "LinkedServiceReference" 
        }, 
        "path": "/sessionlog/" 
} 
} 
```

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | -------- 
validateDataConsistency | Se si imposta true per questa proprietà, l'attività di copia verificherà le dimensioni del file, lastModifiedDate, e il checksum MD5 per ogni oggetto copiato dall'archivio di origine nell'archivio di destinazione per assicurare la coerenza dei dati tra l'archivio di origine e quello di destinazione. Si ricorda che le prestazioni di copia saranno influenzate dall'abilitazione di questa opzione.  | True<br/>False (impostazione predefinita) | No
dataInconsistency | Una delle coppie chiave-valore all'interno del contenitore delle proprietà skipErrorFile per determinare se ignorare i dati incoerenti.<br/> \- True: permette di copiare il resto ignorando i dati incoerenti.<br/> - False: permette di interrompere l'attività di copia una volta trovati dati incoerenti.<br/>Tenere presente che questa proprietà è valida solo quando si imposta validateDataConsistency su True.  | True<br/>False (impostazione predefinita) | No
logStorageSettings | Un gruppo di proprietà che può essere specificato per abilitare il log di sessione per registrare gli oggetti ignorati. | | No
linkedServiceName | Servizio collegato di [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) per archiviare i file di log della sessione. | Nomi di un servizio collegato di tipo `AzureBlobStorage` o `AzureBlobFS` che fa riferimento all'istanza da usare per archiviare i file di log. | No
path | Percorso dei file di log. | Specificare il percorso desiderato per archiviare i file di log. Se non si specifica un percorso, il servizio crea automaticamente un contenitore. | No

>[!NOTE]
>- La coerenza dei dati non è supportata nello scenario di copia di staging. 
>- Quando si copiano file binari da qualsiasi archivio di archiviazione in Archiviazione BLOB di Azure o Azure Data Lake Storage Gen2, l'attività di copia esegue la verifica delle dimensioni del file e del checksum MD5 per assicurare la coerenza dei dati tra gli archivi di origine e di destinazione. 
>- Quando si copiano i file binari da qualsiasi archivio di archiviazione in qualsiasi di archiviazione diversi dall'Archiviazione BLOB di Azure o Azure Data Lake Storage Gen2, l'attività di copia verifica le dimensioni dei file per garantire la coerenza dei dati tra l'archivio di origine e quello di destinazione.


## <a name="monitoring"></a>Monitoraggio

### <a name="output-from-copy-activity"></a>Output dell'attività di copia
Quando l'attività di copia viene eseguita completamente, è possibile visualizzare il risultato della verifica della coerenza dei dati dall'output di ciascuna esecuzione dell'attività di copia:

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
È possibile visualizzare i dettagli della verifica di coerenza dei dati da "Proprietà dataConsistencyVerification".

Valore di **VerificationResult**: 
-   **Verificato**:  È stato verificato che i dati copiati siano coerenti tra l'archivio di origine e quello di destinazione. 
-   **Non verificato**: I dati copiati non sono stati verificati come coerenti perché validateDataConsistency non è stato abilitato nell'attività di copia. 
-   **Non supportato**: I dati copiati non sono stati verificati come coerenti perché la verifica della coerenza dei dati non è supportata per questa particolare coppia di copia. 

Valore di **InconsistentData**: 
-   **Trovato**: L'attività di copia di ADF ha rilevato dati incoerenti. 
-   **Ignorata**: L'attività di copia di ADF ha rilevato e ignorato i dati incoerenti. 
-   **Nessuna**: L'attività di copia di ADF non ha trovato dati incoerenti. Questo può essere dovuto al fatto che i dati sono stati verificati come coerenti tra l'archivio di origine e di destinazione o perché è stato disabilitato validateDataConsistency nell'attività di copia. 

### <a name="session-log-from-copy-activity"></a>Log di sessione dell'attività di copia

Se si configura la registrazione dei file incoerenti, vedere il file di log al percorso seguente: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`.  I file di log sono file CSV. 

Lo schema di un file di log è il seguente:

Colonna | Descrizione 
-------- | -----------  
Timestamp | Timestamp dell'ora in cui ADF ignora i file incoerenti.
Level | Livello log dell'elemento. Si troverà nel livello "Avviso" per l'elemento che mostra il file ignorato.
OperationName | Comportamento operativo dell'attività di copia ADF in ogni file. Sarà "FileSkip" per specificare il file da ignorare.
OperationItem | Il nome file da ignorare.
Message | Altre informazioni per illustrare il motivo per cui i file vengono ignorati.

Di seguito è riportato un esempio di file di log: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
Dal file di log riportato sopra, è possibile vedere che sample1.csv è stato ignorato perché non è stato possibile verificarne la coerenza tra l'archivio di origine e quello di destinazione. È possibile ottenere altre informazioni sui motivi per cui sample1.csv diventa incoerente, cioè perché è stato modificato da altre applicazioni quando l'attività di copia di ADF esegue la copia nello stesso momento. 



## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Tolleranza di errore dell'attività di copia](copy-activity-fault-tolerance.md)


