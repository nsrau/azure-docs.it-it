---
title: Mantenere i metadati e gli ACL usando l'attività di copia in Azure Data Factory
description: Informazioni su come mantenere i metadati e gli ACL durante la copia usando l'attività di copia in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: jingwang
ms.openlocfilehash: 5ce1b85394a7bb604841f7fb941bdebf12c0bca2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414161"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Mantenere i metadati e gli ACL usando l'attività di copia in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando si utilizza Azure Data Factory attività di copia per copiare i dati dall'origine al sink, negli scenari seguenti è inoltre possibile conservare i metadati e gli ACL insieme a.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>Mantieni i metadati per la migrazione del Lake

Quando si esegue la migrazione dei dati da un data Lake a un altro, tra cui [Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md)e [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), è possibile scegliere di mantenere i metadati dei file insieme ai dati.

L'attività di copia supporta la conservazione dei seguenti attributi durante la copia dei dati:

- **Tutti i metadati specificati dal cliente** 
- E le **cinque proprietà di sistema predefinite dell'archivio dati**seguenti: `contentType`, `contentLanguage` (ad eccezione di Amazon S3) `contentEncoding`, `contentDisposition`, `cacheControl`,.

Quando si copiano i file così come sono da Amazon S3/Azure Data Lake storage Gen2/BLOB di Azure al BLOB di Azure Data Lake storage Gen2/Azure con formato binario, è possibile trovare l'opzione **Mantieni** nella scheda**Impostazioni** **attività** > di copia per la creazione di attività o la pagina **Impostazioni** in copia dati strumento.

![Conserva metadati dell'attività di copia](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Di seguito è riportato un esempio di configurazione JSON dell'attività `preserve`di copia (vedere): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>Mantieni gli ACL da Data Lake Storage Gen1/Gen2 a Gen2

Quando si esegue l'aggiornamento da Azure Data Lake Storage Gen1 a Gen2 o si copiano dati tra ADLS Gen2, è possibile scegliere di mantenere gli elenchi di controllo di accesso (ACL) POSIX insieme ai file di dati. Per altre informazioni sul controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-access-control.md) e [controllo di accesso in Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

L'attività di copia supporta la conservazione dei seguenti tipi di ACL durante la copia dei dati. È possibile selezionare uno o più tipi:

- **ACL**: copiare e mantenere gli elenchi di controllo di accesso POSIX su file e directory. Copia gli ACL completi esistenti dall'origine al sink. 
- **Proprietario**: copiare e mantenere l'utente proprietario dei file e delle directory. È necessario l'accesso con privilegi avanzati ai Data Lake Storage Gen2 sink.
- **Gruppo**: copiare e mantenere il gruppo proprietario di file e directory. È necessario l'accesso con privilegi avanzati a sink Data Lake Storage Gen2 o all'utente proprietario (se l'utente proprietario è anche membro del gruppo di destinazione).

Se si specifica di copiare da una cartella, Data Factory replica gli ACL per la cartella specificata e i file e le directory al suo interno, `recursive` se è impostato su true. Se si specifica di copiare da un singolo file, vengono copiati gli ACL in tale file.

>[!NOTE]
>Quando si usa ADF per mantenere gli ACL da Data Lake Storage Gen1/Gen2 a Gen2, gli ACL esistenti in sink Gen2's cartella/file corrispondenti verranno sovrascritti.

>[!IMPORTANT]
>Quando si sceglie di mantenere gli ACL, assicurarsi di concedere autorizzazioni sufficientemente elevate affinché Data Factory funzionino con l'account di Data Lake Storage Gen2 del sink. Ad esempio, usare l'autenticazione con chiave dell'account o assegnare il ruolo di proprietario dei dati BLOB di archiviazione all'entità servizio o all'identità gestita.

Quando si configura l'origine come data Lake storage Gen1/Gen2 con il formato binario o l'opzione di copia binaria e si esegue il sink come data Lake storage Gen2 con formato binario o l'opzione di copia binaria, è possibile trovare l'opzione **Mantieni** nella pagina **Impostazioni** in copia dati strumento o nella scheda**Impostazioni** **attività** > di copia per la creazione di attività.

![Data Lake Storage Gen1/Gen2 a Gen2 Mantieni ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Di seguito è riportato un esempio di configurazione JSON dell'attività `preserve`di copia (vedere): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
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
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Prestazioni dell'attività di copia](copy-activity-performance.md)
