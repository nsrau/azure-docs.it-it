---
title: Formato ORC in Azure Data Factory
description: Questo argomento descrive come gestire il formato ORC in Azure Data Factory.This topic describes how to deal with ORC format in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: e104c4c8e976207859b75212d5406558f04c6377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597491"
---
# <a name="orc-format-in-azure-data-factory"></a>Formato ORC in Azure Data Factory

Seguire questo articolo quando si desidera **analizzare i file ORC o scrivere i dati in formato ORC**. 

Il formato ORC è supportato per i connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), Azure Data Lake Storage [Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Archiviazione file di Azure](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di](concepts-datasets-linked-services.md) dati. In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati ORC.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà type del dataset deve essere impostata su **Orc**. | Sì      |
| posizione         | Impostazioni di posizione dei file. Ogni connettore basato su file ha `location`il proprio tipo di posizione e le proprietà supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione delle proprietà del set di dati**. | Sì      |

Di seguito è riportato un esempio di set di dati ORC in Archiviazione BLOB di Azure:Below is an example of ORC dataset on Azure Blob Storage:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Tenere presente quanto segue:

* I tipi di dati complessi non sono supportati (STRUCT, MAP, LIST, UNION).
* Spazi vuoti nel nome della colonna non sono supportati.
* Il file ORC dispone di tre [opzioni relative alla compressione](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Data Factory supporta la lettura dei dati dal file ORC in uno di questi formati compressi. Per leggere i dati, Data Factoy usa la compressione codec dei metadati. Tuttavia, durante la scrittura in un file ORC, Data Factory sceglie ZLIB che è il valore predefinito per ORC. Al momento non esiste alcuna opzione per ignorare tale comportamento.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione viene fornito un elenco delle proprietà supportate dall'origine ORC e dal sink.

### <a name="orc-as-source"></a>ORC come fonte

Le proprietà seguenti sono supportate nella sezione *** \*relativa all'origine\* *** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata su **OrcSource**. | Sì      |
| StoreImpostazioni | Gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di lettura supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione Delle proprietà dell'attività Copy**. | No       |

### <a name="orc-as-sink"></a>ORC come lavandino

Le proprietà seguenti sono supportate nella sezione del *** \*sink\* *** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà type dell'origine dell'attività di copia deve essere impostata su **OrcSink**. | Sì      |
| StoreImpostazioni | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file `storeSettings`dispone di impostazioni di scrittura supportate in . **Vedere i dettagli nell'articolo del connettore -> sezione Delle proprietà dell'attività Copy**. | No       |

## <a name="using-self-hosted-integration-runtime"></a>Utilizzo del runtime di integrazione self-hostedUsing Self-hosted Integration Runtime

> [!IMPORTANT]
> Per la copia consentita dal runtime di integrazione self-hosted, ad esempio tra archivi dati locali e cloud, se non si copiano file ORC così **come sono,** è necessario installare il pacchetto ridistribuibile **A 64 bit JRE 8 (Java Runtime Environment) o OpenJDK** e **Microsoft Visual C. 2010 Redistributable Package** nel computer a raggi- per mano. Controlla il paragrafo seguente con ulteriori dettagli.

Per la copia in esecuzione su Runtime di runtime self-hosted con serializzazione/deserializzazione *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* dei file ORC, ADF individua *`JAVA_HOME`* il runtime Java controllando innanzitutto il Registro di sistema per JRE, se non viene trovato, in secondo luogo controllando la variabile di sistema per OpenJDK.

- **Per utilizzare JRE**: l'IR a 64 bit richiede JRE a 64 bit. disponibile [qui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Per utilizzare OpenJDK**: È supportato dalla versione IR 3.13. Includere jvm.dll in un pacchetto con tutti gli altri assembly necessari di OpenJDK nel computer del runtime di integrazione self-hosted e impostare di conseguenza la variabile di ambiente di sistema JAVA_HOME.
- **Per installare il pacchetto ridistribuibile di Visual C, 2010**: il pacchetto ridistribuibile di Visual Cè 2010 non viene installato con le installazioni a distribuzione a distribuzione automatica. disponibile [qui](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Se si copiano dati in/dal formato ORC utilizzando Self-hosted Integration Runtime e si colpisce l'errore "Si è verificato un errore durante `_JAVA_OPTIONS` la chiamata di java, messaggio: **java.lang.OutOfMemoryError:Java heap space**", è possibile aggiungere una variabile di ambiente nel computer che ospita il runtime di integrazione self-hosted per regolare la dimensione dell'heap min/max per JVM per consentire tale copia, quindi eseguire nuovamente la pipeline.

![Impostare le dimensioni dell'heap JVM nel runtime di integrazione self-hosted](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Esempio: impostare la variabile `_JAVA_OPTIONS` con il valore `-Xms256m -Xmx16g`. Il flag `Xms` specifica il pool di allocazione della memoria iniziale per Java Virtual Machine (JVM), mentre `Xmx` specifica il pool di allocazione della memoria massima. JVM verrà quindi avviato con una quantità di memoria pari a `Xms` e potrà usare una quantità massima di memoria pari a `Xmx`. Per impostazione predefinita, ADF utilizza min 64 MB e max 1G.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
