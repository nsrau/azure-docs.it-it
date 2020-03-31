---
title: Formato parquet in Azure Data Factory
description: Questo argomento descrive come gestire il formato Parquet in Azure Data Factory.This topic describes how to deal with Parquet format in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 81bbd476cea0472647ca183fb188fc13725d1469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597627"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet in Azure Data Factory

Seguire questo articolo quando si desidera **analizzare i file Diquet o scrivere i dati in formato Parquet**. 

Il formato di parquet è supportato per i seguenti connettori: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), Azure Data Lake Storage [Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Archiviazione file di Azure](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di](concepts-datasets-linked-services.md) dati. In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati Parquet.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà type del set di dati deve essere impostata su **Parquet**. | Sì      |
| posizione         | Impostazioni di posizione dei file. Ogni connettore basato su file ha `location`il proprio tipo di posizione e le proprietà supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione delle proprietà del set di dati**. | Sì      |
| compressionCodec (compressionCodec) | Il codec di compressione da utilizzare per la scrittura in file Parquet. Durante la lettura dai file di parquet, Data Factories determina automaticamente il codec di compressione in base ai metadati del file.<br>I tipi supportati sono "**none**", "**gzip**", "**snappy**" (predefinito) e "**lzo**". Nota: attualmente l'attività di copia non supporta L-O quando i file di parquet di lettura/scrittura. | No       |

> [!NOTE]
> Lo spazio vuoto nel nome della colonna non è supportato per i file Parquet.

Di seguito è riportato un esempio di set di dati di parquet in Archiviazione BLOB di Azure:Below is an example of Parquet dataset on Azure Blob Storage:

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione fornisce un elenco delle proprietà supportate dalla sorgente e dal sink del parquet.

### <a name="parquet-as-source"></a>Parquet come fonte

Le proprietà seguenti sono supportate nella sezione *** \*relativa all'origine\* *** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata su **ParquetSource**. | Sì      |
| StoreImpostazioni | Gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di lettura supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione Delle proprietà dell'attività Copy**. | No       |

### <a name="parquet-as-sink"></a>Parquet come lavandino

Le proprietà seguenti sono supportate nella sezione del *** \*sink\* *** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata su **ParquetSink**. | Sì      |
| StoreImpostazioni | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di scrittura supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione Delle proprietà dell'attività Copy**. | No       |

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Informazioni dettagliate sulla [trasformazione dell'origine](data-flow-source.md) e sulla [trasformazione sink](data-flow-sink.md) nel mapping del flusso di dati.

## <a name="data-type-support"></a>Supporto dei tipi di dati

I tipi di dati complessi del parquet non sono attualmente supportati (ad esempio MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Utilizzo del runtime di integrazione self-hostedUsing Self-hosted Integration Runtime

> [!IMPORTANT]
> Per la copia potenziata dal runtime di integrazione self-hosted, ad esempio tra archivi dati locali e cloud, se non si copiano i file Parquet così **come sono,** è necessario installare il pacchetto ridistribuibile **OpenJDK** e **Microsoft Visual C. 2010** a 64 bit nel computer a raggi- per mano. Controlla il paragrafo seguente con ulteriori dettagli.

Per la copia in esecuzione su Runtime di runtime self-hosted con serializzazione/deserializzazione *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* dei file Parquet, ADF individua *`JAVA_HOME`* il runtime Java controllando innanzitutto il Registro di sistema per JRE, se non viene trovato, in secondo luogo controllando la variabile di sistema per OpenJDK.

- **Per utilizzare JRE**: l'IR a 64 bit richiede JRE a 64 bit. disponibile [qui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Per utilizzare OpenJDK**: È supportato dalla versione IR 3.13. Includere jvm.dll in un pacchetto con tutti gli altri assembly necessari di OpenJDK nel computer del runtime di integrazione self-hosted e impostare di conseguenza la variabile di ambiente di sistema JAVA_HOME.
- **Per installare il pacchetto ridistribuibile di Visual C, 2010**: il pacchetto ridistribuibile di Visual Cè 2010 non viene installato con le installazioni a distribuzione a distribuzione automatica. disponibile [qui](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Se si copiano i dati nel/dal formato Parquet usando il runtime di integrazione self-hosted e si verifica l'errore "An error occurred when invoking java, message: **java.lang.OutOfMemoryError:Java heap space**" (Errore durante la chiamata di Java, messaggio: java.lang.OutOfMemoryError: spazio dell'heap di Java), è possibile aggiungere una variabile di ambiente `_JAVA_OPTIONS` nel computer che ospita il runtime di integrazione self-hosted per regolare le dimensioni min/max dell'heap per JVM e poter ottimizzare la copia, quindi eseguire di nuovo la pipeline.

![Impostare le dimensioni dell'heap JVM nel runtime di integrazione self-hosted](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Esempio: impostare la variabile `_JAVA_OPTIONS` con il valore `-Xms256m -Xmx16g`. Il flag `Xms` specifica il pool di allocazione della memoria iniziale per Java Virtual Machine (JVM), mentre `Xmx` specifica il pool di allocazione della memoria massima. JVM verrà quindi avviato con una quantità di memoria pari a `Xms` e potrà usare una quantità massima di memoria pari a `Xmx`. Per impostazione predefinita, ADF utilizza min 64 MB e max 1G.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
