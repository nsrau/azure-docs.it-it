---
title: Caricare grandi quantità in Azure Data Lake Storage Gen1 usando i metodi offline | Microsoft Docs
description: Usare lo strumento AdlCopy per copiare i dati dai BLOB di Archiviazione di Azure ad Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4a8126d658f227d9eed372cd51cf06f8f12c99f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194978"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-azure-data-lake-storage-gen1"></a>Usare il servizio Importazione/Esportazione di Microsoft Azure per la copia offline dei dati in Azure Data Lake Storage Gen1
Questo articolo contiene informazioni su come copiare set di dati di grandi dimensioni (> 200 GB) in Azure Data Lake Storage Gen1 usando metodi di copia offline, ad esempio il [servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md). In particolare, il file usato come esempio in questo articolo ha una dimensione di 339.420.860.416 byte, vale a dire circa 319 GB sul disco. Chiameremo questo file 319GB.tsv.

Il servizio Importazione/Esportazione di Azure consente di trasferire in modo sicuro grandi quantità di dati nell'archiviazione BLOB di Azure attraverso la spedizione delle unità disco rigido a un data center di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di archiviazione di Azure**.
* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come crearne uno, vedere [Iniziare a usare Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Preparazione dei dati

Prima di usare il servizio di importazione/esportazione, è necessario suddividere il file di dati da trasferire **in copie di dimensioni inferiori a 200 GB**. Lo strumento di importazione non funziona con file di dimensioni superiori a 200 GB. In questa esercitazione il file viene suddiviso in blocchi di 100 GB. A tale scopo è possibile usare [Cygwin](https://cygwin.com/install.html). Cygwin supporta i comandi di Linux. In questo caso usare il comando seguente:

    split -b 100m 319GB.tsv

L'operazione di suddivisione crea file con i nomi riportati di seguito.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Preparare i dischi con i dati
Per preparare le unità disco rigido, seguire le istruzioni in [Usare il servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md), sezione **Preparare le unità**. Di seguito è riportata la sequenza generale:

1. Procurarsi un disco rigido che soddisfi i requisiti per l'uso con il servizio di Importazione/Esportazione di Azure.
2. Identificare un account di archiviazione di Azure in cui verranno copiati i dati dopo la spedizione al data center di Azure.
3. Usare lo [strumento di importazione/esportazione di Azure](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), un'utilità della riga di comando. Di seguito è riportato un frammento di codice di esempio che indica come usare lo strumento.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Per altri frammenti di codice di esempio, vedere [Usare il servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md).
4. Il comando precedente crea un file journal nel percorso specificato. Usare questo file journal per creare un processo di importazione dal [portale di Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>Creare un processo di importazione
È ora possibile creare un processo di importazione usando le istruzioni in [Usare il servizio Importazione/Esportazione di Azure](../storage/common/storage-import-export-service.md), sezione **Creare il processo di importazione**. Per questo processo di importazione fornire, oltre ad altri dettagli, anche il file journal creato durante la preparazione delle unità disco.

## <a name="physically-ship-the-disks"></a>Spedire fisicamente i dischi
A questo punto è possibile spedire fisicamente i dischi a un data center di Azure, dove i dati verranno copiati nei BLOB di Archiviazione di Azure specificato durante la creazione del processo di importazione. Inoltre, se durante la creazione del processo si è scelto di inserire le informazioni di rilevamento in un secondo momento, è possibile tornare al processo di importazione e aggiornare il numero di tracciabilità.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Copiare i dati da BLOB di Archiviazione di Azure ad Azure Data Lake Storage Gen1
Completato il processo di importazione, è possibile verificare se i dati sono disponibili nei BLOB di Archiviazione di Azure specificati. È quindi possibile usare diversi metodi per trasferire i dati dai BLOB ad Azure Data Lake Storage Gen1. Per tutte le opzioni disponibili per il caricamento di dati, vedere [Inserire i dati in Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

In questa sezione sono riportate le definizioni JSON che è possibile usare per creare una pipeline di Azure Data Factory per la copia dei dati. È possibile usare queste definizioni JSON dal [portale di Azure](../data-factory/tutorial-copy-data-portal.md) o da [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Servizio collegato all'origine (BLOB di Archiviazione di Azure)
```
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-azure-data-lake-storage-gen1"></a>Servizio collegato alla destinazione (Azure Data Lake Storage Gen1)
```
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```
### <a name="input-data-set"></a>Set di dati di input
```
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
### <a name="output-data-set"></a>Set di dati di output
```
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```
### <a name="pipeline-copy-activity"></a>Pipeline (attività di copia)
```
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```
Per altre informazioni, vedere l'articolo sullo [Spostare i dati dal BLOB di Archiviazione di Azure ad Azure Data Lake Storage Gen1 con Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-storage-gen1"></a>Ricostruire i file di dati in Azure Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il file originale da 319 GB è stato suddiviso in file di dimensioni minori, in modo che possano essere trasferiti usando il servizio di Importazione/Esportazione di Azure. Ora che i dati sono in Azure Data Lake Storage Gen1, è possibile riportare il file alla dimensione originale. A tale scopo, è possibile usare i cmdlet di Azure PowerShell seguenti.

```
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
