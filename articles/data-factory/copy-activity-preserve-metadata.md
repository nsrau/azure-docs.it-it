---
title: Conservare metadati e ACL usando l'attività di copia in Azure Data FactoryPreserve metadata and ACLs using copy activity in Azure Data Factory
description: Informazioni su come conservare i metadati e gli ACL durante la copia usando l'attività di copia in Azure Data Factory.Learn about how to preserve metadata and ACLs during copy using copy activity in Azure Data Factory.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414161"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Conservare metadati e ACL usando l'attività di copia in Azure Data FactoryPreserve metadata and ACLs using copy activity in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando si usa l'attività di copia di Azure Data Factory per copiare i dati dall'origine al sink, negli scenari seguenti è anche possibile mantenere i metadati e gli ACL.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>Conservare i metadati per la migrazione del lago

Quando si esegue la migrazione dei dati da un data lake a un altro, inclusi [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md)e Azure Data Lake [Storage Gen2](connector-azure-data-lake-storage.md), è possibile scegliere di mantenere i metadati del file insieme ai dati.

L'attività di copia supporta la conservazione dei seguenti attributi durante la copia dei dati:

- **Tutti i metadati specificati dal cliente** 
- E i seguenti cinque archivi di `contentType`dati `contentLanguage` incorporati proprietà di `contentEncoding` **sistema** `cacheControl`: , (ad eccezione di Amazon S3), , `contentDisposition`.

Quando si copiano i file così come sono da Amazon S3/Azure Data Lake Storage Gen2/Azure Blob in Azure Data Lake Storage Gen2/Azure Blob con formato binario, è possibile trovare l'opzione **Mantieni** nella scheda **Copia impostazioni attività** > **Settings** per la creazione di attività o nella pagina **Impostazioni** in Strumento Copia dati.

![L'attività di copia conserva i metadati](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Ecco un esempio di configurazione JSON `preserve`dell'attività di copia (vedere ): 

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

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>Conservare gli ACL da Data Lake Storage Gen1/Gen2 a Gen2

Quando si esegue l'aggiornamento da Azure Data Lake Storage Gen1 a Gen2 o si copiano dati tra ADLS Gen2, è possibile scegliere di mantenere gli elenchi di controllo di accesso (ACL) POSIX insieme ai file di dati. Per altre informazioni sul controllo degli accessi, vedere [Controllo dell'accesso in Archiviazione dati di Azure Esettore Evitabile Gen1](../data-lake-store/data-lake-store-access-control.md) e Controllo degli accessi [in Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-access-control.md)

L'attività di copia supporta il mantenimento dei seguenti tipi di ACL durante la copia dei dati. È possibile selezionare uno o più tipi:

- **ACL**: Copiare e mantenere gli elenchi di controllo di accesso POSIX su file e directory. Copia gli ACL esistenti completi dall'origine al sink. 
- **Proprietario**: Copia e conserva l'utente proprietario di file e directory. È necessario l'accesso utente con privilegi avanzati al sink Data Lake Storage Gen2.
- **Gruppo**: Copia e conserva il gruppo proprietario di file e directory. È necessario l'accesso utente con privilegi avanzati per l'affondare Data Lake Storage Gen2 o l'utente proprietario (se l'utente proprietario è anche un membro del gruppo di destinazione).

Se si specifica di copiare da una cartella, Data Factory replica gli ACL per la `recursive` cartella specificata e i file e le directory in essa indicati, se è impostato su true. Se si specifica di copiare da un singolo file, vengono copiati gli ACL su tale file.

>[!NOTE]
>Quando si utilizza ADF per mantenere gli ACL da Data Lake Storage Gen1/Gen2 a Gen2, gli ACL esistenti nella cartella o nei file corrispondenti del sink Gen2 verranno sovrascritti.

>[!IMPORTANT]
>Quando si sceglie di mantenere gli ACL, assicurarsi di concedere autorizzazioni sufficientemente elevate per il funzionamento di Data Factory sull'account Data Lake Storage Gen2 del sink. Ad esempio, usare l'autenticazione con chiave dell'account o assegnare il ruolo Proprietario dati BLOB di archiviazione all'entità servizio o all'identità gestita.

Quando si configura l'origine come origine come Data Lake Storage Gen1/Gen2 con il formato binario o l'opzione di copia binaria e si esegue il sink come Data Lake Storage Gen2 con il formato binario o l'opzione di copia binaria, è possibile trovare l'opzione **Preserve** nella pagina **Impostazioni** in Copia strumento dati o nella scheda **Copia impostazioni attività** > **Settings** per la creazione di attività.

![ACL di archiviazione dati da cui la generazione1/da gen2 a Gen2](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Ecco un esempio di configurazione JSON `preserve`dell'attività di copia (vedere ): 

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
