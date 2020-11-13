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
ms.openlocfilehash: 3591bfe046fa1c3e1e55aa49a0ae3ad698bc57b3
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593672"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Verifica della coerenza dei dati nell'attività di copia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando si spostano i dati dall'archivio di origine a quello di destinazione, l'attività di copia di Azure Data Factory offre un'opzione che consente di eseguire ulteriori verifiche di coerenza dei dati per garantire che i dati non vengano copiati solo dall'archivio di origine all'archivio di destinazione, ma anche che siano coerenti tra l'archivio di origine e quello di destinazione. Una volta che durante lo spostamento dei dati sono stati rilevati file incoerenti, è possibile interrompere l'attività di copia o continuare a copiare il resto abilitando l'impostazione di tolleranza di errore per ignorare i file non coerenti. È possibile ottenere i nomi di file ignorati abilitando l'impostazione del log sessione nell'attività di copia. Per ulteriori informazioni, è possibile fare riferimento al [log di sessione nell'attività di copia](copy-activity-log.md) .

## <a name="supported-data-stores-and-scenarios"></a>Archivi dati e scenari supportati

-   La verifica della coerenza dei dati è supportata da tutti i connettori eccetto FTP, sFTP e HTTP. 
-   La verifica della coerenza dei dati non è supportata nello scenario di copia di staging.
-   Quando si copiano i file binari, la verifica di coerenza dei dati è disponibile solo quando il comportamento di ' PreserveHierarchy ' è impostato nell'attività di copia.
-   Quando si copiano più file binari in un'attività di copia singola con verifica della coerenza dei dati abilitata, è possibile interrompere l'attività di copia o continuare a copiare il resto abilitando l'impostazione di tolleranza di errore per ignorare i file non coerenti. 
-   Quando si copia una tabella in un'unica attività di copia con verifica coerenza dati abilitata, l'attività di copia ha esito negativo se il numero di righe lette dall'origine è diverso dal numero di righe copiate nella destinazione più il numero di righe incompatibili ignorate.


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
validateDataConsistency | Se si imposta true per questa proprietà, durante la copia dei file binari, l'attività di copia verificherà le dimensioni del file, lastModifiedDate e il checksum MD5 per ogni file binario copiato dall'archivio di origine nell'archivio di destinazione per assicurare la coerenza dei dati tra l'archivio di origine e quello di destinazione. Quando si copiano dati tabulari, l'attività di copia controllerà il conteggio totale delle righe dopo il completamento del processo per garantire che il numero totale di righe lette dall'origine corrisponda al numero di righe copiate nella destinazione e al numero di righe non compatibili ignorate. Si ricorda che le prestazioni di copia saranno influenzate dall'abilitazione di questa opzione.  | True<br/>False (impostazione predefinita) | No
dataInconsistency | Una delle coppie chiave-valore all'interno del contenitore delle proprietà skipErrorFile per determinare se si desidera ignorare i file incoerenti. <br/> -True: si vuole copiare il resto ignorando i file incoerenti.<br/> -False: si desidera interrompere l'attività di copia dopo che è stato trovato un file incoerente.<br/>Tenere presente che questa proprietà è valida solo quando si copiano file binari e si imposta validateDataConsistency su true.  | True<br/>False (impostazione predefinita) | No
logSettings | Gruppo di proprietà che è possibile specificare per consentire al log sessione di registrare i file ignorati. | | No
linkedServiceName | Servizio collegato di [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) per archiviare i file di log della sessione. | Nomi di un servizio collegato di tipo `AzureBlobStorage` o `AzureBlobFS` che fa riferimento all'istanza da usare per archiviare i file di log. | No
path | Percorso dei file di log. | Specificare il percorso desiderato per archiviare i file di log. Se non si specifica un percorso, il servizio crea automaticamente un contenitore. | No

>[!NOTE]
>- Quando si copiano file binari da o in BLOB di Azure o in Azure Data Lake Storage Gen2, ADF esegue la verifica del checksum MD5 a livello di blocco sfruttando l' [API blob di Azure](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy) e l' [API Azure Data Lake storage Gen2](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers). Se sono presenti ContentMD5 nei file nel BLOB di Azure o Azure Data Lake Storage Gen2 come origini dati, ADF esegue la verifica del checksum MD5 a livello di file anche dopo aver letto i file. Dopo aver copiato i file nel BLOB di Azure o Azure Data Lake Storage Gen2 come destinazione dati, ADF scrive ContentMD5 nel BLOB di Azure o in Azure Data Lake Storage Gen2, che può essere ulteriormente utilizzato dalle applicazioni downstream per la verifica della coerenza dei dati.
>- ADF esegue la verifica delle dimensioni dei file durante la copia di file binari tra gli archivi di archiviazione.

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
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```
È possibile visualizzare i dettagli della verifica di coerenza dei dati da "Proprietà dataConsistencyVerification".

Valore di **VerificationResult** : 
-   **Verificato** :  È stato verificato che i dati copiati siano coerenti tra l'archivio di origine e quello di destinazione. 
-   **Non verificato** : I dati copiati non sono stati verificati come coerenti perché validateDataConsistency non è stato abilitato nell'attività di copia. 
-   **Non supportato** : I dati copiati non sono stati verificati come coerenti perché la verifica della coerenza dei dati non è supportata per questa particolare coppia di copia. 

Valore di **InconsistentData** : 
-   **Trovato** : L'attività di copia di ADF ha rilevato dati incoerenti. 
-   **Ignorata** : L'attività di copia di ADF ha rilevato e ignorato i dati incoerenti. 
-   **Nessuna** : L'attività di copia di ADF non ha trovato dati incoerenti. Questo può essere dovuto al fatto che i dati sono stati verificati come coerenti tra l'archivio di origine e di destinazione o perché è stato disabilitato validateDataConsistency nell'attività di copia. 

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