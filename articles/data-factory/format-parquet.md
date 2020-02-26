---
title: Formato parquet in Azure Data Factory
description: In questo argomento viene descritto come gestire il formato parquet in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 81bbd476cea0472647ca183fb188fc13725d1469
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597627"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formato parquet in Azure Data Factory

Seguire questo articolo quando si desidera **analizzare i file parquet o scrivere i dati in formato parquet**. 

Il formato parquet è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati parquet.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **parquet**. | Sì      |
| posizione         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location`. **Per informazioni dettagliate, vedere l'articolo connettore-> sezione Proprietà set di dati**. | Sì      |
| compressionCodec | Codec di compressione da usare durante la scrittura in file parquet. Durante la lettura da file parquet, Data Factory determina automaticamente il codec di compressione basato sui metadati del file.<br>I tipi supportati sono "**None**", "**gzip**", "**Snapper**" (impostazione predefinita) e "**LZO**". Nota Attualmente l'attività di copia non supporta LZO quando si leggono/scrivono file parquet. | No       |

> [!NOTE]
> Lo spazio vuoto nel nome della colonna non è supportato per i file parquet.

Di seguito è riportato un esempio di set di dati parquet nell'archivio BLOB di Azure:

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink parquet.

### <a name="parquet-as-source"></a>Parquet come origine

Le proprietà seguenti sono supportate nella sezione ***\*origine\**** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **ParquetSource**. | Sì      |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings`. **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

### <a name="parquet-as-sink"></a>Parquet come sink

Le proprietà seguenti sono supportate nella sezione ***\*sink\**** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **ParquetSink**. | Sì      |
| storeSettings | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate in `storeSettings`. **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Per informazioni dettagliate, vedere [trasformazione origine](data-flow-source.md) e [trasformazione sink](data-flow-sink.md) nel flusso di dati del mapping.

## <a name="data-type-support"></a>Supporto dei tipi di dati

I tipi di dati parquet complessi non sono attualmente supportati (ad esempio, MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Uso di Integration Runtime self-hosted

> [!IMPORTANT]
> Per la copia abilitata da self-hosted Integration Runtime ad esempio tra archivi dati locali e cloud, se non si copiano i file parquet **così come sono**, è necessario installare il **64 bit JRE 8 (Java Runtime Environment) o OpenJDK** e **Microsoft Visual C++ 2010 Redistributable Package** nel computer IR. Per altri dettagli, vedere il paragrafo seguente.

Per la copia in esecuzione nel runtime di integrazione self-hosted con la serializzazione/deserializzazione dei file Parquet, il file di definizione dell'applicazione (AFD) individua il runtime Java eseguendo prima una ricerca di JRE nel Registro di sistema *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* . In caso di esito negativo, esegue una ricerca di OpenJDK nella variabile di sistema *`JAVA_HOME`* .

- **Per usare JRE**: il runtime di integrazione a 64 bit richiede jre a 64 bit. disponibile [qui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Per usare OpenJDK**: è supportata dalla versione 3,13 di IR. Includere jvm.dll in un pacchetto con tutti gli altri assembly necessari di OpenJDK nel computer del runtime di integrazione self-hosted e impostare di conseguenza la variabile di ambiente di sistema JAVA_HOME.
- **Per installare Visual C++ 2010 Redistributable Package**: Visual C++ 2010 Redistributable Package non è installato con installazioni di runtime di integrazione self-hosted. disponibile [qui](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Se si copiano i dati nel/dal formato Parquet usando il runtime di integrazione self-hosted e si verifica l'errore "An error occurred when invoking java, message: **java.lang.OutOfMemoryError:Java heap space**" (Errore durante la chiamata di Java, messaggio: java.lang.OutOfMemoryError: spazio dell'heap di Java), è possibile aggiungere una variabile di ambiente `_JAVA_OPTIONS` nel computer che ospita il runtime di integrazione self-hosted per regolare le dimensioni min/max dell'heap per JVM e poter ottimizzare la copia, quindi eseguire di nuovo la pipeline.

![Impostare le dimensioni dell'heap JVM nel runtime di integrazione self-hosted](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Esempio: impostare la variabile `_JAVA_OPTIONS` con il valore `-Xms256m -Xmx16g`. Il flag `Xms` specifica il pool di allocazione della memoria iniziale per Java Virtual Machine (JVM), mentre `Xmx` specifica il pool di allocazione della memoria massima. JVM verrà quindi avviato con una quantità di memoria pari a `Xms` e potrà usare una quantità massima di memoria pari a `Xmx`. Per impostazione predefinita, ADF USA min 64 MB e Max 1G.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
