---
title: Formato parquet in Azure Data Factory | Microsoft Docs
description: Questo argomento descrive come gestire con formato Parquet in Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 360b794f0d8ba9c145a92f015f264eb624fbb0f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65144888"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet in Azure Data Factory

Seguire questo articolo quando si desidera **analizzare i file Parquet o scrivere i dati nel formato Parquet**. 

Formato parquet è supportato per i connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), [Blob di Azure](connector-azure-blob-storage.md), [Azure Data Lake Store Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md), [archiviazione File di Azure](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Parquet.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà type del set di dati deve essere impostata su **Parquet**. | Yes      |
| location         | Impostazioni del percorso dei file. Ogni connettore basato su file dispone del proprio tipo di posizione e supportate le proprietà in `location`. **Vedere i dettagli nell'articolo del connettore -> sezione Proprietà set di dati**. | Yes      |
| compressionCodec | Il codec di compressione da usare durante la scrittura di file Parquet. Durante la lettura dal file Parquet, Data Factory determina automaticamente il codec di compressione in base ai metadati di file.<br>Tipi supportati sono "**none**","**gzip**","**snappy**" (predefinito), e "**lzo**". Si noti attualmente nepodporuje funkci LZO attività di copia. | No       |

> [!NOTE]
> Spazi vuoti nel nome della colonna non è supportato per i file Parquet.

Di seguito è riportato un esempio di set di dati Parquet in archiviazione Blob di Azure:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Parquet e sink.

### <a name="parquet-as-source"></a>Parquet come origine

Nell'attività di copia sono supportate le proprietà seguenti ***\*sorgente\**** sezione.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata su **ParquetSource**. | Yes      |
| storeSettings | Un gruppo di proprietà sulla lettura dei dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings`. **Vedere i dettagli nell'articolo del connettore -> sezione Proprietà attività di copia**. | No       |

### <a name="parquet-as-sink"></a>Parquet come sink

Nell'attività di copia sono supportate le proprietà seguenti ***\*sink\**** sezione.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata su **ParquetSink**. | Yes      |
| storeSettings | Un gruppo di proprietà relativa alla scrittura dei dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportati in `storeSettings`. **Vedere i dettagli nell'articolo del connettore -> sezione Proprietà attività di copia**. | No       |

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Informazioni dettagliate dal [trasformazione sorgente](data-flow-source.md) e [sink trasformazione](data-flow-sink.md) nel Mapping di flusso di dati.

## <a name="data-type-support"></a>Supporto dei tipi di dati

Parquet tipi di dati complessi non sono attualmente supportati (ad esempio, MAP, elenco, uno STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Tramite il Runtime di integrazione self-hosted

> [!IMPORTANT]
> Per le copie attivate dal runtime di integrazione self-hosted, ad esempio tra l'archivio dati locale e quello nel cloud, se non si esegue una copia **identica** dei file Parquet, è necessario installare **JRE 8 (Java Runtime Environment) a 64 bit o OpenJDK** nel computer del runtime di integrazione. Per informazioni più dettagliate, vedere il paragrafo seguente.

Per la copia in esecuzione nel runtime di integrazione self-hosted con la serializzazione/deserializzazione dei file Parquet, il file di definizione dell'applicazione (AFD) individua il runtime Java eseguendo prima una ricerca di JRE nel Registro di sistema *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* . In caso di esito negativo, esegue una ricerca di OpenJDK nella variabile di sistema *`JAVA_HOME`* .

- **Per usare JRE**: il runtime di integrazione a 64 bit richiede JRE a 64 bit disponibile [qui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Per usare OpenJDK**: è supportato a partire dalla versione 3.13 del runtime di integrazione. Includere jvm.dll in un pacchetto con tutti gli altri assembly necessari di OpenJDK nel computer del runtime di integrazione self-hosted e impostare di conseguenza la variabile di ambiente di sistema JAVA_HOME.

> [!TIP]
> Se si copiano i dati nel/dal formato Parquet usando il runtime di integrazione self-hosted e si verifica l'errore "An error occurred when invoking java, message: **java.lang.OutOfMemoryError:Java heap space**" (Errore durante la chiamata di Java, messaggio: java.lang.OutOfMemoryError: spazio dell'heap di Java), è possibile aggiungere una variabile di ambiente `_JAVA_OPTIONS` nel computer che ospita il runtime di integrazione self-hosted per regolare le dimensioni min/max dell'heap per JVM e poter ottimizzare la copia, quindi eseguire di nuovo la pipeline.

![Impostare le dimensioni dell'heap JVM nel runtime di integrazione self-hosted](C:/AzureContent/azure-docs-pr/articles/data-factory/media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Esempio: impostare la variabile `_JAVA_OPTIONS` con il valore `-Xms256m -Xmx16g`. Il flag `Xms` specifica il pool di allocazione della memoria iniziale per Java Virtual Machine (JVM), mentre `Xmx` specifica il pool di allocazione della memoria massima. JVM verrà quindi avviato con una quantità di memoria pari a `Xms` e potrà usare una quantità massima di memoria pari a `Xmx`. Per impostazione predefinita, Azure Data Factory usa una quantità di memoria minima pari a 64 MB e una quantità di memoria massima pari a 1 GB.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping di flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)