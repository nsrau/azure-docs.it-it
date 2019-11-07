---
title: Formato ORC in Azure Data Factory
description: In questo argomento viene descritto come gestire il formato ORC in Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: a5125b53d960ddead063435666de5b26ce0bc291
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674754"
---
# <a name="orc-format-in-azure-data-factory"></a>Formato ORC in Azure Data Factory

Seguire questo articolo quando si desidera **analizzare i file ORC o scrivere i dati in formato ORC**. 

Il formato ORC è supportato per i connettori seguenti: [Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Archiviazione](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati ORC.

| Proprietà         | Descrizione                                                  | Obbligatorio |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **ORC**. | Sì      |
| location         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location`. **Per informazioni dettagliate, vedere l'articolo connettore-> sezione Proprietà set di dati**. | Sì      |

Di seguito è riportato un esempio di set di dati ORC nell'archivio BLOB di Azure:

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink ORC.

### <a name="orc-as-source"></a>ORC come origine

Le proprietà seguenti sono supportate nella sezione ***\*origine\**** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatorio |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **OrcSource**. | Sì      |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings`. **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

### <a name="orc-as-sink"></a>ORCO come sink

Le proprietà seguenti sono supportate nella sezione ***\*sink\**** dell'attività di copia.

| Proprietà      | Descrizione                                                  | Obbligatorio |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **OrcSink**. | Sì      |
| storeSettings | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate in `storeSettings`. **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

## <a name="using-self-hosted-integration-runtime"></a>Uso di Integration Runtime self-hosted

> [!IMPORTANT]
> Per le copie attivate dal runtime di integrazione self-hosted, ad esempio tra l'archivio dati locale e quello nel cloud, se non si esegue una copia **identica** dei file ORC, è necessario installare **JRE 8 (Java Runtime Environment) a 64 bit o OpenJDK** nel computer del runtime di integrazione. Per informazioni più dettagliate, vedere il paragrafo seguente.

Per la copia in esecuzione nel runtime di integrazione self-hosted con la serializzazione/deserializzazione dei file ORC, il file di definizione dell'applicazione (AFD) individua il runtime Java eseguendo prima una ricerca di JRE nel Registro di sistema *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* . In caso di esito negativo, esegue una ricerca di OpenJDK nella variabile di sistema *`JAVA_HOME`* .

- **Per usare JRE**: il runtime di integrazione a 64 bit richiede jre a 64 bit. disponibile [qui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Per usare OpenJDK**: è supportato a partire dalla versione 3.13 del runtime di integrazione. Includere jvm.dll in un pacchetto con tutti gli altri assembly necessari di OpenJDK nel computer del runtime di integrazione self-hosted e impostare di conseguenza la variabile di ambiente di sistema JAVA_HOME.

> [!TIP]
> Se si copiano dati da e verso il formato ORC usando Integration Runtime indipendenti e si verifica un errore indicante che si è verificato un errore durante la chiamata a Java, messaggio: **java. lang. OutOfMemoryError: spazio heap Java**, è possibile aggiungere una variabile di ambiente `_JAVA_OPTIONS` nel computer che ospita il runtime di integrazione self-hosted per modificare le dimensioni dell'heap min/max per JVM per consentire tale copia, quindi eseguire di nuovo la pipeline.

![Impostare le dimensioni dell'heap JVM nel runtime di integrazione self-hosted](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Esempio: impostare la variabile `_JAVA_OPTIONS` con il valore `-Xms256m -Xmx16g`. Il flag `Xms` specifica il pool di allocazione della memoria iniziale per Java Virtual Machine (JVM), mentre `Xmx` specifica il pool di allocazione della memoria massima. JVM verrà quindi avviato con una quantità di memoria pari a `Xms` e potrà usare una quantità massima di memoria pari a `Xmx`. Per impostazione predefinita, Azure Data Factory usa una quantità di memoria minima pari a 64 MB e una quantità di memoria massima pari a 1 GB.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
