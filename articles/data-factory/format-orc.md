---
title: Formato ORC in Azure Data Factory
description: In questo argomento viene descritto come gestire il formato ORC in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 9b68d3724c6390fc5d30745924451e27ef9855b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81417726"
---
# <a name="orc-format-in-azure-data-factory"></a>Formato ORC in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Seguire questo articolo quando si desidera **analizzare i file ORC o scrivere i dati in formato ORC**. 

Il formato ORC è supportato per i connettori seguenti [: Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati ORC.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | La proprietà Type del set di dati deve essere impostata su **ORC**. | Sì      |
| posizione         | Impostazioni del percorso dei file. Ogni connettore basato su file ha un tipo di percorso e proprietà supportate in `location` . **Per informazioni dettagliate, vedere l'articolo connettore-> sezione Proprietà set di dati**. | Sì      |

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

Nella sezione *** \* origine \* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **OrcSource**. | Sì      |
| storeSettings | Un gruppo di proprietà su come leggere i dati da un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di lettura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

### <a name="orc-as-sink"></a>ORCO come sink

Nella sezione *** \* sink \* *** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **OrcSink**. | Sì      |
| storeSettings | Gruppo di proprietà su come scrivere dati in un archivio dati. Ogni connettore basato su file ha le proprie impostazioni di scrittura supportate in `storeSettings` . **Per informazioni dettagliate, vedere l'articolo connettore > sezione proprietà dell'attività di copia**. | No       |

## <a name="using-self-hosted-integration-runtime"></a>Uso di Integration Runtime self-hosted

> [!IMPORTANT]
> Per la copia abilitata da self-hosted Integration Runtime ad esempio tra archivi dati locali e cloud, se non si esegue la copia dei file ORC **così come sono**, è necessario installare il pacchetto **64-bit JRE 8 (Java Runtime Environment) o OpenJDK** e **Microsoft Visual C++ 2010 ridistribuibile** nel computer IR. Per altri dettagli, vedere il paragrafo seguente.

Per la copia in esecuzione sul runtime di integrazione self-hosted con la serializzazione/deserializzazione dei file ORC, ADF individua il runtime Java controllando prima di tutto il registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* di sistema per JRE, se non è stato trovato, controllando in secondo luogo la variabile *`JAVA_HOME`* di sistema per OpenJDK.

- **Per usare JRE**: il runtime di integrazione a 64 bit richiede jre a 64 bit. disponibile [qui](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Per usare OpenJDK**: è supportata dalla versione 3,13 di IR. Includere jvm.dll in un pacchetto con tutti gli altri assembly necessari di OpenJDK nel computer del runtime di integrazione self-hosted e impostare di conseguenza la variabile di ambiente di sistema JAVA_HOME.
- **Per installare Visual C++ pacchetto ridistribuibile 2010**: Visual C++ 2010 Redistributable Package non è installato con installazioni di runtime di integrazione self-hosted. disponibile [qui](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Se si copiano dati da e verso il formato ORC usando Integration Runtime indipendenti e si verifica un errore indicante che si è verificato un errore durante la chiamata a Java, messaggio: **java. lang. OutOfMemoryError: spazio heap Java**, è possibile aggiungere una variabile di ambiente `_JAVA_OPTIONS` nel computer che ospita il runtime di integrazione self-hosted per modificare le dimensioni dell'heap min/max per JVM in modo da consentire la

![Impostare le dimensioni dell'heap JVM nel runtime di integrazione self-hosted](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Esempio: impostare la variabile `_JAVA_OPTIONS` con il valore `-Xms256m -Xmx16g`. Il flag `Xms` specifica il pool di allocazione della memoria iniziale per Java Virtual Machine (JVM), mentre `Xmx` specifica il pool di allocazione della memoria massima. JVM verrà quindi avviato con una quantità di memoria pari a `Xms` e potrà usare una quantità massima di memoria pari a `Xmx`. Per impostazione predefinita, ADF USA min 64 MB e Max 1G.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)
