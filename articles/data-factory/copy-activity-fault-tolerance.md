---
title: Tolleranza di errore dell'attività di copia in Azure Data Factory | Microsoft Docs
description: Informazioni su come aggiungere la tolleranza di errore all'attività di copia in Azure Data Factory ignorando le righe incompatibili.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: jingwang
ms.openlocfilehash: f1a40c09c2d08eddedd3b6b51d2a138ec403f6bc
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014914"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolleranza di errore dell'attività di copia in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versione corrente](copy-activity-fault-tolerance.md)

L'attività di copia di Azure Data Factory offre due modi per gestire le righe incompatibili durante la copia di dati tra gli archivi dati di origine e sink:

- È possibile interrompere l'attività di copia quando vengono rilevati dati incompatibili (comportamento predefinito).
- È possibile continuare a copiare tutti i dati aggiungendo la tolleranza di errore e ignorando le righe di dati incompatibili. È anche possibile registrare le righe incompatibili nell'archivio BLOB di Azure o in Azure Data Lake Store. È quindi possibile esaminare il log per conoscere la causa dell'errore, correggere i dati nell'origine dati e ripetere l'attività di copia.

## <a name="supported-scenarios"></a>Scenari supportati
L'attività di copia supporta tre scenari per rilevare, ignorare e registrare i dati incompatibili:

- **Incompatibilità tra il tipo di dati di origine e il tipo nativo sink**. 

    Ad esempio:  si vogliono copiare dati da un file CSV nell'archiviazione BLOB a un database SQL con una definizione di schema che contiene tre colonne di tipo INT. Le righe del file CSV contenenti dati numerici, ad esempio 123, 456, 789, vengono copiate nell'archivio sink. Tuttavia, le righe che contengono valori non numerici, ad esempio 123, 456, abc, vengono rilevate come incompatibili e vengono ignorate.

- **Mancata corrispondenza nel numero di colonne tra l'origine e il sink**.

    Ad esempio:  si vogliono copiare dati da un file CSV nell'archivio BLOB a un database SQL con una definizione di schema che contiene sei colonne. Le righe del file CSV che contengono sei colonne vengono copiate nell'archivio sink. Le righe del file CSV che contengono più o meno di sei colonne vengono rilevate come incompatibili e vengono ignorate.

- **Violazione della chiave primaria per la scrittura in SQL Server/database SQL di Azure/Azure Cosmos DB**.

    Ad esempio:  si vogliono copiare dati da un'istanza di SQL Server a un database SQL. Il database SQL del sink contiene la definizione di una chiave primaria, che invece manca nell'istanza di SQL Server di origine. Non è possibile copiare nel sink le righe duplicate presenti nell'origine. L'attività di copia copierà nel sink solo la prima riga dei dati di origine. Le righe di origine successive che contengono il valore della chiave primaria duplicato vengono rilevate come incompatibili e vengono ignorate.

>[!NOTE]
>- Per caricare dati in SQL Data Warehouse mediante PolyBase, configurare le impostazioni native della tolleranza di errore di PolyBase specificando i criteri di rifiuto tramite "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" nell'attività di copia. È comunque possibile abilitare il reindirizzamento delle righe incompatibili di PolyBase a BLOB o ADLS come di consueto, come illustrato di seguito.
>- Questa funzionalità non è applicabile quando è configurata l'attività di copia per richiamare lo strumento [Unload di Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).


## <a name="configuration"></a>Configurazione
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

Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Specifica se ignorare o meno le righe incompatibili durante la copia. | True <br/>False (impostazione predefinita) | No 
redirectIncompatibleRowSettings | Un gruppo di proprietà che può essere specificato quando si vuole registrare le righe incompatibili. | &nbsp; | No 
linkedServiceName | Servizio collegato di [Archiviazione di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) con cui archiviare il log che contiene le righe ignorate. | Nome di un servizio collegato `AzureStorage` o `AzureDataLakeStore` che fa riferimento all'istanza da usare per archiviare il file di log. | No 
path | Percorso del file di log che contiene le righe ignorate. | Specificare il percorso da usare per registrare i dati incompatibili. Se non si specifica un percorso, il servizio crea automaticamente un contenitore. | No 

## <a name="monitor-skipped-rows"></a>Monitorare le righe ignorate
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


